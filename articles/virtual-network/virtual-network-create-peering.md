---
title: "建立 Azure 虛擬網路對等互連 - Resource Manager - 相同訂用帳戶 | Microsoft Docs"
description: "了解如何在透過 Resource Manager 建立且存在於相同 Azure 訂用帳戶的虛擬網路之間，建立虛擬網路對等互連。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: ab62164c85ece30181217a36a51d19fda52907bc
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>建立虛擬網路對等互連 - Resource Manager，相同訂用帳戶

在本教學課程中，您會了解如何在透過 Resource Manager 建立的虛擬網路之間，建立虛擬網路對等互連。 這兩個虛擬網路均存在於相同的訂用帳戶中。 對等互連兩個虛擬網路，可讓不同虛擬網路中的資源彼此通訊，且通訊時會有相同的頻寬和延遲，彷彿這些資源是位於相同的虛擬網路中。 深入了解[虛擬網路對等互連](virtual-network-peering-overview.md)。 

建立虛擬網路對等互連的步驟會因一些因素而有所不同，這取決於虛擬網路是位於相同還是不同的訂用帳戶中，以及是透過哪一個 [Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)建立虛擬網路。 請按一下下表中的案例，以了解如何在其他案例中建立虛擬網路對等互連：

|Azure 部署模型  | Azure 訂閱  |
|--------- |---------|
|[兩者皆使用 Resource Manager](create-peering-different-subscriptions.md) |不同|
|[一個使用 Resource Manager、一個使用傳統部署模型](create-peering-different-deployment-models.md) |相同|
|[一個使用 Resource Manager、一個使用傳統部署模型](create-peering-different-deployment-models-subscriptions.md) |不同|

虛擬網路對等互連無法在透過傳統部署模型建立的兩個虛擬網路之間建立。 如果您需要將兩個都是透過傳統部署模型建立的虛擬網路連接，可以使用 Azure [VPN 閘道](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)來連接這些虛擬網路。 

此教學課程將同一個區域中的虛擬網路視為對等。 將不同區域中的虛擬網路視為對等的功能目前為預覽狀態。 完成[註冊通用虛擬網路對等互連](#register)中的步驟，然後再嘗試將不同區域中的虛擬網路視為對等，否則對等互連會失敗。 一般可以使用透過 Azure [VPN 閘道](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)連接不同區域中的虛擬網路，而且不需要註冊。

您可以使用 [Azure 入口網站](#portal)、Azure [命令列介面](#cli) (CLI)、Azure [PowerShell](#powershell) 或 [Azure Resource Manager 範本](#template)，來建立虛擬網路對等互連。 按一下任何先前的工具連結，直接前往使用您所選工具建立虛擬網路對等互連的步驟。

## <a name="portal"></a>建立對等互連 - Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
2. 依序按一下 [新增]、[網路] 及 [虛擬網路]。
3. 在 [建立虛擬網路] 刀鋒視窗上，輸入或選取下列設定的值，然後按一下 [建立]：
    - **名稱**：*myVnet1*
    - **位址空間**：*10.0.0.0/16*
    - **子網路名稱**：*預設值*
    - **子網路位址範圍**：*10.0.0.0/24*
    - **訂用帳戶**︰選取您的訂用帳戶
    - **資源群組**：選取 [新建] 並輸入 *myResourceGroup*
    - **位置**：*美國東部*
4. 再次完成步驟 2-3 並在步驟 3 中指定下列值：
    - **名稱**： *myVnet2*
    - **位址空間**：*10.1.0.0/16*
    - **子網路名稱**：*預設值*
    - **子網路位址範圍**：*10.1.0.0/24*
    - **訂用帳戶**︰選取您的訂用帳戶
    - **資源群組**：選取 [使用現有] 並選取 *myResourceGroup*
    - **位置**：*美國東部*
5. 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myResourceGroup*。 當搜尋結果中出現 **MyResourceGroup** 時，按一下該項目。 **myresourcegroup** 資源群組的刀鋒視窗隨即出現。 資源群組包含在前述步驟中建立的兩個虛擬網路。
6. 按一下 [myVNet1]。
7. 在顯示的 [myVnet1] 刀鋒視窗，從刀鋒視窗左側的垂直選項清單中按一下 [對等互連]。
8. 在顯示的 [myVnet1 - 對等互連] 刀鋒視窗中，按一下 [+ 新增]
9. 在顯示的 [新增對等互連] 刀鋒視窗中，輸入或選取下列選項，然後按一下 [確定]：
     - **名稱**：*myVnet1ToMyVnet2*
     - **虛擬網路部署模型**：選取 [Resource Manager]。 
     - **訂用帳戶**︰選取您的訂用帳戶
     - **虛擬網路**：按一下 [選擇虛擬網路]，然後按一下 [myVnet2]。
     - **允許虛擬網路存取**：確定已選取 [啟用]。
    本教學課程中不會使用其他設定。 若要了解所有對等互連設定，請閱讀[管理虛擬網路對等互連](virtual-network-manage-peering.md#create-a-peering)。
10. 按一下上一個步驟中的 [確定] 後，[新增對等互連] 刀鋒視窗隨即關閉，而且您會再次看到 [myVnet1 - 對等互連] 刀鋒視窗。 幾秒之後，您建立的對等互連會出現在刀鋒視窗中。 您建立之 **myVnet1ToMyVnet2** 對等互連的 [對等互連狀態] 資料行中列出 [已起始]。 您已將 Vnet1 對等互連到 Vnet2，但現在必須將 myVnet2 對等互連到 myVnet1。 必須以建立雙線的對等互連，虛擬網路中的資源才能彼此通訊。
11. 針對 myVnet2 再次完成步驟 5-10。 將對等互連命名為 *myVnet2ToMyVnet1*。
12. 按一下 [確定] 來建立 MyVnet2 的對等互連幾秒之後，您剛建立之 **myVnet2ToMyVnet1** 對等互連的 [對等互連狀態] 資料行中會列出 [已連線]。
13. 針對 MyVnet1 再次完成步驟 5-7。 **myVnet1ToVNet2** 對等互連的 [對等互連狀態] 現在也是 [已連線]。 您在對等互連中兩個虛擬網路的 [對等互連狀態] 資料行中看到 [已連線] 之後，對等互連變已建立成功。
14. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
15. **選擇性︰**若要刪除您在本教學課程中建立的資源，請完成本文之[刪除資源](#delete-portal)一節中的步驟。

您在任何一個虛擬網路中建立的任何 Azure 資源現在能夠透過其 IP 位址彼此通訊。 如果您使用虛擬網路的預設 Azure 名稱解析，則虛擬網路中的資源無法跨虛擬網路解析名稱。 如果您想要跨對等互連中的虛擬網路解析名稱，您必須建立自己的 DNS 伺服器。 了解如何設定[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="cli"></a>建立對等互連 - Azure CLI

下列指令碼：

- 需要 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 `az --version` 命令。 如果您需要升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 

您可以不安裝 CLI 及其相依項目，而是改用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 按一下以下指令碼中的 [試試看] 按鈕，這會叫用可讓您登入 Azure 帳戶的 Cloud Shell。 若要執行指令碼，請按一下 [Copy] \(複製\) 按鈕，然後將內容貼到您的 Cloud Shell。

1. 建立一個資源群組和兩個虛擬網路。

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. 在兩個虛擬網路之間建立虛擬網路對等互連。
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. 在指令碼執行後，檢閱每個虛擬網路的對等互連。 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    再次執行前一個命令，以 *myVnet2* 取代 *myVnet1*。 兩個命令的輸出會在 [對等互連] 資料行中顯示 [已連線]。

     您在任何一個虛擬網路中建立的任何 Azure 資源現在能夠透過其 IP 位址彼此通訊。 如果您使用虛擬網路的預設 Azure 名稱解析，則虛擬網路中的資源無法跨虛擬網路解析名稱。 如果您想要跨對等互連中的虛擬網路解析名稱，您必須建立自己的 DNS 伺服器。 了解如何設定[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

4. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
5. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-cli)中的步驟。


## <a name="powershell"></a>建立對等互連 - PowerShell

1. 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 若要啟動 PowerShell 工作階段，請移至 [開始]，輸入 **powershell**，然後按一下 [PowerShell]。
3. 在 PowerShell 中，輸入 `login-azurermaccount` 命令來登入 Azure。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
4. 建立一個資源群組和兩個虛擬網路。 若要執行指令碼，請複製下列指令碼、將它貼到 PowerShell，然後當螢幕上出現最後一行之後按 `Enter`：

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. 在兩個虛擬網路之間建立虛擬網路對等互連。 請複製下列指令碼、將它貼到 PowerShell，然後當螢幕上出現最後一行之後按 `Enter`：
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. 若要檢閱虛擬網路的子網路，請複製下列命令、貼到 PowerShell 視窗，然後按 `Enter`：

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    再次執行前一個命令，以 *myVnet2* 取代 *myVnet1*。 兩個命令的輸出會在 [對等互連] 資料行中顯示 [已連線]。
7. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
8. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-powershell)中的步驟。

您在任何一個虛擬網路中建立的任何 Azure 資源現在能夠透過其 IP 位址彼此通訊。 如果您使用虛擬網路的預設 Azure 名稱解析，則虛擬網路中的資源無法跨虛擬網路解析名稱。 如果您想要跨對等互連中的虛擬網路解析名稱，您必須建立自己的 DNS 伺服器。 了解如何設定[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="template"></a>建立對等互連 - Resource Manager 範本

1. 參考[建立虛擬網路對等互連](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) Resource Manager 範本。 範本會提供使用 Azure 入口網站、PowerShell 或 Azure CLI 來部署範本的指示。 登入您選擇的工具，以使用具備建立虛擬網路對等互連之必要權限的帳戶來部署範本。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
2. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
3. **選擇性︰**若要刪除您在本教學課程中建立的資源，請使用 Azure 入口網站、PowerShell 或 Azure CLI 來完成本文之[刪除資源](#delete)一節中的步驟。

## <a name="permissions"></a>權限

您用於建立虛擬網路對等互連的帳戶必須具有必要的角色或權限。 例如，如果您要將兩個虛擬網路 (分別名為 VNet1 和 VNet2) 對等互連，您的帳戶必須針對每個虛擬網路獲得下列最基本的角色或權限：
    
|虛擬網路|角色|權限|
|---|---|---|
|VNet1|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

深入了解[內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及如何對[自訂角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)指派特定權限 (僅限 Resource Manager)。

## <a name="delete"></a>刪除資源
當您完成本教學課程時，您可能會想刪除您在教學課程中建立的資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。

### <a name="delete-portal"></a>Azure 入口網站

1. 在入口網站的搜尋方塊中，輸入 **myResourceGroup**。 在搜尋結果中按一下 [myResourceGroup]。
2. 在 [myResourceGroup] 刀鋒視窗中，按一下 [刪除] 圖示。
3. 若要確認刪除動作，請在 [輸入資源群組名稱] 方塊中輸入 **myResourceGroup**，然後按一下 [刪除]。

### <a name="delete-cli"></a>Azure CLI

輸入下列命令：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

輸入下列命令：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

## <a name="register"></a>註冊可獲得虛擬網路對等互連全球預覽版

將不同區域中的虛擬網路視為對等的功能目前為預覽狀態。 僅有限的區域 (初期為美國中西部、加拿大中部和美國西部 2) 可使用此功能。 在不同區域中的虛擬網路之間建立的虛擬網路對等互連，可能無法達到與同一個區域中的虛擬網路之間的對等互連相同程度的可用性和可靠性。 如需此功能可用性和狀態的最新通知，請查看 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network) 頁面。

若要使各區域的虛擬網路對等互連，您必須使用 Azure PowerShell 或 Azure CLI 完成下列步驟 (在您想要對等互連之虛擬網路適用的訂用帳戶內)，先註冊預覽：

### <a name="powershell"></a>PowerShell

1. 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 啟動 PowerShell 工作階段，然後使用 `Login-AzureRmAccount` 命令來登入 Azure。
3. 輸入下列命令，對於在您想要對等互連之每個虛擬網路適用的訂用帳戶註冊預覽：

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. 藉由輸入下列命令，確認您已註冊預覽版︰

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    請等到您為兩個訂用帳戶輸入上述命令後所收到的 **RegistrationState** 輸出都變成 **Registered** 之後，才完成本文中入口網站、Azure CLI、PowerShell 或 Resource Manager 範本等節中的步驟。

### <a name="azure-cli"></a>Azure CLI

1. [安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)。
2. 輸入 `az --version` 命令來確定您使用 2.0.18 版或更高版本的 Azure CLI。 如果不是，請安裝最新版本。
3. 使用 `az login` 命令登入 Azure。
4. 輸入下列命令來註冊預覽版︰

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. 藉由輸入下列命令，確認您已註冊預覽版︰

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    請等到您為兩個訂用帳戶輸入上述命令後所收到的 **RegistrationState** 輸出都變成 **Registered** 之後，才完成本文中入口網站、Azure CLI、PowerShell 或 Resource Manager 範本等節中的步驟。

## <a name="next-steps"></a>後續步驟

- 請先徹底熟悉重要[虛擬網路對等互連的條件約束和行為](virtual-network-manage-peering.md#requirements-and-constraints)，再建立虛擬網路對等互連以供生產環境使用。
- 了解所有[虛擬網路對等互連設定](virtual-network-manage-peering.md#create-a-peering)。
- 了解如何透過虛擬網路對等互連來[建立中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。
