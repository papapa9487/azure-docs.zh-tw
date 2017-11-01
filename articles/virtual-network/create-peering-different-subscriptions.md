---
title: "建立 Azure 虛擬網路對等互連 - Resource Manager - 不同訂用帳戶 | Microsoft Docs"
description: "了解如何在透過 Resource Manager 建立且存在於不同 Azure 訂用帳戶中的虛擬網路之間，建立虛擬網路對等互連。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 43dffa5a095545499b8cc4989af746a6290e4cf7
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2017
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>建立虛擬網路對等互連 - Resource Manager，不同訂用帳戶 

在本教學課程中，您會了解如何在透過 Resource Manager 建立的虛擬網路之間，建立虛擬網路對等互連。 這些虛擬網路存在於不同的訂用帳戶中。 對等互連兩個虛擬網路，可讓不同虛擬網路中的資源彼此通訊，且通訊時會有相同的頻寬和延遲，彷彿這些資源是位於相同的虛擬網路中。 深入了解[虛擬網路對等互連](virtual-network-peering-overview.md)。 

建立虛擬網路對等互連的步驟會因一些因素而有所不同，這取決於虛擬網路是位於相同還是不同的訂用帳戶中，以及是透過哪一個 [Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)建立虛擬網路。 請按一下下表中的案例，以了解如何在其他案例中建立虛擬網路對等互連：

|Azure 部署模型  | Azure 訂閱  |
|--------- |---------|
|[兩者皆使用 Resource Manager](virtual-network-create-peering.md) |相同|
|[一個使用 Resource Manager、一個使用傳統部署模型](create-peering-different-deployment-models.md) |相同|
|[一個使用 Resource Manager、一個使用傳統部署模型](create-peering-different-deployment-models-subscriptions.md) |不同|

只能在存在於相同 Azure 區域中的兩個虛擬網路之間建立虛擬網路對等互連。

  > [!WARNING]
  > 在不同地區的虛擬網路之間建立虛擬網路對等互連的功能，目前只提供預覽版。 在下方註冊訂用帳戶可以獲得預覽版。 在此情況下建立的虛擬網路對等互連，與在正式發行版情況下建立的虛擬網路對等互連，可能不會有相同等級的可用性和可靠性。 在此情況下建立的虛擬網路對等互連不受支援、可能功能受限，且可能並非在所有 Azure 區域中都可供使用。 如需此功能可用性和狀態的最新通知，請查看 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network) 頁面。

虛擬網路對等互連無法在透過傳統部署模型建立的兩個虛擬網路之間建立。 如果您需要將兩個都是透過傳統部署模型建立的虛擬網路連接，或是將存在於不同 Azure 區域中的虛擬網路連接，可以使用 Azure [VPN 閘道](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)來連接這些虛擬網路。 

您可以使用 [Azure 入口網站](#portal)、Azure [命令列介面](#cli) (CLI)、Azure [PowerShell](#powershell) 或 [Azure Resource Manager 範本](#template)，來建立虛擬網路對等互連。 按一下任何先前的工具連結，直接前往使用您所選工具建立虛擬網路對等互連的步驟。

## <a name="register"></a>註冊可獲得 VNet 對等互連全球預覽版

若要跨地區建立虛擬網路的對等互連，請註冊以取得預覽版。請為包含您想要建立對等互連之虛擬網路的兩個訂用帳戶，執行下列步驟。 您唯一可用來註冊預覽版的工具是 PowerShell。

1. 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 啟動 PowerShell 工作階段，然後使用 `Login-AzureRmAccount` 命令來登入 Azure。
3. 輸入下列命令來針對預覽版註冊您的訂用帳戶︰

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
    請等到您為兩個訂用帳戶輸入下列命令後所收到的 **RegistrationState** 輸出都變成 **Registered** 之後，才完成本文中入口網站、Azure CLI 或 PowerShell 等節中的步驟：

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```
  > [!WARNING]
  > 在不同地區的虛擬網路之間建立虛擬網路對等互連的功能，目前只提供預覽版。 用預覽版建立之虛擬網路對等互連的功能可能有限，而且可能只有部分 Azure 區域能進行對等互連。 如需此功能可用性和狀態的最新通知，請查看 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network) 頁面。

## <a name="portal"></a>建立對等互連 - Azure 入口網站

本教學課程針對每個訂用帳戶使用不同的帳戶。 如果您使用對兩個訂用帳戶都有權限的帳戶，便可以使用該相同帳戶來進行所有步驟、略過登出入口網站的步驟，以及略過指派另一位使用者權限給虛擬網路的步驟。

1. 以 UserA 身分登入 [Azure 入口網站](https://portal.azure.com)。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
2. 依序按一下 [新增]、[網路] 及 [虛擬網路]。
3. 在 [建立虛擬網路] 刀鋒視窗上，輸入或選取下列設定的值，然後按一下 [建立]：
    - **名稱**：*myVnetA*
    - **位址空間**：*10.0.0.0/16*
    - **子網路名稱**：*預設值*
    - **子網路位址範圍**：*10.0.0.0/24*
    - **訂用帳戶**︰選取訂用帳戶 A。
    - **資源群組**：選取 [建立新項目]，然後輸入 *myResourceGroupA*
    - **位置**：*美國東部*
4. 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myVnetA*。 當 myVnetA 出現在搜尋結果中時，按一下 [myVnetA]。 隨即會顯示 [myVnetA] 虛擬網路刀鋒視窗。
5. 在顯示的 [myVnetA] 刀鋒視窗中，從刀鋒視窗左側的垂直選項清單中按一下 [存取控制 (IAM)]。
6. 在顯示的 [myVnetA - 存取控制 (IAM)] 刀鋒視窗中，按一下 [+ 新增]。
7. 在顯示的 [新增權限] 刀鋒視窗中，選取 [角色] 方塊中的 [網路參與者]。
8. 在 [選取] 方塊中，選取 [UserB]，或輸入 UserB 的電子郵件地址來搜尋它。 顯示的使用者清單來自與您設定對等互連之虛擬網路相同的 Azure Active Directory 租用戶。
9. 按一下 [儲存] 。
10. 在 [myVnetA - 存取控制 (IAM)] 刀鋒視窗中，從刀鋒視窗左側的垂直選項清單中按一下 [屬性]。 複製 [資源識別碼]，在稍後的步驟中將會用到此識別碼。 資源識別碼與以下範例類似：/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA。
11. 以 UserA 身分登出入口網站，然後以 UserB 身分登入。
12. 完成步驟 2 到 3，其中在步驟 3 中輸入或選取下列值：

    - **名稱**：*myVnetB*
    - **位址空間**：*10.1.0.0/16*
    - **子網路名稱**：*預設值*
    - **子網路位址範圍**：*10.1.0.0/24*
    - **訂用帳戶**︰選取訂用帳戶 B。
    - **資源群組**：選取 [建立新項目]，然後輸入 *myResourceGroupB*
    - **位置**：*美國東部*

13. 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myVnetB*。 當 myVnetB 出現在搜尋結果中時，按一下 [myVnetB]。 隨即會顯示 [myVnetB] 虛擬網路刀鋒視窗。
14. 在顯示的 [myVnetB] 刀鋒視窗中，從刀鋒視窗左側的垂直選項清單中按一下 [屬性]。 複製 [資源識別碼]，在稍後的步驟中將會用到此識別碼。 資源識別碼與以下範例類似：/subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB。
15. 按一下 [myVnetB] 刀鋒視窗中的 [存取控制 (IAM)]，然後針對 myVnetB 完成步驟 5 到 10，其中在步驟 8 中輸入 **UserA**。
16. 以 UserB 身分登出入口網站，然後以 UserA 身分登入。
17. 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myVnetA*。 當 myVnetA 出現在搜尋結果中時，按一下 [myVnetA]。 隨即會顯示 [myVnet] 虛擬網路刀鋒視窗。
18. 按一下 [myVnetA]。
19. 在顯示的 [myVnetA] 刀鋒視窗中，從刀鋒視窗左側的垂直選項清單中按一下 [對等]。
20. 在顯示的 [myVnetA - 對等互連] 刀鋒視窗中，按一下 [+ 新增]
21. 在顯示的 [新增對等互連] 刀鋒視窗中，輸入或選取下列選項，然後按一下 [確定]：
     - **名稱**：*myVnetAToMyVnetB*
     - **虛擬網路部署模型**：選取 [Resource Manager]。
     - **我知道我的資源識別碼**：核取此方塊。
     - **資源識別碼**：輸入來自步驟 14 的資源識別碼。
     - **允許虛擬網路存取**：確定已選取 [啟用]。
    本教學課程中不會使用其他設定。 若要了解所有對等互連設定，請閱讀[管理虛擬網路對等互連](virtual-network-manage-peering.md#create-a-peering)。
22. 在上一個步驟中按一下 [確定] 之後，[新增對等互連] 刀鋒視窗隨就會關閉，而您則會再次看到 [myVnetA - 對等] 刀鋒視窗。 幾秒之後，您建立的對等互連會出現在刀鋒視窗中。 您所建立之 **myVnetAToMyVnetB** 對等互連的 [對等互連狀態] 資料行中會列出 [已起始]。 您已將 myVnetA 對等互連到 myVnetB，但現在必須將 myVnetB 對等互連到 myVnetA。 必須以建立雙線的對等互連，虛擬網路中的資源才能彼此通訊。
23. 以 UserA 身分登出入口網站，然後以 UserB 身分登入。
24. 針對 myVnetB 完成步驟 17 到 21。 在步驟 21 中，將對等互連命名為 *myVnetBToMyVnetA*，針對 [虛擬網路] 選取 [myVnetA]，然後在 [資源識別碼] 方塊中輸入來自步驟 10 的識別碼。
25. 按一下 [確定] 來建立 myVnetB 的對等互連幾秒之後，您剛建立之 **myVnetBToMyVnetA** 對等互連的 [對等互連狀態] 資料行中就會列出 [已連線]。
26. 以 UserB 身分登出入口網站，然後以 UserA 身分登入。
27. 再次完成步驟 17 到 19。 **myVnetAToVNetB** 對等互連的 [對等互連狀態] 現在也是 [已連線]。 您在對等互連中兩個虛擬網路的 [對等互連狀態] 資料行中看到 [已連線] 之後，對等互連變已建立成功。 您在任何一個虛擬網路中建立的任何 Azure 資源現在能夠透過其 IP 位址彼此通訊。 如果您使用虛擬網路的預設 Azure 名稱解析，則虛擬網路中的資源無法跨虛擬網路解析名稱。 如果您想要跨對等互連中的虛擬網路解析名稱，您必須建立自己的 DNS 伺服器。 了解如何設定[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。
28. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
29. **選擇性︰**若要刪除您在本教學課程中建立的資源，請完成本文之[刪除資源](#delete-portal)一節中的步驟。

## <a name="cli"></a>建立對等互連 - Azure CLI

本教學課程針對每個訂用帳戶使用不同的帳戶。 如果您使用對兩個訂用帳戶都有權限的帳戶，便可以使用該相同帳戶來進行所有步驟、略過登出 Azure 的步驟，以及移除建立使用者角色指派項目的指令碼行。 請使用您要用於 UserA 和 UserB 的使用者名稱來取代下列指令碼中的 UserA@azure.com 和 UserB@azure.com。

下列指令碼：

- 需要 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 

您可以不安裝 CLI 及其相依項目，而是改用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 按一下以下指令碼中的 [試試看] 按鈕，這會叫用可讓您登入 Azure 帳戶的 Cloud Shell。 

1. 開啟 CLI 工作階段，然後使用 `azure login` 命令來以 UserA 身分登入 Azure。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
2. 將下列指令碼複製到您電腦上的文字編輯器中，使用 SubscriptionA 的 ID 來取代 `<SubscriptionA-Id>`，接著複製修改過的指令碼並貼到您的 CLI 工作階段中，然後按 `Enter`。 如果您不知道您的訂用帳戶 ID，請輸入 'az account show' 命令。 輸出中的 **id** 值就是您的訂用帳戶 ID。

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```
    
3. 使用 `az logout` 命令來以 UserA 身分登出 Azure，然後以 UserB 身分登入 Azure。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
4. 建立 myVnetB。 將步驟 2 中的指令碼內容複製到您電腦上的文字編輯器中。 使用 SubscriptionB 的 ID 來取代 `<SubscriptionA-Id>`。 將 10.0.0.0/16 變更為 10.1.0.0/16、將所有 A 變更為 B，以及將所有 B 變更為 A。複製修改過的指令碼並貼到您的 CLI 工作階段中，然後按 `Enter`。 
5. 以 UserB 身分登出 Azure，然後以 UserA 身分登入 Azure。
6. 建立從 myVnetA 到 myVnetB 的虛擬網路對等互連。 將下列指令碼內容複製到您電腦上的文字編輯器中。 使用 SubscriptionB 的 ID 來取代 `<SubscriptionB-Id>`。 若要執行指令碼，請複製修改過的指令碼並貼到您的 CLI 工作階段中，然後按 Enter。
 
    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)
    
        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. 檢視 myVnetA 的對等互連狀態。

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    狀態為 **Initiated**。 在您建立從 myVnetB 到 myVnetA 的對等互連之後，它就會變更為 **Connected**。

8. 將 UserA 登出 Azure，然後以 UserB 身分登入 Azure。
9. 建立從 myVnetB 到 myVnetA 的對等互連。 將步驟 6 中的指令碼內容複製到您電腦上的文字編輯器中。 使用 SubscriptionA 的 ID 來取代 `<SubscriptionB-Id>`，並將所有 A 變更為 B，以及將所有 B 變更為 A。變更完成之後，複製修改過的指令碼並貼到您的 CLI 工作階段中，然後按 `Enter`。
10. 檢視 myVnetB 的對等互連狀態。 將步驟 7 中的指令碼內容複製到您電腦上的文字編輯器中。 針對資源群組和虛擬網路名稱將 A 變更為 B，複製指令碼、將修改過的指令碼貼到您的 CLI 工作階段中，然後按 `Enter`。 對等互連狀態為 **Connected**。 在您建立從 myVnetB 到 myVnetA 的對等互連之後，myVnetA 的對等互連狀態就會變更為 **Connected**。 您可以將 UserA 重新登入 Azure，然後再次完成步驟 7 以確認 myVnetA 的對等互連狀態。 

    > [!NOTE]
    > 必須等到兩個虛擬網路的對等互連狀態都變成 **Connected** 之後，才算已建立對等互連。

11. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
12. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-cli)中的步驟。

您在任何一個虛擬網路中建立的任何 Azure 資源現在能夠透過其 IP 位址彼此通訊。 如果您使用虛擬網路的預設 Azure 名稱解析，則虛擬網路中的資源無法跨虛擬網路解析名稱。 如果您想要跨對等互連中的虛擬網路解析名稱，您必須建立自己的 DNS 伺服器。 了解如何設定[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。
 
## <a name="powershell"></a>建立對等互連 - PowerShell

本教學課程針對每個訂用帳戶使用不同的帳戶。 如果您使用對兩個訂用帳戶都有權限的帳戶，便可以使用該相同帳戶來進行所有步驟、略過登出 Azure 的步驟，以及移除建立使用者角色指派項目的指令碼行。 請使用您要用於 UserA 和 UserB 的使用者名稱來取代下列指令碼中的 UserA@azure.com 和 UserB@azure.com。

1. 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 啟動 PowerShell 工作階段。
3. 在 PowerShell 中，輸入 `login-azurermaccount` 命令來以 UserA 身分登入 Azure。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
4. 建立資源群組和虛擬網路 A。將下列指令碼複製到您電腦上的文字編輯器中。 使用 SubscriptionA 的 ID 來取代 `<SubscriptionA-Id>`。 如果您不知道您的訂用帳戶 ID，請輸入 `Get-AzureRmSubscription` 命令來檢視它。 傳回之輸出中的 **Id** 值就是您的訂用帳戶 ID。 若要執行指令碼，請複製修改過的指令碼並貼到 PowerShell 中，然後按 `Enter`。

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. 將 UserA 登出 Azure，然後以 UserB 身分登入。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
6. 將步驟 4 中的指令碼內容複製到您電腦上的文字編輯器中。 使用訂用帳戶 B 的 ID 來取代 `<SubscriptionA-Id>`。將 10.0.0.0/16 變更為 10.1.0.0/16。 將所有 A 都變更為 B，並將所有 B 都變更為 A。若要執行指令碼，請複製修改過的指令碼並貼到 PowerShell 中，然後按 `Enter`。
7. 將 UserB 登出 Azure，然後以 UserA 身分登入。
8. 建立從 myVnetA 到 myVnetB 的對等互連。 將下列指令碼複製到您電腦上的文字編輯器中。 使用訂用帳戶 B 的 ID 來取代 `<SubscriptionB-Id>`。若要執行指令碼，請複製修改過的指令碼並貼到 PowerShell 中，然後按 `Enter`。
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. 檢視 myVnetA 的對等互連狀態。

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    狀態為 **Initiated**。 在您設定從 myVnetB 到 myVnetA 的對等互連之後，它就會變更為 **Connected**。

10. 將 UserA 登出 Azure，然後以 UserB 身分登入。
11. 建立從 myVnetB 到 myVnetA 的對等互連。 將步驟 8 中的指令碼內容複製到您電腦上的文字編輯器中。 使用訂用帳戶 A 的識別碼來取代 `<SubscriptionB-Id>`，並將所有 A 都變更為 B，以及將所有 B 都變更為 A。若要執行指令碼，請複製修改過的指令碼並貼到 PowerShell 中，然後按 `Enter`。
12. 檢視 myVnetB 的對等互連狀態。 將步驟 9 中的指令碼內容複製到您電腦上的文字編輯器中。 針對資源群組和虛擬網路名稱，將 A 變更為 B。 若要執行指令碼，請將修改過的指令碼貼到 PowerShell 中，然後按 `Enter`。 狀態為 **Connected**。 在您建立從 **myVnetB** 到 **myVnetA** 的對等互連之後，**myVnetA** 的對等互連狀態就會變更為 **Connected**。 您可以將 UserA 重新登入 Azure，然後再次完成步驟 9 以確認 myVnetA 的對等互連狀態。 

    > [!NOTE]
    > 必須等到兩個虛擬網路的對等互連狀態都變成 **Connected** 之後，才算已建立對等互連。

    您在任何一個虛擬網路中建立的任何 Azure 資源現在能夠透過其 IP 位址彼此通訊。 如果您使用虛擬網路的預設 Azure 名稱解析，則虛擬網路中的資源無法跨虛擬網路解析名稱。 如果您想要跨對等互連中的虛擬網路解析名稱，您必須建立自己的 DNS 伺服器。 了解如何設定[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

13. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
14. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-powershell)中的步驟。

## <a name="template"></a>建立對等互連 - Resource Manager 範本

1. 若要建立虛擬網路，並將適當的[權限](#permissions)指派給每個訂用帳戶中的帳戶，請完成本文的[入口網站](#portal)、[Azure CLI](#cli) 或 [PowerShell](#powershell) 章節中的步驟。
2. 將下列文字儲存至您本機電腦上的檔案。 使用 UserA 的訂用帳戶識別碼來取代 `<subscription ID>`。 例如，您可以將檔案另存為 vnetpeeringA.json。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
    }
    ```

3. 以 UserA 身分登入 Azure，然後使用[入口網站](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template)、[PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine) 或 [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template) 部署範本。 指定您在步驟 2 中儲存範例 JSON 文字的目的地檔案名稱。
4. 將步驟 2 中的範例 JSON 複製到您電腦上的檔案，然後變更以下列項目開頭的行：
    - **名稱**：將 *myVnetA/myVnetAToMyVnetB* 變更為 *myVnetB/myVnetBToMyVnetA*。
    - **識別碼**：將 `<subscription ID>` 取代為 UserB 的訂用帳戶識別碼，並將 *myVnetB* 變更為 *myVnetA*。
5. 以 UserB 身分登入 Azure，並再次完成步驟 3。
6. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
7. **選擇性︰**若要刪除您在本教學課程中建立的資源，請使用 Azure 入口網站、PowerShell 或 Azure CLI 來完成本文之[刪除資源](#delete)一節中的步驟。

## <a name="permissions"></a>權限

您用於建立虛擬網路對等互連的帳戶必須具有必要的角色或權限。 例如，如果您是將名為 **myVnetA** 和 **myVnetB** 的兩個虛擬網路對等互連，您的帳戶就必須獲指派各個虛擬網路的下列最基本角色或權限：
    
|虛擬網路|角色|權限|
|---|---|---|
|myVnetA|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|myVnetB|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

深入了解[內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及如何對[自訂角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)指派特定權限 (僅限 Resource Manager)。

## <a name="delete"></a>刪除資源
當您完成本教學課程時，您可能會想刪除您在教學課程中建立的資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。

### <a name="delete-portal"></a>Azure 入口網站

1. 以 UserA 身分登入 Azure 入口網站。
2. 在入口網站搜尋方塊中，輸入 **myResourceGroupA**。 在搜尋結果中，按一下 [myResourceGroupA]。
3. 在 [myResourceGroupA] 刀鋒視窗中，按一下 [刪除] 圖示。
4. 若要確認刪除，請在 [輸入資源群組名稱] 方塊中輸入 **myResourceGroupA**，然後按一下 [刪除]。
5. 以 UserA 身分登出入口網站，然後以 UserB 身分登入。
6. 針對 myResourceGroupB，完成步驟 2 到 4。

### <a name="delete-cli"></a>Azure CLI

1. 以 UserA 身分登入 Azure，然後執行下列命令：

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. 以 UserA 身分登出 Azure，然後以 UserB 身分登入。
3. 執行以下命令：

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. 以 UserA 身分登入 Azure，然後執行下列命令：

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. 以 UserA 身分登出 Azure，然後以 UserB 身分登入。
3. 執行以下命令：

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>後續步驟

- 請先徹底熟悉重要[虛擬網路對等互連的條件約束和行為](virtual-network-manage-peering.md#requirements-and-constraints)，再建立虛擬網路對等互連以供生產環境使用。
- 了解所有[虛擬網路對等互連設定](virtual-network-manage-peering.md#create-a-peering)。
- 了解如何透過虛擬網路對等互連來[建立中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。
