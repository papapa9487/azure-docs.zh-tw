---
title: "Windows 的 Azure 自訂指令碼延伸模組 | Microsoft Docs"
description: "使用「自訂指令碼擴充功能」來自動執行 Windows VM 組態工作"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/16/2017
ms.author: danis
ms.openlocfilehash: b250f7aee18ddf06c4f33b36247127141eeac528
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="custom-script-extension-for-windows"></a>Windows 的自訂指令碼延伸模組

「自訂指令碼擴充功能」會在 Azure 虛擬機器上下載並執行指令碼。 此擴充功能適用於部署後組態、軟體安裝或其他任何組態/管理工作。 您可以從 Azure 儲存體或 GitHub 下載指令碼，或是在擴充功能執行階段將指令碼提供給 Azure 入口網站。 「自訂指令碼擴充功能」會與 Azure Resource Manager 範本整合，您也可以使用 Azure CLI、PowerShell、Azure 入口網站或「Azure 虛擬機器 REST API」來執行它。

本文件詳細說明如何透過 Azure PowerShell 模組、Azure Resource Manager 範本使用自訂指令碼擴充功能，同時也詳細說明 Windows 系統上的疑難排解步驟。

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統

您可以對 Windows 10 Client、Windows Server 2008 R2、2012、2012 R2 和 2016 版執行適用於 Windows 的自訂指令碼擴充功能。

### <a name="script-location"></a>指令碼位置

指令碼必須儲存在 Azure Blob 儲存體或任何可透過有效 URL 存取的其他位置。

### <a name="internet-connectivity"></a>網際網路連線

適用於 Windows 的自訂指令碼擴充功能會要求目標虛擬機器連接到網際網路。 

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示自訂指令碼擴充功能的結構描述。 擴充功能需要指令碼位置 (Azure 儲存體或其他具有有效 URL 的位置)，以及可供執行的命令。 如果使用 Azure 儲存體做為指令碼來源，則需要 Azure 儲存體帳戶名稱和帳戶金鑰。 這些項目應被視為敏感性資料，並在擴充功能保護的設定組態中指定。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| 類型 | 擴充功能 |
| typeHandlerVersion | 1.9 |
| fileUris (例如) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (例如) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName (例如) | examplestorageacct |
| storageAccountKey (例如) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

**注意** - 屬性名稱會區分大小寫。 使用如上所示的名稱以避免發生部署問題。

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節詳述的 JSON 結構描述可以用於 Azure Resource Manager 範本，以在 Azure Resource Manager 範本部署期間執行自訂指令碼擴充功能。 在 [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows) 可以找到包含自訂指令碼擴充功能的範例範本。

## <a name="powershell-deployment"></a>PowerShell 部署

`Set-AzureRmVMCustomScriptExtension` 命令可以用來將自訂指令碼擴充功能新增至現有的虛擬機器。 如需詳細資訊，請參閱 [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension)。
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure PowerShell 模組，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請執行下列命令。

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

擴充功能執行輸出會記錄至目標虛擬機器上下列目錄中的檔案。
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定檔案會下載至目標虛擬機器上的下列目錄之中。
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
其中 `<n>` 是十進位整數，可能會在擴充功能的執行之間變更。  `1.*` 值符合擴充功能目前實際的 `typeHandlerVersion` 值。  例如，實際的目錄可能是 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`。  

執行 `commandToExecute` 命令時，擴充功能會將此目錄 (例如 `...\Downloads\2`) 設定為目前的工作目錄。 這可讓您使用相對路徑找出透過 `fileURIs` 屬性下載的檔案位置。 如需範例，請參閱下表。

由於絕對下載路徑會隨時間而改變，最好盡可能在 `commandToExecute` 字串中選擇相對的指令碼/檔案路徑。 例如：
```json
    "commandToExecute": "powershell.exe . . . -File './scripts/myscript.ps1'"
```

第一個 URI 區段後的路徑資訊會為透過 `fileUris` 屬性清單下載的檔案而保留。  如下表所示，下載的檔案會對應到下載的子目錄，以反映 `fileUris` 值結構。  

#### <a name="examples-of-downloaded-files"></a>下載檔案的範例

| fileUris 中的 URI | 相對下載位置 | 絕對下載位置 * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* 如同上述，絕對目錄路徑會隨 VM 生命週期而變更，但不會在 CustomScript 擴充的單次執行期間。

### <a name="support"></a>支援

如果關於本文有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇] 上的 Azure 專家 (https://azure.microsoft.com/en-us/support/forums/)。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/en-us/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/en-us/support/faq/)。
