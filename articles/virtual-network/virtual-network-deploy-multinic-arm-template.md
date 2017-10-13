---
title: "建立具有多個 NIC 的 VM - Azure Resource Manager 範本 | Microsoft Docs"
description: "使用 Azure Resource Manager 範本建立具有多個 NIC 的 VM。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85bfa264c6cf2b0586816a47b3ab72f3aee8ec96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>使用範本建立具有多個 NIC 的 VM
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../resource-manager-deployment-model.md)。  本文涵蓋內容包括使用 Resource Manager 部署模型，Microsoft 建議大部分的新部署使用此模型，而不是[傳統部署模型](virtual-network-deploy-multinic-classic-ps.md)。
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

在下列步驟中，WEB 伺服器使用名為 *IaaSStory* 的資源群組，而 DB 伺服器使用名為 *IaaSStory-BackEnd* 的資源群組。

## <a name="prerequisites"></a>必要條件
您需要建立 *IaaSStory* 資源群組，其中含有此案例的所有必要資源，才能建立 DB 伺服器。 若要建立這些資源，請完成下列步驟：

1. 瀏覽至 [範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 在範本頁面中，按一下 [父資源群組] 右邊的 [部署至 Azure]。
3. 視需要變更參數值，然後依照 Azure Preview 入口網站中的步驟部署資源群組。

> [!IMPORTANT]
> 請確定您的儲存體帳戶名稱是唯一的。 在 Auzre 中不能有重複的儲存體帳戶名稱。
> 

## <a name="understand-the-deployment-template"></a>了解部署範本
在部署此文件所提供的範本之前，請確定您了解它的功用。 下列步驟提供此範本的清晰概觀：

1. 瀏覽至 [範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 按一下 **azuredeploy.json** 以開啟範本檔案。
3. 請注意下面列出的 *osType* 參數。 此參數用於選取要用於資料庫伺服器的 VM 映像，以及多個作業系統相關設定。

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. 向下捲動到變數清單，然後檢查 **dbVMSetting** 變數的定義，如下所示。 它會接收包含在 **dbVMSettings** 變數中的其中一個陣列元素。 如果您熟悉軟體開發術語，您可以將 **dbVMSettings** 變數視為雜湊表或字典。

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. 假設您決定要在後端部署執行 SQL 的 Windows VM。 **osType** 的值會是 *Windows*，而 **dbVMSetting** 變數會包含下列元素，它代表 **dbVMSettings** 變數中的第一個值。

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. 請注意 **vmSize** 的值為 *Standard_DS3*。 只有特定的 VM 大小允許使用多個 NIC。 請參閱 [Windows Server VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)和 [Linux VM 大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)文章，以確認哪些 VM 大小支援多個 NIC。

7. 向下捲動至 **resources** 並注意第一個元素。 它描述儲存體帳戶。 此儲存體帳戶將用來維護每個資料庫 VM 所使用的資料磁碟。 在此案例中，每部資料庫 VM 都有儲存在一般儲存體的作業系統磁碟，以及儲存在 SSD (進階) 儲存體的兩個資料磁碟。

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. 向下捲動到下一個資源，如下所示。 這項資源代表每個資料庫 VM 中用於資料庫存取的 NIC。 請注意此資源中 **copy** 函式的使用方式。 根據 **dbCount** 參數，此範本可讓您部署任意數量的 VM。 因此，您需要建立相同數量的 NIC 以用於資料庫存取 (每部 VM 一個)。

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. 向下捲動到下一個資源，如下所示。 這項資源代表每個資料庫 VM 中用於管理的 NIC。 同樣地，每個資料庫 VM 都需要一個這種 NIC。 請注意， **networkSecurityGroup** 元素只會連結允許存取 RDP/SSH 的 NSG 到此 NIC。

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. 向下捲動到下一個資源，如下所示。 這項資源代表所有資料庫 VM 要共用的可用性設定組。 如此一來，您可以保證在維護期間，設定組中一定會有一部 VM 在執行。

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. 向下捲動到下一個資源。 這項資源代表資料庫 VM，如下面列出的前幾行所示。 請注意再次用到 **copy** 函式的方式，確定是根據 **dbCount** 參數建立多個 VM。 另外也請注意 **dependsOn** 集合。 在 VM 部署前，它會列出兩個必須建立的 NIC，以及可用性設定組和儲存體帳戶。

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. 在 VM 資源中向下捲動至 **networkProfile** 元素，如下所示。 請注意，每部 VM 都有兩個參照的 NIC。 當您為 VM 建立多個 NIC 時，您必須將其中一個 NIC 的 **primary** 屬性設為 *true*，其餘的設為 *false*。

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>使用按一下即部署來部署 ARM 範本

> [!IMPORTANT]
> 依照下列指示進行之前，請確定您有遵循 [必要條件](#Pre-requisites) 中的步驟。
> 

公用儲存機制中可用的範例範本會使用一個包含預設值的參數檔案，這些預設值可用來產生上述案例。 若要使用「按一下即部署」來部署此範本，請依循[此連結](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)，按一下 [後端資源群組 (請參閱文件)] 右邊的 [部署至 Azure]，視需要取代預設參數值，然後遵循入口網站中的指示。

下圖顯示部署之後，新資源群組的內容。

![後端資源群組](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 部署範本
若要使用 PowerShell 來部署您下載的範本，請完成[安裝和設定 PowerShell](/powershell/azure/overview) 文章中的步驟來安裝和設定 PowerShell，然後完成下列步驟︰

執行 **`New-AzureRmResourceGroup`** Cmdlet，以使用範本建立資源群組。

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

預期的輸出：

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署範本
若要使用 Azure CLI 部署範本，請依照下列步驟執行。

1. 如果您從未用過 Azure CLI，請參閱 [安裝和設定 Azure CLI](../cli-install-nodejs.md) ，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶。
2. 執行 **`azure config mode`** 命令，以切換為資源管理員模式，如下所示。

    ```azurecli
    azure config mode arm
    ```

    預期的輸出如下︰

        info:    New mode is arm

3. 開啟 [參數檔案](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json)，選取其內容，然後將該內容儲存至您電腦中的一個檔案。 在此範例中，我們將參數檔案儲存為 *parameters.json*。
4. 執行 **`azure group deployment create`** Cmdlet，以使用先前下載並修改的範本和參數檔案，部署新的 VNet。 輸出後顯示的清單可說明所使用的參數。

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    預期的輸出：
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

