---
title: "Azure 自動化中的 Runbook 執行 | Microsoft Docs"
description: "描述如何處理 Azure 自動化中的 Runbook 的詳細資料。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: d10c8ce2-2c0b-4ea7-ba3c-d20e09b2c9ca
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/17/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 14f923e3f08dd3b286218ae56012ce14edcc4058
ms.contentlocale: zh-tw
ms.lasthandoff: 08/19/2017

---
# <a name="runbook-execution-in-azure-automation"></a>Azure 自動化中的 Runbook 執行
當您在 Azure 自動化中啟動 Runbook 時即會建立一個工作。 工作是 Runbook 的單一執行個體。 會指派 Azure 自動化背景工作，以執行每項工作。 雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。 您無法控制哪個背景工作要為您的作業要求提供服務。  單一 Runbook 一次可以執行多個工作。 當您在 Azure 入口網站中檢視 Runbook 清單時，它會列出已針對每個 Runbook 啟動之所有作業的狀態。 您可以檢視每一個 Runbook工作的清單，以追蹤每個 Runbook 的狀態。 如需不同的工作狀態的描述，請參閱 [工作狀態](#job-statuses)。

下圖顯示[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks) 和 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 之 Runbook 作業的生命週期。

![工作狀態 -PowerShell 工作流程](./media/automation-runbook-execution/job-statuses.png)

下圖顯示 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)之 Runbook 工作的生命週期。

![工作狀態 - PowerShell 指令碼](./media/automation-runbook-execution/job-statuses-script.png)

藉由連線至您的 Azure 訂用帳戶，您的作業即可存取您的 Azure 資源。 如果可從公用雲端存取這些資源，他們將只可以存取您的資料中心中的資源。

## <a name="job-statuses"></a>工作狀態
下表描述工作可能會有不同的狀態。

| Status | 說明 |
|:--- |:--- |
| Completed |工作已成功完成。 |
| Failed |針對 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md)，此 Runbook 無法編譯。  針對 [PowerShell 指令碼 Runbook](automation-runbook-types.md)，此 Runbook 無法啟動，或工作中發生例外狀況。 |
| 處理失敗，正在等候資源 |工作失敗，因為其達到 [公平共用](#fairshare) 的三次上限，且每次從相同的檢查點或啟動 Runbook 開始。 |
| 已排入佇列 |工作正在等候取得自動化背景工作中的資源，以便可啟動。 |
| 啟動中 |工作已指派給背景工作，並且系統正在進行啟動。 |
| 繼續中 |工作暫停後，系統正在繼續工作。 |
| 執行中 |工作正在執行。 |
| 執行中，正在等候資源 |工作已卸載，因為已達到 [公平共用](#fairshare) 上限。 作業很快會從其上一個檢查點繼續。 |
| 已停止 |工作完成之前已由使用者停止。 |
| 停止中 |系統正在停止工作。 |
| 暫止 |工作已由使用者、系統或 Runbook 中的命令暫停。 已暫停的作業可以再次啟動，並從其上一個檢查點，或從 Runbook 的開頭繼續 (若無檢查點)。 只有在發生例外狀況時，系統才會暫停 Runbook。 根據預設，ErrorActionPreference 會設定為 [繼續]，代表作業會在發生錯誤時繼續執行。 如果此喜好設定變數設定為 [停止]，作業會在發生錯誤時暫停。  只適用於 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md) 。 |
| 暫停中 |因使用者要求，系統正在嘗試暫停工作。 Runbook 必須達到其下一個檢查點才能暫停。 如果它已通過其最後一個檢查點，則可在暫停之前完成。  只適用於 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md) 。 |

## <a name="viewing-job-status-from-the-azure-portal"></a>從 Azure 入口網站檢視作業狀態
您可以檢視所有 Runbook 作業的摘要狀態，或在 Azure 入口網站中向下切入至特定 Runbook 作業的詳細資訊，或者藉由設定與您 Microsoft Operations Management Suite (OMS) Log Analytics 工作區的整合以轉送 Runbook 作業狀態和作業串流來加以檢視。  如需與 OMS Log Analytics 整合的詳細資訊，請參閱[從自動化將作業狀態和作業串流轉送到 Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md)。  

### <a name="automation-runbook-jobs-summary"></a>自動化 Runbook 作業摘要
在所選自動化帳戶的右邊，您可以在 [作業統計資料] 圖格下方，看到所選自動化帳戶的所有 Runbook 作業摘要。<br><br> ![[作業統計資料] 圖格](./media/automation-runbook-execution/automation-account-job-status-summary.png)。<br> 這個圖格會針對所有已執行的作業顯示作業狀態的計數和圖形表示。  

按一下圖格，即會顯示 [作業] 刀鋒視窗，其中包含所有已執行作業的摘要清單，以及狀態、作業執行和開始與完成時間。<br><br> ![自動化帳戶的 [作業] 刀鋒視窗](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)<br><br>  您可以篩選作業清單，方法是選取 [篩選作業]，然後篩選特定的 Runbook、作業狀態，或從下拉式清單中，選取要在其中進行搜尋的日期/時間範圍。<br><br> ![篩選作業狀態](./media/automation-runbook-execution/automation-account-jobs-filter.png)

或者，您可以檢視特定 Runbook 的作業摘要詳細資料，方法是在您的自動化帳戶中，從 [Runbook] 刀鋒視窗中選取該 Runbook，然後選取 [作業] 圖格。  這會顯示 [作業] 刀鋒視窗，而您可以從此處按一下作業記錄，以檢視其詳細資料和輸出。<br><br> ![自動化帳戶的 [作業] 刀鋒視窗](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)<br> 

### <a name="job-summary"></a>工作摘要
您可以針對特定 Runbook 及其最新狀態，檢視所有已建立工作的清單。 您可以依工作狀態和上次工作變更的日期範圍來篩選此清單。 若要檢視其詳細資訊和輸出，請按一下作業的名稱。 工作的詳細檢視包含提供給該工作的 Runbook 參數值。

您可以使用下列步驟來檢視 Runbook 工作。

1. 在 Azure 入口網站中，選取 [自動化]，然後選取自動化帳戶的名稱。
2. 從中樞選取 [Runbook]，然後在 [Runbook] 刀鋒視窗上選取清單中的 Runbook。
3. 在已選取 Runbook 的刀鋒視窗上，按一下 [作業] 圖格。
4. 按一下清單中的其中一個作業，然後在 [Runbook 作業詳細資料] 刀鋒視窗，您可以檢視其詳細資料與輸出。

## <a name="retrieving-job-status-using-windows-powershell"></a>使用 Windows PowerShell 擷取工作狀態
您可以使用 [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)，來擷取針對 Runbook 建立的作業以及特定作業的詳細資料。 如果您使用 [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx)，透過 Windows PowerShell 來啟動 Runbook，則其會傳回產生的作業。 使用 [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)Output 來取得作業的輸出。

下列範例命令會針對範例 Runbook 擷取上一個作業並顯示其狀態、提供給 Runbook 參數的值，以及來自作業的輸出。

    $job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
    –RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
    $job.Status
    $job.JobParameters
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output

## <a name="fair-share"></a>公平共用
為了在雲端中的所有 Runbook 之間共用資源，「Azure 自動化」會在任何作業已執行 3 小時之後將其暫時卸載。  在此期間，[以 PowerShell 為基礎的 Runbook](automation-runbook-types.md#powershell-runbooks) 的作業會停止，而且不會重新啟動。  作業狀態顯示 [已停止]。  這類型的 Runbook 一律會從頭重新啟動，因為它們不支援檢查點。  

[以 PowerShell 工作流程為基礎的 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 將會從其最後一個[檢查點](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints)繼續。  執行三個小時之後，服務將會暫停 Runbook 作業，而其狀態顯示 [執行中，等待資源]。  當沙箱可以使用時，自動化服務會自動重新啟動 Runbook 並從最後一個檢查點繼續。  這是 PowerShell 工作流程的正常暫停/重新啟動行為。  如果 Runbook 再次超過三個小時的執行階段，此程序會重複 (最多三次)。  在第三次重新啟動之後，如果 Runbook 仍未在三小時內完成，則 Runbook 作業會失敗，而作業狀態會顯示 [失敗，等待資源]。  在此情況下，您會收到下列失敗例外狀況。

*作業無法繼續執行，因為已從相同的檢查點重複收回該作業。請確定您的 Runbook 在未保存其狀態的情況下，不會長時間執行作業。*

這是為了防止服務無限期地執行 Runbook 而不會完成，因為它們在未重新卸載的情況下，無法進入下一個檢查點。

如果 Runbook 沒有檢查點，或作業在卸載之前未到達第一個檢查點，則會從頭重新啟動。  

建立 Runbook 時，您應該確定在兩個檢查點之間執行的任何活動的時間不會超過 3 小時。 您可能需要在您的 Runbook 中新增檢查點，以確保它不會達到此 3 小時的限制或中斷長時間執行的作業。 例如，您的 Runbook 可能在大型 SQL 資料庫上執行重新索引。 如果此單一作業未在公平共用的限制內完成，則會卸載作業並從頭開始重新啟動。 在此情況下，您應該將重新索引作業分成多個步驟，例如一次重新索引一個資料表，然後在每個作業之後插入檢查點，讓工作可以在最後一個作業完成後繼續。

## <a name="next-steps"></a>後續步驟
* 若要深入了解可在 Azure 自動化中用來啟動 Runbook 的不同方法，請參閱[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)


