---
title: "在 Azure 自動化中建立或匯入 Runbook"
description: "本文說明如何在 Azure 自動化中建立新的 Runbook，或從檔案匯入新的 Runbook。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 24414362-b690-4474-8ca7-df18e30fc31d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 78df606568b941cf72b823c3715fefce1540c790
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>在 Azure 自動化中建立或匯入 Runbook
您可以將 Runbook 新增至 Azure 自動化，方法是[建立新的](#creating-a-new-runbook)，或是從檔案或 [Runbook 資源庫](automation-runbook-gallery.md)匯入現有 Runbook。 本文提供從檔案建立和匯入 Runbook 的資訊。  您可以在 [Azure 自動化的 Runbook 和模組資源庫](automation-runbook-gallery.md)中取得有關存取社群 Runbook 和模組的所有詳細資料。

## <a name="creating-a-new-runbook"></a>建立新的 Runbook
您可以使用其中一個 Azure 入口網站或 Windows PowerShell，在 Azure 自動化中建立新的 Runbook。 一旦建立 Runbook 之後，您就能使用[了解 PowerShell 工作流程](automation-powershell-workflow.md)和 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)中的資訊來編輯它。

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>使用 Azure 傳統入口網站建立新的 Azure 自動化 Runbook
您只能在 Azure 入口網站中使用 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 。

1. 在 Azure 傳統入口網站中，按一下 [新增]、[應用程式服務]、[自動化]、[Runbook]、[快速建立]。
2. 輸入必要資訊，然後按一下建立 。 Runbook 名稱必須以字母開頭，可以具有字母、數字、底線和連字號。
3. 如果您想要立即編輯 Runbook，則按一下 [編輯 Runbook] 。 否則，請按一下 [確定] 。
4. 新的 Runbook 會出現在 [Runbook] 索引標籤上。

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>使用 Azure 入口網站建立新的 Azure 自動化 Runbook
1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 從 [中樞] 選取 [Runbook] 以開啟 Runbook 清單。
3. 按一下 [加入 Runbook] 按鈕，然後按一下 [建立新的 Runbook]。
4. 輸入 Runbook 的 [名稱]  ，然後選取其 [類型](automation-runbook-types.md)。 Runbook 名稱必須以字母開頭，可以具有字母、數字、底線和連字號。
5. 按一下 [建立]  來建立 Runbook 並開啟編輯器。

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>使用 Windows PowerShell 建立新的 Azure 自動化 Runbook
您可以使用 [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) Cmdlet，建立空的 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 您可以指定 **Name** 參數來建立空白 Runbook，您稍後可加以編輯，或者您可以指定 **Path** 參數以匯入 Runbook 檔案。 您也應該包含 **Type** 參數來指定這四個 Runbook 類型其中之一。

下列範例命令顯示如何建立新的空白 Runbook。

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>將 Runbook 從檔案匯入 Azure 自動化
您可以在 Azure 自動化中建立新的 Runbook，方法是匯入 PowerShell 指令碼或 PowerShell 工作流程 (.ps1 延伸模組)，或匯入已匯出的圖形化 Runbook (.graphrunbook)。  您必須指定匯入期間建立的 [Runbook 類型](automation-runbook-types.md)，並考量下列事項。

* .graphrunbook 檔案只能匯入至新的 [圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)，圖形化 Runbook 只能從 .graphrunbook 檔案建立。
* 包含 PowerShell 工作流程的 .ps1 檔案只能匯入至 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。  如果檔案包含多個 PowerShell 工作流程，則匯入將會失敗。 您必須將每個工作流程儲存到它們各自的檔案，並且個別匯入。
* 未包含工作流程的 .ps1 檔案可以匯入至 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 或 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。  如果是匯入 PowerShell 工作流程 Runbook 中，則會轉換為工作流程，而且 Runbook 會包含註解來指明所做的變更。

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>使用 Azure 傳統入口網站從檔案匯入 Runbook
您可以使用下列程序，將指令碼檔案匯入到 Azure 自動化。  請注意，您只能使用此入口網站將 .ps1 檔案匯入 PowerShell 工作流程 Runbook。  對於其他類型，您必須使用 Azure 入口網站。

1. 在 Azure 傳統入口網站中，選取 [自動化]，然後選取自動化帳戶。
2. 按一下 [匯入] 。
3. 按一下 [瀏覽檔案]  並找出要匯入的指令碼檔案。
4. 如果您想要立即編輯 Runbook，則按一下 [編輯 Runbook] 。 否則，請按一下 [確定]。
5. 新的 Runbook 會出現在自動化帳戶的 [Runbook] 索引標籤上。
6. 您必須 [發佈 Runbook](#publishing-a-runbook) ，才能執行。

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>使用 Azure 入口網站從檔案匯入 Runbook
您可以使用下列程序，將指令碼檔案匯入到 Azure 自動化。  

> [!NOTE]
> 請注意，您只能使用入口網站，將 .ps1 檔案匯入 PowerShell 工作流程 Runbook。
> 
> 

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 從 [中樞] 選取 [Runbook] 以開啟 Runbook 清單。
3. 按一下 [加入 Runbook] 按鈕，然後按一下 [匯入]。
4. 按一下 [Runbook 檔案]  以選取要匯入的檔案
5. 如果 [名稱]  欄位已啟用，則您可以選擇變更它。  Runbook 名稱必須以字母開頭，可以具有字母、數字、底線和連字號。
6. [Runbook 類型](automation-runbook-types.md) 會自動選取，但在考量適用的限制之後，您可以變更類型。 
7. 新的 Runbook 會出現在自動化帳戶的 Runbook 清單中。
8. 您必須 [發佈 Runbook](#publishing-a-runbook) ，才能執行。

> [!NOTE]
> 當您匯入圖形化 Runbook 或圖形化 PowerShell 工作流程 Runbook 之後，您可以視需要選擇轉換成另一種類型。 無法轉換為文字 Runbook。
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>使用 Windows PowerShell 從指令碼檔案匯入 Runbook
您可以使用 [Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) Cmdlet，來匯入為指令碼檔案以做為草稿 PowerShell 工作流程 Runbook。 如果 Runbook 已經存在，除非您使用 *-Force* 參數，否則匯入會失敗。 

以下範例命令示範如何將指令碼檔案匯入 Runbook。

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>發佈 Runbook
當您建立或匯入新的 Runbook 時，您必須發佈才能執行它。  自動化中的每個 Runbook 都有草稿和已發行的版本。 只可執行已發行版本，而且只可編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 草稿版本應該已可供使用時，您會將它發佈，以草稿版本覆寫已發佈版本。

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>使用 Azure 傳統入口網站發佈 Runbook
1. 在 Azure 傳統入口網站中開啟 Runbook。
2. 在畫面頂端按一下 [撰寫] 。
3. 在畫面底部按一下 [發佈]，然後對驗證訊息按一下 [是]。

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>使用 Azure 入口網站發佈 Runbook
1. 在 Azure 入口網站中開啟 Runbook。
2. 按一下 [ **編輯** ] 按鈕。
3. 按一下 [發佈] 按鈕，然後對驗證訊息按一下 [是]。

## <a name="to-publish-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 發佈 Runbook
您可以使用 [Publish-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) Cmdlet，利用 Windows PowerShell 發佈 Runbook。 下列範例命令顯示如何發佈範例 Runbook。

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>後續步驟
* 若要了解如何從 Runbook 和 PowerShell 模組資源庫中受益，請參閱 [Azure 自動化的 Runbook 和模組資源庫](automation-runbook-gallery.md)
* 若要深入了解使用文字編輯器編輯 PowerShell 和 PowerShell 工作流程 Runbook，請參閱 [在 Azure 自動化中編輯文字式 Runbook](automation-edit-textual-runbook.md)
* 若要深入了解如何編寫圖形化 Runbook，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)

