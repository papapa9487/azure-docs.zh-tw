---
title: "建立虛擬網路 | Azure Resource Manager 範本 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 範本建立虛擬網路。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81602766848a91331c8d811ea1c8ec3ffae44b96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立虛擬網路

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure 有兩個部署模型：Azure Resource Manager 和傳統。 Microsoft 建議透過 Resource Manager 部署模型建立資源。 若要深入了解兩個模型的差異，請閱讀[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。
 
本文說明如何使用 Azure Resource Manager 範本透過 Resource Manager 部署模型建立 VNet。 您也可以使用其他工具透過 Resource Manager 建立 VNet，或從下列清單中選取不同選項以透過傳統部署模型建立 VNet︰

> [!div class="op_single_selector"]
- [入口網站](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [範本](virtual-networks-create-vnet-arm-template-click.md)
- [入口網站 (傳統)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (傳統)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (傳統)](virtual-networks-create-vnet-classic-cli.md)

您將了解如何從 GitHub 下載和修改現有 ARM 範本，並從 GitHub、PowerShell 和 Azure CLI 部署範本。

如果您只需要直接從 GitHub 部署 ARM 範本而不做任何變更，請跳至 [從 GitHub 部署範本](#deploy-the-arm-template-by-using-click-to-deploy)。

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>下載並了解 Azure 資源管理員範本
您可以下載現有的範本，以透過 Github 建立 VNet 和兩個子網路，然後進行任何需要的變更，並重複使用該範本。 若要這樣做，完成下列步驟：

1. 瀏覽至 [範例範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)。
2. 依序按一下 [azuredeploy.json] 和 [RAW]。
3. 將檔案儲存至您電腦上的本機資料夾。
4. 如果您熟悉範本的使用方式，請跳至步驟 7。
5. 開啟您剛儲存的檔案，並查看第 5 行中 **parameters** 下方的內容。 ARM 範本的參數提供值的預留位置，可以在部署期間填寫。
   
   | 參數 | 說明 |
   | --- | --- |
   | **位置** |將會在當中建立 VNet 的 Azure 區域 |
   | **vnetName** |新 VNet 的名稱 |
   | **addressPrefix** |以 CIDR 格式表示的 VNet 位址空間 |
   | **subnet1Name** |第一個 VNet 的名稱 |
   | **subnet1Prefix** |第一個子網路的 CIDR 區塊 |
   | **subnet2Name** |第二個 VNet 的名稱 |
   | **subnet2Prefix** |第二個子網路的 CIDR 區塊 |
   
   > [!IMPORTANT]
   > GitHub 所維護的 Azure 資源管理員範本可能會隨著時間改變。 使用範本前，請務必先檢查當中的內容。
   > 
   > 
6. 檢查 **resources** 下方的內容，並注意以下項目：
   
   * **type**。 範本所建立的資源類型。 在此案例中為 **Microsoft.Network/virtualNetworks**，代表 VNet。
   * **名稱**。 資源的名稱。 請查看使用 **[parameters('vnetName')]**的項目，這表示此名稱是在部署期間由使用者的輸入內容，或參數檔案提供。
   * **屬性**。 資源屬性的清單。 此範本在 VNet 建立期間會使用位址空間和子網路屬性。
7. 瀏覽回 [範例範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)。
8. 依序按一下 [azuredeploy-paremeters.json] 和 [RAW]。
9. 將檔案儲存至您電腦上的本機資料夾。
10. 開啟您剛儲存的檔案，編輯參數的值。 使用下列值部署案例中所述的 VNet：

    ```json
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. 儲存檔案。


## <a name="deploy-the-template-using-powershell"></a>使用 PowerShell 部署範本

使用 PowerShell，完成下列步驟部署您下載的範本：

1. 完成[如何安裝並設定 Azure PowerShell](/powershell/azure/overview) 中的下列步驟來安裝和設定 Azure PowerShell。
2. 執行下列命令以建立新的資源群組：

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    此命令會在「美國中部」Azure 區域中，建立名為 *TestRG* 的資源群組。 如需資源群組的詳細資訊，請瀏覽 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。

    預期的輸出：

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. 執行下列命令，以使用先前下載並修改的範本和參數檔案來部署新的 VNet：

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    預期的輸出：
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. 執行下列命令以檢視新 VNet 的屬性：

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    預期的輸出：

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>使用按一下即部署來部署範本

您可以重複使用預先定義的 Azure Resource Manager 範本 (且範本已上傳至由 Microsoft 維護且開放社群使用的 GitHub 存放庫)。 這些範本可以直接從 GitHub 部署，或下載並修改以符合您的需求。 若要部署可建立具有兩個子網路之 VNet 的範本，完成下列步驟：

1. 請透過瀏覽器瀏覽 [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)。
2. 向下捲動範本清單，按一下 [101-vnet-two-subnets]。 查看 **README.md** 檔案，如下所示。

    ![Github 中的 READEME.md 檔案](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. 按一下 [ **部署至 Azure**]。 如有必要，請輸入您的 Azure 登入認證。 
4. 在 參數 刀鋒視窗中，輸入您要用以建立新 VNet 的值，然後按一下確定。 下圖顯示此案例的值︰
   
    ![ARM 範本參數](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. 按一下 [資源群組]，選取要加入 VNet 的目的地資源群組，或按一下 [新建]，將 VNet 加入至新的資源群組。 下圖顯示新資源群組 **TestRG** 的資源群組設定：

    ![資源群組](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. 如有必要，變更 VNet 的**訂用帳戶**和**位置**設定。
7. 如果您不想在**「開始面板」**上看到 VNet 圖格，請停用 [釘選到「開始面板」]。
8. 按一下 [法律條款] 並閱讀條款，然後按一下 [購買] 表示同意條款。 
9. 按一下 [建立] 來建立 VNet。
   
    ![在 Preview 入口網站中提交部署磚](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. 一旦部署完成，在 Azure 入口網站中按一下 [更多服務]，在出現的 [篩選] 方塊中輸入「虛擬網路」，然後按一下 [虛擬網路] 就會看到 [虛擬網路] 刀鋒視窗。 按一下刀鋒視窗中的 [TestVNet]。 在 [TestVNet] 刀鋒視窗中，按一下 [子網路] 以查看建立的子網路，如下圖所示︰
    
     ![在 Preview 入口網站中建立 VNet](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>後續步驟

了解如何連接︰

- 虛擬機器 (VM) 至虛擬網路；請閱讀[建立 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 或[建立 Linux VM](../virtual-machines/linux/quick-create-portal.md)。 但不是如文章中的步驟建立 VNet 和子網路，而是選取現有的 VNet 和子網路來連接 VM。
- 虛擬網路至其他虛擬網路；請閱讀[連接 VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。
- 虛擬網路至內部部署網路；使用網站對網站虛擬私人網路 (VPN) 或 ExpressRoute 線路。 如需了解做法，請閱讀[使用網站對網站 VPN 將 VNet 連接到內部部署網路](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)以及[將 VNet 連結至 ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-arm.md)。
