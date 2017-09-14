---
title: "建立具有多個子網路的 Azure 虛擬網路 | Microsoft Docs"
description: "了解如何在 Azure 中建立具有多個子網路的虛擬網路。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: f82a95ec9543b2d53ef28bf7f15315e23cf4893a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>建立有多個子網路的虛擬網路

本教學課程可供了解如何建立具有不同公用和私人子網路的基本 Azure 虛擬網路。 虛擬網路中的資源可以互相通訊，也可以和連線到虛擬網路之其他網路中的資源通訊。 您可以在虛擬網路內的相同或不同子網路中建立各種 Azure 資源，例如虛擬機器、App Service 環境、虛擬機器擴展集、Azure HDInsight 及雲端服務。 在不同子網路建立資源可讓您利用[網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)獨立地篩選流入和流出子網路的網路流量，以及在您選擇後透過網路虛擬設備 (例如防火牆) [在子網路之間路由傳送流量](virtual-network-create-udr-arm-ps.md)。 

您可以採取下列各節所包含的步驟，使用 [Azure 入口網站](#portal)、Azure 命令列介面 ([Azure CLI](#azure-cli))、[Azure PowerShell](#powershell) 和 [Azure Resource Manager 範本](#resource-manager-template)來建立虛擬網路。 無論您使用哪一種工具來建立虛擬網路，結果都會相同。 按一下工具連結即可前往教學課程中關於該工具的章節。 深入了解所有[虛擬網路](virtual-network-manage-network.md)和[子網路](virtual-network-manage-subnet.md)設定。

本文提供透過 Resource Manager 部署模型建立虛擬網路的步驟，而此部署模型正是建立新虛擬網路時我們建議使用的部署模型。 如果您需要建立虛擬網路 (傳統)，請參閱[建立虛擬網路 (傳統)](create-virtual-network-classic.md)。 如果您不熟悉 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="portal"></a>Azure 入口網站

1. 在網際網路瀏覽器中，移至 [Azure 入口網站](https://portal.azure.com)。 使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在入口網站中按一下 [+新增] > [網路] > [虛擬網路]。
3. 在 [建立虛擬網路] 刀鋒視窗中輸入下列值，然後按一下 [建立]：

    |設定|值|
    |---|---|
    |名稱|myVnet|
    |位址空間|10.0.0.0/16|
    |子網路名稱|公開|
    |子網路位址範圍|10.0.0.0/24|
    |資源群組|讓 [新建] 保持選取狀態，然後輸入 **myResourceGroup**。|
    |訂用帳戶和位置|選取您的訂用帳戶和位置。

    如果您不熟悉 Azure，請深入了解[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和[位置](https://azure.microsoft.com/regions) (也稱為「區域」)。
4. 當您在入口網站中建立虛擬網路時，您只能建立一個子網路。 在本教學課程中，您會在建立虛擬網路後建立第二個子網路。 稍後您可能會在**公用**子網路中建立可從網際網路存取的資源。 您也可能會在**私人**子網路中建立無法從網際網路存取的資源。 若要建立第二個子網路，請在頁面頂端的 [搜尋資源] 方塊中輸入 **myVnet**。 在搜尋結果中按一下 [myVnet]。 如果您的訂用帳戶中有多個同名虛擬網路，請檢查每個虛擬網路下面所列出的資源群組。 確定您所按的是擁有資源群組 **myResourceGroup** 的 **myVnet** 搜尋結果。
5. 在 [myVnet] 刀鋒視窗中，按一下 [設定] 底下的 [子網路]。
6. 在 [myVnet - 子網路] 刀鋒視窗中，按一下 [+子網路]。
7. 在 [新增子網路] 刀鋒視窗中，對 [名稱] 輸入**私人**。 對 [位址範圍] 輸入 **10.0.1.0/24**。  按一下 [確定] 。
8. 在 [myVnet - 子網路] 刀鋒視窗中檢閱子網路。 您就會看到您所建立的**公用**和**私人**子網路。
9. **選擇性：**完成[後續步驟](#next-steps)底下所列的其他教學課程，以使用網路安全性群組篩選流入和流出每個子網路的網路流量、透過網路虛擬設備在子網路之間路由傳送流量，或是將虛擬網路連線至其他虛擬網路或內部部署網路。
10. **選擇性︰**完成[刪除資源](#delete-portal)中的步驟，以刪除您在本教學課程中所建立的資源。

## <a name="azure-cli"></a>Azure CLI

無論您是從 Windows、Linux 或 macOS 執行命令，所使用的 Azure CLI 命令都相同。 不過，不同作業系統殼層的指令碼編寫會有差異。 下列步驟中的指令碼會在 Bash 殼層中執行。 

1. [安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。 您可以不安裝 CLI 及其必要條件，而是改用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 Cloud Shell 具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 若要使用 Cloud Shell，按一下[入口網站](https://portal.azure.com)頂端的 Cloud Shell (**> _**) 按鈕，或直接按一下後續步驟中的 [試用] 按鈕。 
2. 如果是在本機執行 CLI，請使用 `az login` 命令登入 Azure。 如果是使用 Cloud Shell，則您已經登入。
3. 檢閱下列指令碼和其註解。 在瀏覽器中，複製指令碼並貼到您的 CLI 工作階段：

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. 當指令碼完成執行後，檢閱虛擬網路的子網路。 複製下列命令並貼到您的 CLI 工作階段：

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **選擇性：**完成[後續步驟](#next-steps)底下所列的其他教學課程，以使用網路安全性群組篩選流入和流出每個子網路的網路流量、透過網路虛擬設備在子網路之間路由傳送流量，或是將虛擬網路連線至其他虛擬網路或內部部署網路。
6. **選擇性︰**完成[刪除資源](#delete-cli)中的步驟，以刪除您在本教學課程中所建立的資源。

## <a name="powershell"></a>PowerShell

1. 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 在 PowerShell 工作階段中，使用 `login-azurermaccount` 命令搭配您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入 Azure。

3. 檢閱下列指令碼和其註解。 在瀏覽器中，複製指令碼並貼到您的 PowerShell 工作階段：

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. 若要檢閱虛擬網路的子網路，請複製下列命令，然後貼到 PowerShell 工作階段：

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **選擇性：**完成[後續步驟](#next-steps)底下所列的其他教學課程，以使用網路安全性群組篩選流入和流出每個子網路的網路流量、透過網路虛擬設備在子網路之間路由傳送流量，或是將虛擬網路連線至其他虛擬網路或內部部署網路。
6. **選擇性︰**完成[刪除資源](#delete-powershell)中的步驟，以刪除您在本教學課程中所建立的資源。

## <a name="resource-manager-template"></a>Resource Manager 範本

您可以使用 Azure Resource Manager 範本來部署虛擬網路。 若要深入了解範本，請參閱[什麼是 Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment)。 若要存取範本並了解其參數，請參閱[建立具有兩個子網路的虛擬網路](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)範本。 您可以使用[入口網站](#template-portal)、[Azure CLI](#template-cli) 或 [PowerShell](#template-powershell) 來部署範本。

部署範本後的選擇性步驟：

1. 完成[後續步驟](#next-steps)底下所列的其他教學課程，以使用網路安全性群組篩選流入和流出每個子網路的網路流量、透過網路虛擬設備在子網路之間路由傳送流量，或是將虛擬網路連線至其他虛擬網路或內部部署網路。
2. 完成[刪除資源](#delete)中所有小節的步驟，以刪除您在本教學課程中所建立的資源。

### <a name="template-portal"></a>Azure 入口網站

1. 在瀏覽器中開啟[範本頁面](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)。
2. 按一下 [部署至 Azure] 按鈕。 如果您尚未登入 Azure，請在顯示的 Azure 入口網站登入畫面上登入。
3. 使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入入口網站。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 為各個參數輸入下列值︰

    |參數|值|
    |---|---|
    |訂用帳戶|選取您的訂用帳戶|
    |資源群組|myResourceGroup|
    |位置|選取位置|
    |Vnet 名稱|myVnet|
    |Vnet 位址首碼|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|公開|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|私人|

5. 同意條款及條件，然後按一下 [購買] 以部署虛擬網路。

### <a name="template-cli"></a>Azure CLI

1. [安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。 您可以不安裝 CLI 及其必要條件，而是改用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 Cloud Shell 具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 若要使用 Cloud Shell，按一下[入口網站](https://portal.azure.com)頂端的 Cloud Shell (**> _**) 按鈕，或直接按一下後續步驟中的 [試用] 按鈕。 
2. 如果是在本機執行 CLI，請使用 `az login` 命令登入 Azure。 如果是使用 Cloud Shell，則您已經登入。
3. 若要建立虛擬網路的資源群組，請複製下列命令並貼到您的 CLI 工作階段︰

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. 您可以使用下列其中一個參數選項來部署範本：
    - **預設參數值**。 輸入下列命令：
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **自訂參數值**。 請先下載並修改範本，再部署範本。 您也可以在命令列使用參數來部署範本，或使用不同的參數檔案來部署範本。 若要下載範本和參數檔，請按一下[建立具有兩個子網路的虛擬網路](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)範本頁面上的 [瀏覽 GitHub] 按鈕。 在 GitHub 中，按一下 **azuredeploy.parameters.json** 或 **azuredeploy.json** 檔案。 然後，按一下 [原始] 按鈕來顯示該檔案。 在瀏覽器中，複製該檔案的內容。 將內容儲存至您電腦上的檔案。 您可以修改範本中的參數值，或使用不同的參數檔來部署範本。  

    若要深入了解如何使用這些方法來部署範本，請輸入 `az group deployment create --help`。

### <a name="template-powershell"></a>PowerShell

1. 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 在 PowerShell 工作階段中，若要使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入，請輸入 `login-azurermaccount`。
3. 若要建立虛擬網路的資源群組，請輸入下列命令︰

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. 您可以使用下列其中一個參數選項來部署範本：
    - **預設參數值**。 輸入下列命令：
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **自訂參數值**。 請先下載並修改範本，再部署範本。 您也可以在命令列使用參數來部署範本，或使用不同的參數檔案來部署範本。 若要下載範本和參數檔，請按一下[建立具有兩個子網路的虛擬網路](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)範本頁面上的 [瀏覽 GitHub] 按鈕。 在 GitHub 中，按一下 **azuredeploy.parameters.json** 或 **azuredeploy.json** 檔案。 然後，按一下 [原始] 按鈕來顯示該檔案。 在瀏覽器中，複製該檔案的內容。 將內容儲存至您電腦上的檔案。 您可以修改範本中的參數值，或使用不同的參數檔來部署範本。  

    若要深入了解如何使用這些方法來部署範本，請輸入 `Get-Help New-AzureRmResourceGroupDeployment`。 

## <a name="delete"></a>刪除資源

完成本教學課程之後，可考慮刪除所建立的資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。

### <a name="delete-portal"></a>Azure 入口網站

1. 在入口網站的搜尋方塊中，輸入 **myResourceGroup**。 在搜尋結果中按一下 [myResourceGroup]。
2. 在 [myResourceGroup] 刀鋒視窗中，按一下 [刪除] 圖示。
3. 若要確認刪除動作，請在 [輸入資源群組名稱] 方塊中輸入 **myResourceGroup**，然後按一下 [刪除]。

### <a name="delete-cli"></a>Azure CLI

在 CLI 工作階段中，輸入下列命令：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

在 PowerShell 工作階段中，輸入下列命令：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

- 若要了解所有虛擬網路和子網路的設定，請參閱[管理虛擬網路](virtual-network-manage-network.md#view-vnet)和[管理虛擬網路子網路](virtual-network-manage-subnet.md#create-subnet)。 您有各種選項可在生產環境中使用虛擬網路和子網路，以便符合不同的需求。
- 對子網路建立和套用[網路安全性群組](virtual-networks-nsg.md)，以篩選輸入和輸出的子網路流量。
- 藉由建立[使用者定義的路由](virtual-network-create-udr-arm-ps.md)以在子網路之間透過網路虛擬設備來路由傳送流量，以及將路由套用至每個子網路。
- 在現有虛擬網路中建立 [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器。
- 在虛擬網路之間建立[虛擬網路對等互連](virtual-network-peering-overview.md)以將兩個虛擬網路連線。
- 使用 [VPN 閘道](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 線路將虛擬網路連線至內部部署網路。

