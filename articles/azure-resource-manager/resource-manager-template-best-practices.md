---
title: "建立 Resource Manager 範本的最佳做法 | Microsoft Docs"
description: "簡化 Azure Resource Manager 範本的指導方針。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.openlocfilehash: a23301ba88279af3f7bf4d353ae808e9eeb0900d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>建立 Azure Resource Manager 範本的最佳做法
下列指導方針可協助您建立可靠又容易使用的 Azure Resource Manager 範本。 指導方針僅為建議。 除了另有註明外，它們並非必要項目。 您的案例可能需要下列其中一個方法或範例的變化。

## <a name="resource-names"></a>資源名稱
通常，您會使用 Resource Manager 中的三種資源名稱︰

* 必須是唯一的資源名稱。
* 不需要是唯一的資源名稱，但是您選擇提供的名稱可協助您根據內容識別資源。
* 可以是一般的資源名稱。

 如需有關資源名稱限制的詳細資訊，請參閱 [Recommended naming conventions for Azure resources (Azure 資源的建議命名慣例)](../guidance/guidance-naming-conventions.md)。

### <a name="unique-resource-names"></a>唯一的資源名稱
您必須為任何有資料存取端點的資源類型，提供唯一的資源名稱。 一些需要唯一名稱的常見資源類型包括︰

* Azure 儲存體<sup>1</sup> 
* Azure App Service 中的 Web Apps 功能
* SQL Server
* Azure 金鑰保存庫
* Azure Redis 快取
* Azure Batch
* Azure 流量管理員
* Azure 搜尋服務
* Azure HDInsight

<sup>1</sup> 儲存體帳戶名稱也必須是小寫、長度不超過 24 個字元，而且沒有任何連字號。

如果您提供資源名稱的參數，必須在部署資源時提供唯一的名稱。 您可以選擇性地建立會使用 [uniqueString()](resource-group-template-functions-string.md#uniquestring) 函式來產生名稱的變數。 

您也可能想要將首碼或尾碼新增至 **uniqueString** 結果。 修改唯一的名稱可協助您更輕鬆地識別名稱的資源類型。 例如，您可以使用下列變數來產生儲存體帳戶的唯一名稱：

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>用於識別的資源名稱
您可能想要將某些資源類型命名，但其名稱不必是唯一的。 針對這些資源類型，您可以提供能識別資源內容和資源類型的名稱。 提供描述性的名稱可協助您識別資源清單中的資源。 如果您需要將不同的資源名稱用於不同的部署，可以使用名稱的參數︰

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

如果您不需要在部署期間傳遞名稱，可以使用變數︰ 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

您也可以使用硬式編碼的值︰

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
}
```

### <a name="generic-resource-names"></a>一般資源名稱
若為主要透過不同資源來存取的資源類型，您可以使用在範本中硬式編碼的一般名稱。 例如，您可以在 SQL Server 上設定防火牆規則的標準、一般名稱︰

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="parameters"></a>參數
當您使用參數時，下列資訊可能會很有幫助︰

* 將參數的使用最小化。 可能的話，使用變數或常值。 只針對這些案例使用參數︰
   
   * 您想要根據環境 (SKU、大小、容量) 使用變化的設定。
   * 您希望能方便識別而指定的資源名稱。
   * 您經常用來完成其他工作 (例如，管理使用者名稱) 的值。
   * 機密資料 (例如密碼)。
   * 建立多個資源類型執行個體時要使用的數字或值陣列。
* 參數名稱使用駝峰式大小寫。
* 在中繼資料中提供每個參數的描述：

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* 定義參數的預設值 (密碼和 SSH 金鑰除外)：
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* 所有密碼和祕密都要使用 **securestring**： 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* 可能的話，請勿使用參數來指定位置。 請改為使用資源群組的 **location** 屬性。 針對所有資源使用 **resourceGroup().location** 運算式，就會將範本中的資源作為資源群組部署在相同的位置：
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   如果只在有限的位置支援某資源類型，您可能想要直接在範本中指定有效的位置。 如果您必須使用 **location** 參數，請儘可能和可能位於相同位置的資源一起共用該參數值。 這樣可以降低系統要求使用者提供位置資訊的次數。
* 資源類型的 API 版本要避免使用參數或變數。 資源屬性和值可能會隨版本號碼而不同。 將 API 版本設定為參數或變數時，程式碼編輯器中的 Intellisense 會無法判斷正確的結構描述。 請改為將 API 版本硬式編碼在範本中。

## <a name="variables"></a>變數
當您使用變數時，下列資訊可能會很有幫助︰

* 您需要在範本中使用一次以上的值，才使用變數。 如果值只會使用一次，硬式編碼值會讓您的範本較容易看懂。
* 您不能使用範本之 **variables** 區段中的 [reference](resource-group-template-functions-resource.md#reference) 函式。 **reference** 函式的值是從資源的執行階段狀態所衍生。 不過，將範本初始剖析時，會將變數加以解析。 請直接在範本的 **resources** 或 **outputs** 區段中，建構需要 **reference** 函式的值。
* 如[資源名稱](#resource-names)中所述，包含變數以用於必須是唯一的資源名稱。
* 您可以將變數組成複雜物件。 使用 **variable.subentry** 格式，參考來自複雜物件的值。 群組變數可協助您追蹤相關的變數。 它也可以改善範本的可讀性。 以下是範例：
   
   ```json
   "variables": {
       "storage": {
           "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
           "type": "Standard_LRS"
       }
   },
   "resources": [
     {
         "type": "Microsoft.Storage/storageAccounts",
         "name": "[variables('storage').name]",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "sku": {
             "name": "[variables('storage').type]"
         },
         ...
     }
   ]
   ```
   
   > [!NOTE]
   > 複雜物件包含的運算式不可以參考來自複雜物件的值。 請針對此目的，定義個別的變數。
   > 
   > 
   
     如需使用複雜物件作為變數的進階範例，請參閱 [Azure Resource Manager 範本中的共用狀態](best-practices-resource-manager-state.md)。

## <a name="resources"></a>資源
當您使用資源時，下列資訊可能會很有幫助︰

* 針對範本中的每個資源指定 **comments**，協助其他參與者了解資源的用途：
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* 您可以使用標記，將中繼資料新增至資源。 使用中繼資料來新增資源的相關資訊。 例如，您可以新增中繼資料，來記錄資源的帳單詳細資料。 如需詳細資訊，請參閱 [使用標記組織您的 Azure 資源](resource-group-using-tags.md)。
* 如果您使用範本中的公用端點 (例如 Azure Blob 儲存體公用端點)，請勿將命名空間硬式編碼。 使用 **reference** 函式，動態擷取命名空間。 您可以使用此方法可將範本部署到不同的公用命名空間環境中，而不需要將範本中的端點手動變更。 將 API 版本設定成與您在範本中用於儲存體帳戶相同的版本：
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   如果將該儲存體帳戶部署在您要建立的相同範本中，當您參考資源時，即不需要指定提供者命名空間。 簡化的語法如下︰
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   如果您範本中其他的值是使用公用命名空間進行設定，請變更這些值以反映相同的 **reference** 函式。 例如，您可以設定虛擬機器診斷設定檔的 **storageUri** 屬性：
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   您也可以參考不同資源群組中現有的儲存體帳戶：

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* 只有在應用程式要求時，才將公用 IP 位址指派給虛擬機器。 若要連線至虛擬機器 (VM) 進行偵錯，或要進行管理或系統管理，請使用輸入 NAT 規則、虛擬網路閘道或 Jumpbox。
   
     如需有關如何連線至虛擬機器的詳細資訊，請參閱︰
   
   * [在 Azure 中執行多層式架構的 VM](../guidance/guidance-compute-n-tier-vm.md)
   * [在 Azure Resource Manager 中設定 VM 的 WinRM 存取](../virtual-machines/windows/winrm.md)
   * [允許使用 Azure 入口網站從外部存取您的 VM](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [允許使用 PowerShell 從外部存取您的 VM](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [允許使用 Azure CLI 從外部存取您的 Linux VM](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* 公用 IP 位址的 **domainNameLabel** 屬性必須是唯一的。 **domainNameLabel** 值的長度必須介於 3 到 63 個字元之間，還要遵循這個規則運算式指定的規則：`^[a-z][a-z0-9-]{1,61}[a-z0-9]$`。 **uniqueString** 函式會產生長度為 13 個字元的字串，因此 **dnsPrefixString** 參數會限制為 50 個字元：

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* 將密碼新增至自訂指令碼擴充功能時，使用 **protectedSettings** 屬性中的 **commandToExecute** 屬性。
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > 為了確保作為參數傳遞至 VM 和擴充功能的祕密會經過加密，請使用相關擴充功能的 **protectedSettings** 屬性。
   > 
   > 

## <a name="outputs"></a>輸出
如果您使用範本建立公用 IP 位址，則請包含 **outputs** 區段以傳回 IP 位址和完整網域名稱的詳細資料。 您可以使用輸出值，輕鬆在部署後擷取公用 IP 位址和 FQDN 的相關詳細資料。 參考資源時，請使用當時用來建立該資源的 API 版本： 

```json
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-vs-nested-templates"></a>單一範本與巢狀範本
若要部署解決方案，您可以使用單一範本或有多個巢狀範本的主要範本。 巢狀範本常見於更進階的案例。 使用巢狀範本會提供您下列優點︰

* 您可以將解決方案分解為多個目標元件。
* 您可以搭配不同的主要範本重複使用巢狀範本。

如果您選擇使用巢狀範本，下列指導方針可協助您將範本設計標準化。 這些指導方針是以 [設計 Azure Resource Manager 範本模式](best-practices-resource-manager-design-templates.md)為基礎。 我們建議具有下列範本的設計︰

* **主要範本** (azuredeploy.json)。 用於輸入參數。
* **共用的資源範本**。 用於部署所有其他資源可以使用的共用資源 (例如虛擬網路和可用性設定組)。 使用 **dependsOn** 運算式可確保在其他範本之前部署此範本。
* **選擇性資源範本**。 用於根據參數 (例如 jumpbox) 有條件地部署資源。
* **成員資源範本**。 應用程式層內的每個執行個體類型都有自己的組態。 您可以在一個階層內定義不同的執行個體類型。 (例如，第一個執行個體會建立叢集，而其他執行個體會新增至現有的叢集。)每個執行個體類型有自己的部署範本。
* **指令碼**。 廣泛可重複使用的指令碼適用於各種個執行個體類型 (例如，將其他磁碟初始化和格式化)。 您針對特定自訂用途而建立的自訂指令碼，會根據各種執行個體類型而不同。

![巢狀範本](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

如需詳細資訊，請參閱 [透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。

## <a name="conditionally-link-to-nested-templates"></a>有條件地連結到巢狀範本
您可以使用參數，有條件地連結到巢狀範本。 參數會變成範本 URI 的一部分︰

```json
"parameters": {
    "newOrExisting": {
        "type": "String",
        "allowedValues": [
            "new",
            "existing"
        ]
    }
},
"variables": {
    "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
},
"resources": [
    {
        "apiVersion": "2015-01-01",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "[variables('templatelink')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
            }
        }
    }
]
```

## <a name="template-format"></a>範本格式
最好將您的範本透過 JSON 驗證程式傳遞。 驗證程式可協助您移除在部署期間可能會造成錯誤的多餘逗號、括號及方括號。 請嘗試在您喜愛的編輯環境 (Visual Studio 程式碼、Atom、Sublime Text、Visual Studio) 中使用 [JSONLint](http://jsonlint.com/) 或 linter 套件。

也建議您將 JSON 格式化以更方便閱讀。 您可以對本機的編輯器使用 JSON 格式器封裝。 在 Visual Studio 中，若要格式化文件，請按 **Ctrl+K、Ctrl+D**。 在 Visual Studio 程式碼中，請按 **Alt+Shift+F**。 如果您的本機編輯器並不會格式化文件，您可以使用 [線上格式器](https://www.bing.com/search?q=json+formatter)。

## <a name="next-steps"></a>後續步驟
* 如需設計虛擬機器解決方案架構的指引，請參閱[在 Azure 中執行 Windows VM](../guidance/guidance-compute-single-vm.md) 和[在 Azure 中執行 Linux VM](../guidance/guidance-compute-single-vm-linux.md)。
* 如需有關設定儲存體帳戶的指引，請參閱 [Azure 儲存體效能與延展性檢查清單](../storage/common/storage-performance-checklist.md)。
* 若要深入了解企業如何使用 Resource Manager 有效地管理訂閱，請參閱 [Azure 企業 Scaffold：規定的訂用帳戶治理](resource-manager-subscription-governance.md)。

