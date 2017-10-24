---
title: "Azure 自動化中的子 Runbook | Microsoft Docs"
description: "說明在 Azure 自動化中從另一個 Runbook 啟動 Runbook，以及在它們之間共用資訊的不同方法。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 919887b9-43e2-4c16-883c-f81807fe37db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 617e18f5435c7eacb7751ccca6ac2f3814745f04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="child-runbooks-in-azure-automation"></a>Azure 自動化中的子 Runbook
Azure 自動化中的最佳作法是撰寫可重複使用、模組化的 Runbook，並包含可供其他 Runbook 使用的不同功能。 父 Runbook 通常會呼叫一或多個子 Runbook 來執行必要的功能。 有兩種方式可以呼叫子 Runbook，而且各有您應該了解的明顯差異，如此您才能判斷在不同的情況下哪一種最適用。

## <a name="invoking-a-child-runbook-using-inline-execution"></a>使用內嵌執行叫用子 Runbook
若要從其他 Runbook 叫用 Runbook 內嵌，請使用 Runbook 的名稱並為其提供參數值，如同您使用活動或 Cmdlet 時一樣。  在相同自動化帳戶中的所有 Runbook 均可以此方式提供所有其他 Runbook 使用。 父 Runbook 會等候子 Runbook 完成，才會移動到下一行，而且任何輸出都會直接回傳到父代。

當您叫用 Runbook 內嵌時，它會在父 Runbook 所在的相同工作中執行。 其執行之子 Runbook 的工作歷程記錄中不會指示。 來自子 Runbook 的任何例外狀況及任何資料流輸出都會與父代相關聯。 這會導致較少的工作，並使工作更容易追蹤和疑難排解，因為子 Runbook 擲回的任何例外狀況及其任何資料流輸出，都與父 Runbook 的工作相關聯。

發佈 Runbook 時，其呼叫的所有子 Runbook 都必須是已發佈的。 這是因為在編譯 Runbook 時，Azure 自動化會建置與任何子 Runbook 的關聯。 如果沒有，父 Runbook 會顯示正常發佈，但在啟動時會產生例外狀況。 如果發生這種情況，您可以重新發佈父 Runbook 以正確參照子 Runbook。 因為已經建立關聯，所以如果任何子 Runbook 有變更，您都不需要重新發佈父 Runbook。

以內嵌方式呼叫之子 Runbook 的參數可以是任何資料類型 (包括複雜物件)，而且沒有您使用 Azure 管理入口網站或使用 Start-AzureRmAutomationRunbook Cmdlet 啟動 Runbook 時的 [JSON 序列化](automation-starting-a-runbook.md#runbook-parameters) 。

### <a name="runbook-types"></a>Runbook 類型
哪些類型可以彼此呼叫：

* [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks) 可以內嵌方式呼叫彼此 (這兩者都是 PowerShell)。
* [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 和圖形化 PowerShell 工作流程 Runbook 可以內嵌方式呼叫彼此 (這兩者都是 PowerShell 工作流程)。
* PowerShell 類型與 PowerShell 工作流程類型無法以內嵌方式彼此呼叫，而且必須使用 Start-AzureRmAutomationRunbook。

何時會與發行順序有關：

* Runbook 的發行順序只對 PowerShell 工作流程和圖形化 PowerShell 工作流程 Runbook 有關係。

叫用使用內嵌執行的圖形化或 PowerShell 工作流程子 Runbook 時，只要使用 Runbook 名稱即可。  當您呼叫 PowerShell 子系 Runbook 時，名稱前面必須加上 *.\\* 來指定指令碼位於本機目錄。 

### <a name="example"></a>範例
下列範例會叫用一個測試子 Runbook，它會接受三個參數、一個複雜物件、一個整數和一個布林值。 子 Runbook 的輸出會指派給一個變數。  此案例的子 Runbook 是 PowerShell 工作流程 Runbook

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

以下是使用 PowerShell Runbook 做為子 Runbook 的相同範例。

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true


## <a name="starting-a-child-runbook-using-cmdlet"></a>使用 Cmdlet 啟動子 Runbook
您可以依照[使用 Windows PowerShell 啟動 Runbook](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell) 中的說明，使用 [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) Cmdlet 啟動 Runbook。 使用這個 Cmdlet 的模式有兩種。  在第一個模式中，Cmdlet 會在子 Runbook 的子作業建立時立即傳回作業識別碼。  在第二個模式 (藉由指定 **-wait** 參數啟用) 中，Cmdlet 會等候子作業完成，並且會傳回子 Runbook 的輸出。

使用 Cmdlet 啟動之子 Runbook 的工作，將會與父 Runbook 的工作分開執行。 這會使產生的作業比叫用指令碼內嵌更多，並使它們更難以困難。父 Runbook 可以利用非同步方式啟動多個子 Runbook，不需要等候每個子 Runbook 完成。 對於呼叫子 Runbook 內嵌的同類型平行執行作業，父 Runbook 將需要使用 [平行關鍵字](automation-powershell-workflow.md#parallel-processing)。

使用 Cmdlet 啟動之子 Runbook 的參數是以雜湊表方式提供，如 [Runbook 參數](automation-starting-a-runbook.md#runbook-parameters)中所述。 只能使用簡單資料類型。 若 Runbook 有複雜資料類型的參數，必須以內嵌方式呼叫。

### <a name="example"></a>範例
下列範例使用參數啟動子 Runbook，然後使用 Start-AzureRmAutomationRunbook -wait 參數等待其完成。 完成後，系統會從子 Runbook 收集其輸出。

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResourceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>子 Runbook 的呼叫方法比較
下表摘要說明從另一個 Runbook 呼叫 Runbook 之兩種方法之間的差異。

|  | 內嵌 | Cmdlet |
|:--- |:--- |:--- |
| 工作 (Job) |與父代在相同的工作中執行的子 Runbook。 |會為子 Runbook 建立個別的工作。 |
| 執行 |父 Runbook 會等待子 Runbook 完成後再繼續執行。 |父 Runbook 會在子 Runbook 啟動後立刻繼續執行，或  父 Runbook 會等候子作業完成。 |
| 輸出 |父 Runbook 可以直接從子 Runbook 取得輸出。 |父 Runbook 必須擷取子 Runbook 作業的輸出，或  父 Runbook 可以直接從子 Runbook 取得輸出。 |
| 參數 |子 Runbook 參數的值是個別指定，而且可以使用任何資料類型。 |子 Runbook 參數的值必須結合成單一雜湊表，且只能包含簡單、陣列，以及運用 JSON 序列化的物件資料類型。 |
| 自動化帳戶 |父 Runbook 只能使用相同自動化帳戶中的子 Runbook。 |父 Runbook 可以使用來自相同 Azure 訂用帳戶，甚至是不同訂用帳戶 (如果您已連接) 之任何自動化帳戶的子 Runbook。 |
| 發佈 |發佈父 Runbook 之前必須先發佈子 Runbook。 |啟動父 Runbook 之前必須先發佈子 Runbook。 |

## <a name="next-steps"></a>後續步驟
* [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)
* [Azure 自動化中的 Runbook 輸出與訊息](automation-runbook-output-and-messages.md)

