---
title: "將 OpenShift Origin 部署至 Azure | Microsoft Docs"
description: "了解如何將 OpenShift Origin 部署至 Azure 虛擬機器。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jbinder
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: jbinder
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>將 OpenShift Origin 部署至 Azure 虛擬機器 

[OpenShift Origin](https://www.openshift.org/) 是建置在 [Kubernetes](https://kubernetes.io/) 上的開放原始碼容器平台。 它可簡化部署、調整及操作多租用戶應用程式的程序。 

本指南說明如何使用 Azure CLI 和 Azure Resource Manager 範本在 Azure 虛擬機器上部署 OpenShift Origin。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 KeyVault 來管理 OpenShift 叢集的 SSH 金鑰。
> * 部署 Azure VM 上的 OpenShift 叢集。 
> * 安裝和設定 [OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) 來管理叢集。
> * 自訂 OpenShift 部署。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

本快速入門需要 Azure CLI 2.0.8 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>登入 Azure 
使用 [az login](/cli/azure/#login) 命令來登入 Azure 訂用帳戶並遵循畫面上的指示進行，或按一下 [試用] 來使用 Cloud Shell。

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫
使用 [az keyvault create](/cli/azure/keyvault#create) 命令來建立要儲存叢集之 SSH 金鑰的 KeyVault。  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>建立 SSH 金鑰 
需要 SSH 金鑰才能安全存取 OpenShift Origin 叢集。 使用 `ssh-keygen` 命令建立 SSH 金鑰組。 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 您所建立的 SSH 金鑰組不能使用複雜密碼。

如需有關 Windows 上的 SSH 金鑰詳細資訊，請參閱[如何在 Windows 上建立 SSH 金鑰](/azure/virtual-machines/linux/ssh-from-windows)。

## <a name="store-ssh-private-key-in-key-vault"></a>將 SSH 私密金鑰儲存在 Key Vault 中
OpenShift 部署會使用您建立的 SSH 金鑰來安全存取 OpenShift 主機。 若要啟用部署以安全地擷取 SSH 金鑰，請使用下列命令將金鑰儲存在 Key Vault。

# <a name="enabled-for-template-deployment"></a>已針對範本部署啟用
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>建立服務主體 
OpenShift 會使用使用者名稱與密碼或服務主體與 Azure 進行通訊。 Azure 服務主體是安全性識別，可供您與應用程式、服務及諸如 OpenShift 等自動化工具搭配使用。 您可以控制和定義對於服務主體可以在 Azure 中執行哪些作業的權限。 為了提高只提供使用者名稱和密碼的安全性，此範例會建立基本的服務主體。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 建立服務主體，並將 OpenShift 所需的認證輸出：

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {strong password} \
          --scopes $(az group show --name myResourceGroup --query id)
```
記下命令傳回的 appId 屬性。
```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > 請勿建立不安全的密碼。  請依照 [Azure AD 密碼規則和限制](/azure/active-directory/active-directory-passwords-policy)指引。

如需有關服務主體的詳細資訊，請參閱[使用 Azure CLI 2.0 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="deploy-the-openshift-origin-template"></a>部署 OpenShift Origin 範本
接下來，使用 Azure Resource Manager 範本來部署 Web 應用程式。 

> [!NOTE] 
> 下列命令需要 az CLI 2.0.8 或更新版本。 您可以使用 `az --version` 命令來確認 az CLI 版本。 若要更新 CLI 版本，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

使用您稍早針對 `aadClientId` 參數所建立之服務主體中的 `appId` 值。

```azurecli 
az group deployment create --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --params \ 
        openshiftMasterPublicIpDnsLabel=myopenshiftmaster \
        infraLbPublicIpDnsLabel=myopenshiftlb \
        openshiftPassword=Pass@word!
        sshPublicKey=~/.ssh/openshift_rsa.pub \
        keyVaultResourceGroup=myResourceGroup \
        keyVaultName=myKeyVault \
        keyVaultSecret=OpenShiftKey \
        aadClientId={appId} \
        aadClientSecret={strong password} 
```
完成部署最多可能需要大約 20 分鐘或更久的時間。 當部署完成時，OpenShift 主控台的 URL 和 OpenShift 主機的 DNS 名稱會列印到終端機。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>連線到 OpenShift 叢集
部署完成時，使用 `OpenShift Console Uri` 可透過瀏覽器連線至 OpenShift 主控台。 或者，您也可以使用下列命令連線至 OpenShift 主機。

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清除資源
若不再需要，您可以使用 [az group delete](/cli/azure/group#delete) 命令將資源群組、OpenShift 叢集和所有相關資源移除。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：
> [!div class="checklist"]
> * 建立 KeyVault 來管理 OpenShift 叢集的 SSH 金鑰。
> * 部署 Azure VM 上的 OpenShift 叢集。 
> * 安裝和設定 [OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) 來管理叢集。

現在您已部署 OpenShift Origin 叢集。 您可以遵循 OpenShift 教學課程，了解如何部署第一個應用程式，以及如何使用 OpenShift 工具。 若要開始使用，請參閱[開始使用 OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)。 
