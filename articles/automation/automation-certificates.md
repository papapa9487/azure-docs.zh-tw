---
title: "Azure 自動化中的憑證資產 | Microsoft Docs"
description: "憑證可以安全地儲存在 Azure 自動化中，使得 Runbook 或 DSC 組態可以存取憑證，以向 Azure 和協力廠商資源進行驗證。  這篇文章說明憑證的詳細資料，以及如何以文字和圖形化編寫形式加以使用。"
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 7082f0c4b1a4cf0f67da5254b4ebb019c7299683
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---

# <a name="certificate-assets-in-azure-automation"></a>Azure 自動化中的憑證資產

憑證可以安全地儲存在 Azure 自動化中，使得 Runbook 或 DSC 組態可以透過使用 Azure Resource Manager 資源的 **Get-AzureRmAutomationCertificate** 活動來存取憑證。 這可讓您建立使用憑證進行驗證的 Runbook 和 DSC 組態，或將它們新增至 Azure 或協力廠商的資源。

> [!NOTE] 
> Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一索引鍵儲存在 Azure 自動化中。 這個索引鍵是由主要憑證加密，並且儲存在 Azure 自動化中。 儲存安全資產之前，會使用主要憑證解密自動化帳戶的金鑰，然後用來加密資產。
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell Cmdlet

下表中的 Cmdlet 是用來透過 Windows PowerShell 建立和管理自動化憑證資產。 它們是隨著 [Azure PowerShell 模組](../powershell-install-configure.md) 的一部分推出，可供在自動化 Runbook 和 DSC 設定中使用。

|Cmdlet|說明|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-4.3.1)|擷取要在 Runbook 或 DSC 組態中使用的憑證相關資訊。 您只能從 Get-AutomationCertificate 活動擷取憑證本身。|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate?view=azurermps-4.3.1)|建立新的憑證到 Azure 自動化。|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate?view=azurermps-4.3.1)|從 Azure 自動化中移除憑證。|建立新的憑證到 Azure 自動化。
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate?view=azurermps-4.3.1)|設定現有的憑證，包括上傳憑證檔案和設定 .pfx 的密碼屬性。|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|上傳指定雲端服務的服務憑證。|


## <a name="python2-functions"></a>Python2 函式

下表中的函式用於存取 Python2 Runbook 中的憑證。

| 函式 | 說明 |
|:---|:---|
| automationassets.get_automation_certificate | 擷取憑證資產的相關資訊。 |

> [!NOTE]
> 您必須在 Python Runbook 的頂端匯入 **automationassets** 模組，才能存取資產函式。


## <a name="creating-a-new-certificate"></a>建立新憑證

建立新憑證時，您會將 cer 或 pfx 檔案上傳到 Azure 自動化。 如果您將憑證標示為可匯出，那麼您可以將它傳送到 Azure 自動化憑證存放區外部。 如果無法匯出，則只可以將它用在 Runbook 或 DSC 組態內的簽署。


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 入口網站建立新憑證

1. 從您的自動化帳戶，按一下 [資產] 圖格，以開啟 [資產] 刀鋒視窗。
1. 按一下 [憑證] 憑證，以開啟 [憑證] 刀鋒視窗。
1. 在分頁的頂端按一下 [ **加入憑證** ]。
2. 在 [ **名稱** ] 方塊中輸入憑證的名稱。
2. 按一下 [上傳憑證檔案] 下方的 [選取檔案]，以瀏覽 .cer 或 .pfx 檔案。  如果您選取 .pfx 檔案，請指定密碼，以及是否應該允許匯出它。
1. 按一下 [ **建立** ] 以儲存新的憑證資產。


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>使用 Windows PowerShell 建立新憑證

下列範例示範如何建立新的自動化憑證，並將其標示為可匯出。 這樣會匯入現有的 pfx 檔案。

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>使用憑證

您必須使用 **Get-AutomationCertificate** 活動以使用憑證。 您不能使用 [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) Cmdlet，因為它會傳回憑證資產的相關資訊，而不是憑證本身。

### <a name="textual-runbook-sample"></a>文字式 Runbook 範例

下列範例程式碼示範如何將憑證新增到 Runbook 中的雲端服務。 在此範例中，密碼是擷取自加密的自動化變數。

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>圖形化 Runbook 範例

透過在圖形化編輯器 [文件庫] 窗格的憑證上按一下滑鼠右鍵，然後選取 [加入至畫布]，即可加入 **Get-AutomationCertificate** 至圖形化 Runbook。

![將憑證新增至畫布](media/automation-certificates/automation-certificate-add-to-canvas.png)

下圖顯示在圖形化 Runbook 中使用憑證的範例。  這是如上所示，用於從文字式 Runbook 加入憑證至雲端服務的相同範例。

![範例圖形化編寫 ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 範例
下列範例示範如何存取 Python2 Runbook 中的憑證。

    # get a reference to the Azure Automation certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    
    # returns the binary cert content  
    print cert 

## <a name="next-steps"></a>後續步驟

- 若要深入了解使用連結控制您的 Runbook 設計用來執行的活動之邏輯流程，請參閱[圖形化編寫中的連結](automation-graphical-authoring-intro.md#links-and-workflow)。 

