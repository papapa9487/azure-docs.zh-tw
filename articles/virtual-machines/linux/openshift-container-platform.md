---
title: "在 Azure 中部署 OpenShift 容器平台 | Microsoft Docs"
description: "在 Azure 中部署 OpenShift 容器平台。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: c91b7232b2f87e0b4b5e659126b96a6ef8b4202c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>在 Azure 中部署 OpenShift 容器平台

有多種方式可以在 Azure 中部署 OpenShift 容器平台。 您可以手動部署所需的所有 Azure 基礎結構元件，然後依照 OpenShift 容器平台[文件](https://docs.openshift.com/container-platform/3.6/welcome/index.html) \(英文\) 進行。
您也可以使用能夠簡化 OpenShift 容器平台叢集部署的現有 Resource Manager 範本。 一旦這類範本位於[這裡](https://github.com/Microsoft/openshift-container-platform/) \(英文\)。

另一個選項是使用 [Azure Marketplace 供應項目](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview) \(英文\)。

對於兩個選項，都需要有 Red Hat 訂用帳戶。 在部署期間，RHEL 執行個體會向 Red Hat 訂用帳戶進行，並且附加至包含 OpenShift 容器平台之權利的集區識別碼。
請確定您具備有效的 Red Hat 訂用帳戶管理員使用者名稱、密碼和集區識別碼 (RHSM 使用者名稱、RHSM 密碼和集區識別碼)。 您可以登入 https://access.redhat.com 來驗證資訊。

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>使用 OpenShift 容器平台 Resource Manager 範本部署

若要使用 Resource Manager 範本部署，請使用參數檔案來提供所有輸入參數。 如果您想要使用輸入參數自訂任何未涵蓋的部署項目，分叉 Github 儲存機制，並變更適當的項目。

某些常見的自訂選項包括 (但不限於)：

- VNet CIDR [azuredeploy.json 中的變數]
- 防禦 VM 大小 [azuredeploy.json 中的變數]
- 命名慣例 [azuredeploy.json 中的變數]
- OpenShift 叢集詳細規格 - 已透過主機檔案 [deployOpenShift.sh] 修改

### <a name="configure-parameters-file"></a>設定參數檔案

使用您稍早針對 `aadClientId` 參數所建立之服務主體中的 `appId` 值。 

下列範例會建立名為 **azuredeploy.parameters.json**，且具有所有必要輸入的參數檔案。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

將以 {} 括住的項目取代為您的相關資訊。

### <a name="deploy-using-azure-cli"></a>使用 Azure CLI 部署

> [!NOTE] 
> 下列命令需要 Azure CLI 2.0.8 或更新版本。 您可以使用 `az --version` 命令來確認 az CLI 版本。 若要更新 CLI 版本，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

下列範例會使用 myOpenShiftCluster 的部署名稱，將 OpenShift 叢集和所有相關的資源部署到名稱為 myResourceGroup 的資源群組。 範本直接參考自 Github 儲存機制，而且會使用名為 **azuredeploy.parameters.json** 檔案的本機參數檔案。

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

根據部署的節點總數，部署需要至少 30 分鐘才能完成。 當部署完成時，OpenShift 主控台的 URL 和 OpenShift 主機的 DNS 名稱會列印到終端機。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-using-openshift-container-platform-marketplace-offer"></a>使用 OpenShift 容器平台 Marketplace 供應項目部署

將 OpenShift 容器平台部署至 Azure 最簡單的方式是，使用 [Azure Marketplace 供應項目](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview) \(英文\)。

此選項是最簡單的一個，但是自訂功能也受到限制。 此供應項目包含三個組態選項：

- 小型：部署包含一個主要節點、一個基礎結構節點、兩個應用程式節點，以及一個防禦節點的非 HA 叢集。 所有節點都是標準的 DS2v2 VM 大小。 此叢集需要總共 10 個核心，而且非常適用於小規模測試。
- 中型：部署包含三個主要節點、兩個基礎結構節點、四個應用程式節點，以及一個防禦節點的 HA 叢集。 除了防禦節點以外的所有節點都是標準的 DS3v2 VM 大小。 防禦節點是標準的 DS2v2。 此叢集需要 38 個核心。
- 大型：部署包含三個主要節點、兩個基礎結構節點、六個應用程式節點，以及一個防禦節點的 HA 叢集。 主要節點和基礎結構節點都是標準的 DS3v2 VM 大小、應用程式節點是標準的 DS4v2 VM 大小，而防禦節點是標準的 DS2v2。 此叢集需要 70 個核心。

Azure 雲端提供者的組態對於中型和大型叢集大小而言是選擇性的。 小型叢集大小並不會提供設定 Azure 雲端提供者的選項。

## <a name="connect-to-the-openshift-cluster"></a>連線到 OpenShift 叢集

部署完成時，使用 `OpenShift Console Uri` 可透過瀏覽器連線至 OpenShift 主控台。 或者，您可以使用下列命令，連線至 OpenShift 主機：

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#delete) 命令將資源群組、OpenShift 叢集和所有相關資源移除。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

- [部署後工作](./openshift-post-deployment.md)
- [OpenShift 部署疑難排解](./openshift-troubleshooting.md)
- [開始使用 OpenShift 容器平台](https://docs.openshift.com/container-platform/3.6/getting_started/index.html) \(英文\)
