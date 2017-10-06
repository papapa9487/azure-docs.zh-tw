---
title: "Runbook 設定 |Microsoft Docs"
description: "描述 Azure 自動化中 Runbook 的組態設定，以及如何使用 Azure 管理入口網站和 Windows PowerShell 來加以變更。"
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
ms.assetid: a726f20c-a952-48b8-88ee-36d76aa3ac61
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: ac8b5372aa06eac8c9a701f0621dbb675fbb565c
ms.openlocfilehash: 20ecbc270e61d234e026e6ba2634c7aad63b3355
ms.contentlocale: zh-tw
ms.lasthandoff: 11/17/2016

---
# <a name="runbook-settings"></a>Runbook 設定
Azure 自動化中的每個 Runbook 具備多個有助於識別其本身及變更其記錄行為的設定。 以下會說明這些設定，後面則是如何加以修改的程序。

## <a name="settings"></a>Settings
### <a name="name-and-description"></a>名稱和描述
建立 Runbook 之後，您即無法變更其名稱。 描述是選擇性的，而且最多可以是 512 個字元。

### <a name="tags"></a>標記
標記可讓您指派不同的單字和片語，有助於識別 Runbook。 例如，將 Runbook 提交到 [PowerShell 資源庫](https://www.powershellgallery.com/)時，您會指定特定的標記來識別應該列出此 Runbook 的分類。 您可以為 Runbook 指定多個標記，使用逗號分隔。

### <a name="logging"></a>記錄
依預設，詳細資訊和進度記錄不會寫入工作歷程記錄。 您可以變更特定 Runbook 的設定來記錄這些記錄。 如需有關這些記錄的詳細資訊，請參閱 [Runbook 輸出和訊息](automation-runbook-output-and-messages.md)。

## <a name="changing-runbook-settings"></a>變更 Runbook 設定

### <a name="changing-runbook-settings-with-the-azure-portal"></a>使用 Azure 入口網站變更 Runbook 設定
您可以在 Azure 入口網站中 Runbook 的 [設定] 刀鋒視窗上變更 Runbook 的設定。

1. 在 Azure 入口網站中，選取 [ **自動化** ]，然後按一下自動化帳戶的名稱。
2. 選取 [ **Runbook** ] 索引標籤。
3. 按一下 Runbook 的名稱，然後系統會將您導向至該 Runbook 的 [設定] 刀鋒視窗。 您可以在此處指定或修改標記、Runbook 描述、設定記錄和追蹤設定，以及存取支援工具來協助您解決問題。     

### <a name="changing-runbook-settings-with-windows-powershell"></a>使用 Windows PowerShell 變更 Runbook 設定
您可以使用 [Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) Cmdlet 來變更 Runbook 的設定。 如果您想要指定多個標記，可以對標記參數提供陣列或單一字串 (使用逗號分隔值)。 您可以使用 [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx) 來取得目前的標記。

下列命令範例示範如何設定 Runbook 的屬性。 此範例會將三個標記加入至現有的標記，並指定應該記錄詳細記錄。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>後續步驟
* 若要了解如何建立及擷取 Runbook 的輸出與錯誤訊息，請參閱 [Runbook 輸出與訊息](automation-runbook-output-and-messages.md) 
* 若要了解如何新增社群或其他來源已經開發的 Runbook，或建立您自己的 Runbook，請參閱[建立或匯入 Runbook](automation-creating-importing-runbook.md) 


