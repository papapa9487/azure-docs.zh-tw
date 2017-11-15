---
title: "在 Azure 中建立 Linux Service Fabric 叢集 | Microsoft Docs"
description: "了解如何使用 Azure CLI 將 Linux Service Fabric 叢集部署到現有的 Azure 虛擬網路。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 33a3474ed91194efbaf2ef96957ad268f43a717e
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>將 Service Fabric Linux 叢集部署到 Azure 虛擬網路
本教學課程是一個系列的第一部分。 您將會了解如何使用 Azure CLI 將 Linux Service Fabric 叢集部署到現有的 Azure 虛擬網路 (VNET) 和子網路。 完成時，您會有在您可以部署應用程式的雲端中執行的叢集。 若要使用 PowerShell 建立 Windows 叢集，請參閱[在 Azure 上建立安全的 Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在 Azure 中使用 Azure CLI 建立 VNET
> * 在 Azure 中使用 Azure CLI 建立安全的 Service Fabric 叢集
> * 使用 X.509 憑證保護叢集
> * 使用 Service Fabric CLI 連接到叢集
> * 刪除叢集

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * 在 Azure 上建立安全叢集
> * [使用 Service Fabric 部署 API 管理](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前：
- 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安裝 [Service Fabric CLI](service-fabric-cli.md)
- 安裝 [Azure CLI 2.0](/cli/azure/install-azure-cli)

下列程序會建立五個節點的 Service Fabric 叢集。 若要計算在 Azure 中執行 Service Fabric 叢集產生的成本，請使用 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)。

## <a name="sign-in-to-azure-and-select-your-subscription"></a>登入 Azure 並選取您的訂用帳戶
本指南使用 Azure CLI。 開始新的工作階段時，請先登入您的 Azure 帳戶並選取您的訂用帳戶，再執行 Azure 命令。
 
執行下列指令碼，以登入您的 Azure 帳戶並選取您的訂用帳戶：

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>建立資源群組
針對您的部署建立新的資源群組，並指定名稱和位置。

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>部署網路拓撲
接下來，設定將作為 API 管理與 Service Fabric 叢集部署位置的網路拓撲。 [Network.json][network-arm] Resource Manager 範本已設定來建立虛擬網路 (VNET)，同時也會建立適用於 Service Fabric 的子網路與網路安全性群組 (NSG) 以及適用於 API 管理的子網路和 NSG。 在[這裡](../virtual-network/virtual-networks-overview.md)深入了解 VNET、子網路與 NSG。

[Network.parameters.json][network-parameters-arm] 參數檔包含要作為 Service Fabric 與 API 管理部署位置之子網路和 NSG 的名稱。  [下一個教學課程](service-fabric-tutorial-deploy-api-management.md)會部署 API 管理。 就這份指南而言，參數值無須變更。 Service Fabric Resource Manager 範本會使用這些值。  如果在此處修改這些值，您也必須在本教學課程和[部署 API 管理教學課程](service-fabric-tutorial-deploy-api-management.md)所使用的其他 Resource Manager 範本中修改這些值。 

下載下列 Resource Manager 範本和參數檔：
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

使用下列指令碼來部署用於網路設定的 Resource Manager 範本和參數檔：

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>部署 Service Fabric 叢集
部署完網路資源之後，下一個步驟是將 Service Fabric 叢集部署至為 Service Fabric 叢集指定之子網路與 NSG 中的 VNET。 將叢集部署至現有的 VNET 和子網路 (先前已在本文中部署) 需要 Resource Manager 範本。  如需詳細資訊，請參閱[使用 Azure Resource Manager 建立叢集](service-fabric-cluster-creation-via-arm.md)。 本教學課程系列已將範本預先設定為使用您在上一個步驟中設定之 VNET、子網路及 NSG 的名稱。  

下載下列 Resource Manager 範本和參數檔：
- [linuxcluster.json][cluster-arm]
- [linuxcluster.parameters.json][cluster-parameters-arm]

使用此範本建立安全叢集。  叢集憑證是用來保護節點對節點通訊並向管理用戶端驗證叢集管理端點的 X.509 憑證。  該憑證也會為 HTTPS 管理 API 及透過 HTTPS 使用的 Service Fabric Explorer 提供 SSL。 Azure 金鑰保存庫可用來管理 Azure 中 Service Fabric 叢集的憑證。  在 Azure 中部署叢集時，負責建立 Service Fabric 叢集的 Azure 資源提供者會從金鑰保存庫提取憑證，並將它們安裝在叢集 VM 上。 

您可以使用憑證授權單位 (CA) 的憑證作為叢集憑證，或對於測試目的，建立自我簽署憑證。 叢集憑證必須︰

- 包含私密金鑰。
- 是為了進行金鑰交換而建立，且可匯出成個人資訊交換 (.pfx) 檔案。
- 有與您用來存取 Service Fabric 叢集的網域相符的主體名稱。 必須如此符合，才能為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 SSL。 您無法從憑證授權單位 (CA) 取得 .cloudapp.azure.com 網域的 SSL 憑證。 您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須與用於您叢集的自訂網域名稱相符。

對於您的部署，在 *linuxcluster.parameters.json* 檔案中填入這些空白參數：

|參數|值|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|

將 **certificateThumbprint**、**certificateUrlValue** 和 **sourceVaultValue** 參數保留空白，以建立自我簽署憑證。  如果您要使用先前上傳至金鑰保存庫的憑證，請填入那些參數值。

下列指令碼會使用 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) 命令和範本，在 Azure 中部署新的叢集。 此 Cmdlet 也會在 Azure 中建立新的金鑰保存庫、將新的自我簽署憑證新增至金鑰保存庫，並將憑證檔案下載至本機。 您可以使用 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) 命令的其他參數，指定現有的憑證及/或金鑰保存庫。

```azurecli
Password="q6D7nN%6ck@6"
Subject="mysfcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>連線到安全的叢集
使用您的金鑰，利用 Service Fabric CLI `sfctl cluster select` 命令連接到叢集。  請注意，只能針對自我簽署憑證使用 **--no-verify** 選項。

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

使用 `sfctl cluster health` 命令來檢查您連接的叢集是否狀況良好。

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>清除資源
本教學課程系列的其他文章會使用您剛才建立的叢集。 如果您現在不打算繼續閱讀下一篇文章，您可能要刪除該叢集以避免產生費用。 刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。

登入 Azure 並選取您要移除叢集的訂用帳戶識別碼。  您可以登入[Azure 入口網站](http://portal.azure.com)找到您的訂用帳戶識別碼。 使用 [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) 命令來刪除資源群組和所有叢集資源。

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure 中使用 Azure CLI 建立 VNET
> * 在 Azure 中使用 Azure CLI 建立安全的 Service Fabric 叢集
> * 使用 X.509 憑證保護叢集
> * 使用 Service Fabric CLI 連接到叢集
> * 刪除叢集

接下來，前進到下列的教學課程，了解如何調整叢集。
> [!div class="nextstepaction"]
> [調整叢集](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
