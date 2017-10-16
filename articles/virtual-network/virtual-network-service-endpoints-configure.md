---
title: "設定 Azure 虛擬網路服務端點 | Microsoft Docs"
description: "了解如何從虛擬網路啟用和停用服務端點"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: 9c953e697d5d1c6e53df8eaf72c4e4fbd83ca286
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-virtual-network-service-endpoints"></a>設定虛擬網路服務端點

虛擬網路 (VNet) 服務端點可讓您將 Azure 服務資源固定在 Azure 虛擬網路上，讓您完全不必透過網際網路就能存取這些資源。 服務端點可讓您從虛擬網路直接連線到 Azure 服務，從而讓您使用 VNet 的私人位址空間來存取 Azure 服務。 透過服務端點預定流向 Azure 服務的流量，一律會留在 Microsoft Azure 骨幹網路中。 請深入了解[虛擬網路服務端點](virtual-network-service-endpoints-overview.md)

本文提供用來啟用和停用服務端點的步驟。 Azure 服務的子網路上啟用了端點後，您就可以將特定服務資源固定在虛擬網路上。

使用 [Azure 入口網站](#azure-portal)、[Azure PowerShell](#azure-powershell)、[Azure 命令列介面](#azure-cli)或 Azure Resource Manager [範本](#resource-manager-template) 就能設定服務端點。

>[!NOTE]
在預覽期間，特定區域會支援 VNet 服務端點功能。 如需支援區域的清單，請參閱 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network)頁面。

## <a name="service-endpoint-configuration-overview"></a>服務端點設定概觀

- 只有透過 Azure Resource Manager 部署模型所部署的 VNet 才能設定服務端點。

- 服務端點會設定在 VNet 的每個子網路上。

- 在子網路中，每項服務只能設定一個服務端點。 您可以對不同服務 (例如 Azure 儲存體、Azure SQL) 設定多個服務端點。

- 您可以在新的或現有的子網路上啟用端點。

- 系統會自動設定端點的位置。 根據預設，系統會將服務端點設定為 VNet 的區域。 如果是 Azure 儲存體，為了支援區域性容錯移轉案例，系統會自動將端點設定為 [Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)。

  >[!NOTE]
  根據 VNet/子網路的大小，啟用服務端點時可能需要一些時間才能完成。 在啟用服務端點時，請確定您沒有在進行重要的工作。 服務端點會在子網路的每個 NIC 上切換路線，並可能會終止任何已開啟的 TCP 連線。 

- 當流往子網路所有 NIC 上之服務的流量已切換為 VNet 私人 IP 位址之後，服務端點呼叫就會傳回「成功」。

- 可供確認端點設定的有效路由：

   為了確認系統是否已正確設定服務端點，子網路任何 NIC 上的「有效路由」會針對每個區域的每項服務顯示新的「預設」路由，且 nextHopType 為 VirtualNetworkServiceEndpoint。 請深入了解[有效路由的疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   只有在設定了一或多個網路介面 (NIC) 並將 NIC 與子網路中正在執行的虛擬機器相關聯時，才能檢視有效路由。

## <a name="azure-portal"></a>Azure 入口網站

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>在 VNet 建立期間於子網路上設定服務端點

1. 開啟 [Azure 入口網站](https://portal.azure.com/)。
使用 Azure 帳戶登入 Azure。 如果您沒有 Azure 帳戶，您可以註冊免費試用。 該帳戶必須擁有必要的[權限](#provisioning)，以便建立虛擬網路和服務端點。
2. 按一下 [+新增] > [網路] > [虛擬網路] > [+新增]。
3. 在 建立虛擬網路 中輸入下列值，然後按一下建立：

設定 | 值
------- | -----
名稱    | myVnet
位址空間 | 10.0.0.0/16
子網路名稱|mySubnet
子網路位址範圍|10.0.0.0/24
資源群組|讓 [新建] 保持選取狀態，然後輸入名稱。
位置|任何支援的區域，例如澳大利亞東部
訂用帳戶|選取您的訂用帳戶。
__ServiceEndpoints__|已啟用
__服務__ | 選取其中一個可用的服務或全部選取。 預覽期間所支援的服務：__"Microsoft.Storage"、"Microsoft.Sql"__。

選取端點的服務：![選取服務端點的服務](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. 確認所有設定都正確無誤，然後按一下 [建立]。

![設定服務端點](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. 若要完成將 Azure 服務資源固定在 VNet 的作業，請按一下[後續步驟](#Next%20Steps)中的服務文件。


### <a name="validating-service-endpoint-configuration"></a>確認服務端點設定

使用下列步驟確認服務端點已完成設定：

- 在資源中按一下 [虛擬網路]。 搜尋 VNet。
- 按一下 VNet 名稱，並瀏覽至 [服務端點]
- 已設定的端點會顯示為「成功」。 您也可以看到自動設定的位置

![確認服務端點設定](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>可供確認端點設定的有效路由

若要檢視子網路中的網路介面 (NIC) 上的有效路由，請按一下該子網路中的任何 NIC。 在 [支援 + 疑難排解] 下方，按一下 [有效路由]。 如果端點已完成設定，您會看到新的「預設」路由，其以服務的位址首碼作為目的地，且 nextHopType 為 "VirtualNetworkServiceEndpoint"。

![服務端點的有效路由](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>為 VNet 中的現有子網路設定服務端點

1. 在資源中按一下 [虛擬網路]，然後搜尋任何現有 VNet
2. 按一下 VNet 名稱，並瀏覽至 [服務端點]
3. 按一下 [新增]。 選取 [服務]。 您一次只能對一個服務建立端點。 
4. 選取您想要套用端點的所有子網路。 按一下 [新增]

![子網路服務端點設定](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>刪除服務端點
1. 在資源中按一下 [虛擬網路]。 篩選 VNet 名稱以搜尋現有的 VNet。
2. 按一下 VNet 名稱，並瀏覽至 [服務端點]
3. 按一下服務名稱，然後以滑鼠右鍵按一下服務端點項目
4. 選取 [刪除]

![服務端點刪除](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure Powershell

安裝的先決條件：

- 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 若要啟動 PowerShell 工作階段，請移至 開始，輸入 **powershell**，然後按一下PowerShell。
- 在 PowerShell 中，輸入 `login-azurermaccount` 命令來登入 Azure。 該帳戶必須擁有必要的[權限](#provisioning)，以便建立虛擬網路和服務端點。

### <a name="get-available-service-endpoints-for-azure-region"></a>取得 Azure 區域可用的服務端點

使用以下命令來取得某個 Azure 區域之端點的支援服務清單。
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

輸出： 
名稱 | ID | 類型
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>在建立虛擬網路 myVNet 時將 Azure 儲存體服務端點新增至子網路 mySubnet

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
您可以使用以逗號分隔的服務名稱清單來啟用多項服務。
範例："Microsoft.Storage", "Microsoft.Sql"

預期的輸出：
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

若要完成將 Azure 服務資源固定在 VNet 的作業，請按一下[後續步驟](#Next%20Steps)中的服務文件。

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>在現有子網路中新增多個服務端點

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

預期的輸出： 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>檢視子網路上所設定的服務端點

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
輸出：
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>刪除子網路上的服務端點
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Azure CLI

安裝的先決條件：
- 使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 如需有關登入的詳細資訊，請參閱[開始使用 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。
 - 該帳戶必須擁有必要的[權限](#provisioning)，以便建立虛擬網路和服務端點。

 如需完整的虛擬網路命令清單，請參閱 [Azure CLI 虛擬網路命令](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)

### <a name="get-available-service-endpoints-for-azure-region"></a>取得 Azure 區域可用的服務端點

使用以下命令來取得某個 Azure 區域 (例如 "EastUS") 之端點的支援服務清單。
```azure-cli
az network vnet list-endpoint-services -l eastus
```
輸出：
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>在建立虛擬網路 myVNet 時將 Azure 儲存體服務端點新增至子網路 mySubnet

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

若要新增多個端點：--service-endpoints Microsoft.Storage Microsoft.Sql

輸出：
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

若要完成將 Azure 服務資源固定在 VNet 的作業，請按一下[後續步驟](#Next%20Steps)中的服務文件。

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>在現有子網路中新增多個服務端點

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

預期的輸出：
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>檢視子網路上所設定的服務端點

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>刪除子網路上的服務端點
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

輸出： 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Resource Manager 範本

### <a name="securing-azure-service-resources-to-vnets"></a>將 Azure 服務資源固定在 VNet 上

您可以透過服務端點將特定的 Azure 資源固定在虛擬網路上。

請下載 [Resource Manager 範本](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)範例以將儲存體帳戶固定在 VNet 中的子網路上。

此範本會建立擁有 2 個子網路的 VNet，以及每個子網路中各有一個 NIC 的 VM。 在一個子網路上啟用端點，並將儲存體帳戶固定在該子網路上。

您可以下載範本，並進行部分修改以符合您的情況。

範本會提供使用 Azure 入口網站、PowerShell 或 Azure CLI 來部署範本的指示。 請確定您有必要的[權限](#provisioning)以設定端點和固定帳戶。

若要將 Azure 資源固定在子網路上：

- 請在該子網路上設定服務端點。
- 請在資源上新增虛擬網路規則以將資源固定在 VNet 上。

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>將資源已固定在子網路上的服務端點刪除
如果 Azure 服務資源已固定在子網路上，而您刪除了服務端點，您就無法再從子網路存取資源。
只重新啟用端點並無法讓您重新能夠存取先前已固定在子網路上的資源。

若要將服務資源重新固定在這個子網路上，您需要：

- 重新啟用端點
- 移除資源上的舊 VNet 規則
- 新增規則以將資源固定在子網路上

## <a name="provisioning"></a>佈建

擁有虛擬網路寫入權限的使用者可以任意地在虛擬網路上設定服務端點。

若要將 Azure 服務資源放到 VNet 保護，使用者必須擁有所要新增之子網路的 "Microsoft.Network/JoinServicetoaSubnet" 權限。 此權限預設會隨附在內建的服務管理員角色中，可藉由建立自訂角色加以修改。

深入了解[內建角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)以及如何將特定權限指派給[自訂角色](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)。

VNet 和 Azure 服務資源不一定要位於相同訂用帳戶中。 如果這兩者位於不同的訂用帳戶中，則在此預覽期間，資源應該位於相同的 Active Directory (AD) 租用戶底下。

## <a name="next-steps"></a>後續步驟

如需如何將服務資源固定在 VNet 的詳細指示，請參閱下列連結：

[將 Azure 儲存體帳戶固定在虛擬網路上](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[將 Azure SQL 固定在虛擬網路上](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
