---
title: "適用於 Windows 的 Azure 效能診斷 VM 擴充功能 |Microsoft Docs"
description: "介紹適用於 Windows 的 Azure 效能診斷 VM 擴充功能。"
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: b521b4cae29578798247921331d98fc5077ad266
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>適用於 Windows 的 Azure 效能診斷 VM 擴充功能

## <a name="summary"></a>摘要
Azure 效能診斷 VM 擴充功能可協助從 Windows VM 收集效能診斷資料，執行分析和提供結果與建議報告，以識別並解決虛擬機器上的效能問題。 此擴充功能會安裝名為 [PerfInsights](http://aka.ms/perfinsights) 的疑難排解工具。

## <a name="prerequisites"></a>必要條件
### <a name="operating-systems"></a>作業系統
此擴充功能可以安裝在 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016、Windows 8.1 和 Windows 10 作業系統上。

## <a name="extension-schema"></a>擴充功能結構描述
下列 JSON 顯示 Azure 效能診斷擴充功能的結構描述。 此擴充功能需要儲存體帳戶的名稱與金鑰，才能儲存診斷輸出與報告。 這些都是機密值，並應該儲存在受保護的設定組態內。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。 請注意，storageAccountName 及 storageAccountKey 須區分大小寫。 其他必要的參數會列在下一節中。

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>屬性值

|   **名稱**   |**值 / 範例**|       **說明**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|API 的版本
|publisher|Microsoft.Azure.Performance.Diagnostics|擴充功能的發行者命名空間
|類型|AzurePerformanceDiagnostics|VM 擴充功能的類型
|typeHandlerVersion|1.0|擴充功能處理常式的版本
|performanceScenario|basic|要擷取資料的效能案例。 有效值為：**basic**、**vmslow**、**azurefiles** 及 **custom**。
|traceDurationInSeconds|300|追蹤的持續時間 (若有選取任何追蹤選項)。
|DiagnosticsTrace|d|啟用診斷追蹤的選項。 有效值為 **d** 或空值。 如果您不想要擷取此追蹤，只要將值保持空白即可。
|perfCounterTrace|p|啟用效能計數器追蹤的選項。 有效值為 **p** 或空值。 如果您不想要擷取此追蹤，只要將值保持空白即可。
|networkTrace|n|啟用 Netmon 追蹤的選項。 有效值為 **n** 或空值。 如果您不想要擷取此追蹤，只要將值保持空白即可。
|xperfTrace|x|啟用 XPerf 追蹤的選項。 有效值為 **x** 或空值。 如果您不想要擷取此追蹤，只要將值保持空白即可。
|storPortTrace|s|啟用 StorPort 追蹤的選項。 有效值為 s 或空值。 如果您不想要擷取此追蹤，只要將值保持空白即可。
|srNumber|123452016365929|支援票證號碼 (若可用)。 如果您沒有此號碼，請保持空白。
|requestTimeUtc|9/2/2017 11:06:00 PM|目前的日期時間 (UTC)。 如果您使用入口網站安裝此擴充功能，便不需要提供此值。
|storageAccountName|mystorageaccount|要儲存診斷記錄檔和結果的儲存體帳戶名稱。
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|儲存體帳戶的金鑰。

## <a name="install-the-extension"></a>安裝擴充功能

請遵循下列步驟以在 Windows 虛擬機器上安裝 VM 擴充功能：

1. 登入 [Azure 入口網站](http://portal.azure.com)。
2. 選取您要安裝此擴充功能的虛擬機器。

    ![選取虛擬機器](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. 選取 [擴充功能] 刀鋒視窗，然後按一下 [新增] 按鈕。

    ![選取擴充功能](media/performance-diagnostics-vm-extension/select-extensions.png)
4. 選取 [Azure 效能診斷] 擴充功能，檢閱條款及條件，然後按一下 [建立] 按鈕。

    ![建立 Azure 效能診斷 VM 擴充功能](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. 提供安裝的參數值，然後按一下 [確定] 以安裝擴充功能。 您可以在[這裡](how-to-use-perfInsights.md#supported-troubleshooting-scenarios)找到支援疑難排解案例的詳細資訊。 

    ![安裝擴充功能](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. 安裝成功之後，您會看到指出佈建成功的訊息。

    ![佈建成功訊息](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > 佈建成功之後，擴充功能便會開始執行，並需要幾分鐘的時間以完成基本案例的執行。 對於其他案例，它會在於安裝期間所指定的持續時間內持續執行。

## <a name="remove-the-extension"></a>移除擴充功能
若要從虛擬機器移除擴充功能，請遵循下列步驟：

1. 登入 [Azure 入口網站](http://portal.azure.com)，選取您要移除此擴充功能的虛擬機器，然後選取 [擴充功能] 刀鋒視窗。 
2. 在清單中按一下效能診斷擴充功能項目的 (**...**)，然後選取 [解除安裝]。

    ![解除安裝擴充功能](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > 您也可以選取擴充功能項目，然後選取 [解除安裝] 選項。

## <a name="template-deployment"></a>範本部署
也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節詳述的 JSON 結構描述可以用於 Azure Resource Manager 範本，以在 Azure Resource Manager 範本部署期間執行 Azure 效能診斷擴充功能。 以下範例範本可以和範本部署搭配使用：

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "diagnosticsTrace": {
      "type": "string",
      "defaultValue": "d"
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell 部署
`Set-AzureRmVMExtension` 命令可以用來將 Azure 效能診斷虛擬機器擴充功能部署到現有的虛擬機器。 執行命令之前，必須將公用和私人組態儲存在 PowerShell 雜湊表中。

PowerShell

````
$PublicSettings = @{ "performanceScenario":"basic","traceDurationInSeconds":300,"diagnosticsTrace":"d","perfCounterTrace":"p","networkTrace":"","xperfTrace":"","storPortTrace":"","srNumber":"","requestTimeUtc":"2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName":"mystorageaccount","storageAccountKey":"mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>所擷取資料的詳細資訊
PerfInsights 工具會根據所選取的案例，收集各種記錄檔、設定、診斷資料等。 如需根據案例所收集之資料的詳細資訊，請造訪 [PerfInsights 文件](http://aka.ms/perfinsights)。

## <a name="view-and-share-the-results"></a>檢視並共用結果

擴充功能的輸出預設會儲存在暫存磁碟機下名為 log_collection 的資料夾內 (通常為 D:\log_collection)。 在此資料夾中，您可以看到包含診斷記錄檔和含有結果與建議報告的 zip 檔案。

建立的 zip 檔案也會上傳至於安裝期間提供的儲存體帳戶，並使用[共用存取簽章 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 共用 30 天。 名為 *zipfilename*_saslink.txt 的文字檔也會在 log_collection 資料夾中建立。 此檔案包含建立用來下載 zip 檔案的 SAS 連結。 任何具有此連結的人員都能夠下載該 zip 檔案。

Microsoft 可能會使用此 SAS 連結來下載診斷資料，以由處理您支援票證的支援工程師進一步調查。

若要檢視報告，請將 zip 檔案解壓縮，然後開啟 **PerfInsights Report.html** 檔案。

您也可以選取擴充功能來直接從入口網站下載 zip 檔案。

![檢視詳細狀態](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> 入口網站中所顯示的 SAS 連結，有時會因為在編碼和解碼作業期間所產生的格式錯誤 URL (由特殊字元造成) 而無法運作。 因應措施是直接從 VM 的 *_saslink.txt 檔案取得該連結。

## <a name="troubleshoot-and-support"></a>疑難排解與支援
### <a name="troubleshoot"></a>疑難排解

- 即使已成功佈建擴充功能，擴充功能部署狀態 (位於通知區域中) 還是有可能會顯示為「部署進行中」。

    只要擴充功能狀態表示已成功佈建擴充功能，就可以放心地忽略這個問題。
- 在安裝期間發生的一些問題，可以使用擴充功能記錄檔進行疑難排解。 擴充功能執行輸出會記錄至下列目錄中的檔案︰

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。