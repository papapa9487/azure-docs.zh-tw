---
title: "使用「範本驗證程式」來檢查 Azure Stack 的範本 | Microsoft Docs"
description: "檢查要部署到 Azure Stack 的範本"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c99e5ebc2612e10f42bddbbd2f1c17d7404305d3
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>使用「範本驗證程式」來檢查 Azure Stack 的範本

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

您可以使用範本驗證工具來檢查您的 Azure Resource Manager [範本](azure-stack-arm-templates.md)是否準備好供 Azure Stack 使用。 範本驗證工具是 Azure Stack 工具的一部分。 使用[從 GitHub 下載工具](azure-stack-powershell-download.md)文章中所述的步驟下載 Azure Stack 工具。 

若要驗證範本，您必須使用位於 [TemplateValidator] 與 [CloudCapabilities] 資料夾的下列 PowerShell 模組與 JSON 檔案： 

 - AzureRM.CloudCapabilities.psm1 會建立雲端功能 JSON 檔案，該檔案代表雲端 (例如 Azure Stack) 中的服務與版本。
 - AzureRM.TemplateValidator.psm1 使用雲端功能 JSON 檔案來測試將在 Azure Stack 中部署的範本。
 - AzureStackCloudCapabilities_with_AddOns_20170627.json 是預設的雲端功能檔案。  您可以自行建立檔案，或從此檔案開始。 

在此主題中，您會為您的範本執行驗證，而且可以選擇性建置雲端功能檔案。

## <a name="validate-templates"></a>驗證範本
在這些步驟中，您會使用 AzureRM.TemplateValidator PowerShell 模組來驗證範本。 您可以使用您自己的範本，或驗證 [Azure Stack 快速入門範本](https://github.com/Azure/AzureStack-QuickStart-Templates)。

1.  匯入 AzureRM.TemplateValidator.psm1 PowerShell 模組：
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  執行範本驗證程式：

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

所有範本驗證警告或錯誤都會記錄到 PowerShell 主控台，而且也會記錄到來源目錄中的 HTML 檔案。 驗證報告輸出的範例看起來像這樣：

![範例驗證報告](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>參數

| 參數 | 說明 | 必要 |
| ----- | -----| ----- |
| TemplatePath | 指定要在其中遞迴尋找 Resource Manager 範本的路徑 | 是 | 
| TemplatePattern | 指定要比對的範本檔案名稱。 | 否 |
| CapabilitiesPath | 指定雲端功能 JSON 檔案的路徑 | 是 | 
| IncludeComputeCapabilities | 包括 IaaS 資源 (例如 VM 大小與 VM 擴充功能) 的評估 | 否 |
| IncludeStorageCapabilities | 包括儲存體資源 (例如 SKU 類型) 的評估 | 否 |
| 報告 | 指定所產生之 HTML 報告的名稱 | 否 |
| 詳細資訊 | 將錯誤與警告記錄到主控台 | 否|


### <a name="examples"></a>範例
此範例會驗證所有下載到本機的 [Azure Stack 快速入門範本](https://github.com/Azure/AzureStack-QuickStart-Templates)，而且也會驗證 VM 大小與擴充功能是否符合 Azure Stack 開發套件功能。

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="build-cloud-capabilities-file"></a>建置雲端功能檔案
下載的檔案包括預設的 *AzureStackCloudCapabilities_with_AddOns_20170627.json* 檔案，此檔案描述 Azure Stack 開發套件中可用的服務版本 (在已安裝 PaaS 服務的情況下)。  若您安裝其他資源提供者，您可以使用 AzureRM.CloudCapabilities PowerShell 模組來建置 JSON 檔案 (包括新服務)。  

1.  確定您能順利連線到 Azure Stack。  這些步驟可從 Azure Stack 開發套件主機執行，或者您可以使用 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 從您的工作站連線。 
2.  匯入 AzureRM.CloudCapabilities PowerShell 模組：

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  使用 Get-CloudCapabilities Cmdlet 來擷取服務版本並建立雲端功能 JSON 檔案：

    ```PowerShell
    Get-AzureRMCloudCapabilities -Location 'local' -Verbose
    ```             


## <a name="next-steps"></a>後續步驟
 - [將範本部署到 Azure Stack](azure-stack-arm-templates.md)
 - [開發適用於 Azure Stack 的範本](azure-stack-develop-templates.md)


