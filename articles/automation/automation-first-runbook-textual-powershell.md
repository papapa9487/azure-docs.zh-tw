---
title: "我在 Azure 自動化中的第一個 PowerShell Runbook | Microsoft Docs"
description: "教學課程將逐步引導您建立、測試和發佈簡單的 PowerShell Runbook。"
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
keywords: "azure powershell, powershell 指令碼教學課程, powershell 自動化"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte;sngun
ms.openlocfilehash: 885fe6caaa00bc0caea875d72fe2626f9e6b1863
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="my-first-powershell-runbook"></a>我的第一個 PowerShell Runbook

> [!div class="op_single_selector"]
> * [圖形化](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流程](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

本教學課程將逐步引導您在 Azure 自動化中建立 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 。 我們先從測試和發佈的簡單 Runbook 開始，同時說明如何追蹤 Runbook 作業的狀態。 接著我們會修改 Runbook 以實際管理 Azure 資源，在此情況下會啟動 Azure 虛擬機器。 最後我們會藉由新增 Runbook 參數，讓 Runbook 更穩固。

## <a name="prerequisites"></a>必要條件
若要完成此教學課程，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md) ，用來保存 Runbook 以及向 Azure 資源驗證。  此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 我們會停止並啟動這部電腦，因此它不該是生產 VM。

## <a name="step-1---create-new-runbook"></a>步驟 1 - 建立新的 Runbook
我們將藉由建立一個輸出文字「Hello World」 的簡單 Runbook 開始。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。  
   [自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。 您應該已經有一些資產。 其中大部分是會自動包含在新自動化帳戶的模組。 您應該也擁有 [必要條件](#prerequisites)中所述的認證資產。
2. 按一下 [Runbook]  磚以開啟 Runbook 的清單。<br><br> ![RunbooksControl](media/automation-first-runbook-textual-powershell/runbooks-control-tiles.png)  
3. 按一下 [新增 Runbook] 按鈕，然後按一下 [建立新的 Runbook] 來建立新的 Runbook。
4. 將 Runbook 命名為「MyFirstRunbook-PowerShell」 。
5. 在此情況下，我們要建立 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)，因此請選取 [PowerShell] 作為 [Runbook 類型]。<br><br> ![Runbook 類型](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6. 按一下 [建立]  來建立 Runbook 並開啟文字式編輯器。

## <a name="step-2---add-code-to-the-runbook"></a>步驟 2 - 將程式碼加入至 runbook
您可以直接將程式碼輸入到 runbook 中，或從程式庫控制項選取 cmdlet、runbook 和資產，並利用任何相關的參數將它們加入至 runbook。 在此逐步解說中，我們會直接在 Runbook 中輸入。

1. 我們的 Runbook 目前是空白的，請在指令碼的本文中輸入「Write-Output "Hello World"」。 。<br><br> ![](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. 按一下 [儲存] 來儲存 Runbook。<br><br> ![儲存按鈕](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-save.png)  

## <a name="step-3---test-the-runbook"></a>步驟 3 - 測試 Runbook
在我們發佈 Runbook 之前，為了使其可用於生產環境，我們想要測試以確定它可以正常運作。 測試 Runbook 時，您會執行其 **草稿** 版本，並以互動方式檢視其輸出。

1. 按一下 [測試窗格]  來開啟 [測試] 窗格。<br><br> ![Test Pane](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-test.png)  
2. 按一下 [開始]  以開始測試。 這應該是唯一啟用的選項。
3. 隨即會建立 [Runbook 工作](automation-runbook-execution.md) ，並顯示其狀態。  
   作業狀態一開始為「已排入佇列」，表示正在等候雲端中的 Runbook 背景工作可供使用。 然後當背景工作宣告該工作時，狀態將變更為 [正在開始]，然後 Runbook 實際開始執行時再變更為 [執行中]。  
4. Runbook 工作完成時，會顯示其輸出。 在我們的情況中，我們應該會看到 Hello World。<br><br> ![測試窗格輸出](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook
我們建立的 Runbook 仍處於草稿模式。 我們需要將它發佈，才能在生產環境中執行它。  當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。  在我們的情況中，因為我們剛剛建立 Runbook，因此還沒有已發佈版本。

1. 按一下 [發佈] 來發佈 Runbook，然後出現提示時按一下 [是]。<br><br> ![發佈按鈕](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-publish.png)  
2. 如果您現在向左捲動以檢視 [Runbook] 窗格中的 Runbook，它會顯示 [已發佈] 的 [撰寫狀態]。
3. 捲動回右方以檢視 **MyFirstRunbook-PowerShell**的窗格。  
   在頂端的選項可讓我們啟動 Runbook、檢視 Runbook、加以排程來使其在未來的某個時間點啟動，或建立 [webhook](automation-webhooks.md)，以便能透過 HTTP 呼叫加以啟動。
4. 我們想要啟動 Runbook，因此按一下 [啟動]，然後在 [啟動 Runbook] 刀鋒視窗開啟時按一下 [確定]。<br><br> ![開始按鈕](media/automation-first-runbook-textual-powershell/automation-runbook-controls-start.png)<br>    
5. 作業窗格會針對我們所建立的 Runbook 作業開啟。 我們可以關閉此窗格，但在此情況下，我們會讓它開啟，以便觀看作業的進度。
6. [作業摘要]  中會顯示作業狀態，且符合當我們測試 Runbook 時看到的狀態。<br><br> ![工作摘要](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. 一旦 Runbook 狀態顯示 [已完成]，請按一下 [輸出]。 [輸出] 窗格會開啟，而且可以看到我們的「Hello World」 。<br><br> ![工作輸出](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. 關閉 [輸出] 窗格。
9. 按一下 [所有記錄檔]  以開啟 Runbook 作業的 [資料流] 窗格。 我們應該只會在輸出資料流中看到「Hello World」  ，但可能也會顯示 Runbook 作業的其他資料流，例如 Runbook 寫入時發生的詳細資訊和錯誤。<br><br> ![](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. 關閉 [資料流] 窗格和 [工作] 窗格，以返回 MyFirstRunbook-PowerShell 窗格。
11. 按一下 [作業]  以開啟此 Runbook 的 [工作] 窗格。 這樣會列出此 Runbook 所建立的所有工作。 由於我們只執行一次作業，因此應該只會看到列出一項作業。<br><br> ![作業清單](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. 您可以按一下此作業，以開啟我們啟動 Runbook 時所檢視的相同 [作業] 窗格。 這可讓您回到過去的時間並檢視針對特定 Runbook 所建立的任何工作的詳細資料。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步驟 5 - 加入驗證來管理 Azure 資源
我們已經測試並發行我們 Runbook，但是到目前為止，它似乎並不實用。 我們想要讓它管理 Azure 資源。 不過它無法辦到這點，除非我們使用在 [必要條件](#prerequisites)中提及的認證對其進行驗證。 我們會利用 **Add-AzureRmAccount** Cmdlet 來執行。

1. 按一下 MyFirstRunbook-PowerShell 窗格上的 [編輯]  來開啟文字式編輯器。<br><br> ![編輯 Runbook](media/automation-first-runbook-textual-powershell/automation-runbook-controls-edit.png)<br>   
2. 我們不再需要 **Write-Output** 行，因此可以放心刪除。
3. 輸入或是複製並貼上下列程式碼，此程式碼會處理「自動化執行身分」帳戶的驗證︰
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. 按一下 [測試]  窗格，我們便可以測試 Runbook。
5. 按一下 [開始]  以開始測試。 測試完成時，您應該會從帳戶收到如同以下顯示基本資訊的輸出。 這可確認認證有效。<br><br> ![驗證](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步驟 6 - 加入程式碼以啟動虛擬機器
由於我們的 runbook 正在驗證我們的 Azure 訂用帳戶，所以我們可以管理資源。 我們會新增一個命令以啟動虛擬機器。 您可以在您的 Azure 訂用帳戶中挑選任何虛擬機器，而現在我們會在 Runbook 中將該名稱硬式編碼。

1. 在 Add-AzureRmAccount 後面輸入 Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'，提供要啟動之虛擬機器的名稱和資源群組名稱。  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. 儲存 Runbook，然後按一下 [測試]  窗格，我們便能加以測試。
3. 按一下 [開始]  以開始測試。 當它完成時，請檢查虛擬機器已啟動。

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>步驟 7 - 將輸入參數加入至 Runbook
我們的 Runbook 目前會啟動我們在 Runbook 中硬式編碼的虛擬機器，但如果我們在啟動 Runbook 時指定虛擬機器，它會更有用。 我們現在會將輸入參數加入 Runbook，以提供該功能。

1. 如下列範例所示，將 [VMName] 和 [ResourceGroupName] 的參數新增至 Runbook，並搭配使用這些變數與 **Start-AzureRmVM** Cmdlet。  
   
   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
2. 儲存 Runbook 並開啟 [測試] 窗格。 您現在可以提供測試中所用的兩個輸入變數的值。
3. 關閉 [測試] 窗格。
4. 按一下 [發佈]  來發行新版本的 Runbook。
5. 停止您在上一個步驟中啟動的虛擬機器。
6. 按一下 [開始]  以啟動 Runbook。 輸入您要啟動之虛擬機器的 [VMName] 和 [ResourceGroupName]。<br><br> ![傳遞參數](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
7. Runbook 完成時，請檢查虛擬機器已啟動。

## <a name="differences-from-powershell-workflow"></a>與 PowerShell 工作流程的差異
PowerShell Runbook 的生命週期、功能和管理與 PowerShell 工作流程 Runbook 相同，但是有一些差異和限制：

1. PowerShell Runbook 執行的速度比 PowerShell 工作流程 Runbook 快，因為它們沒有編譯步驟。
2. PowerShell 工作流程 Runbook 支援檢查點，PowerShell 工作流程 Runbook 可以使用檢查點從 Runbook 的任何點繼續，而 PowerShell runbook 只能從頭開始。
3. PowerShell 工作流程 Runbook 支援平行和序列執行，而 PowerShell Runbook 只能以序列方式執行命令。
4. 在 PowerShell 工作流程 Runbook 中，活動、命令或指令碼區塊可以有它自己的 Runspace，而在 PowerShell Runbook 中，指令碼中的所有項目會在單一的 Runspace 中執行。 原生 PowerShell Runbook 與 PowerShell 工作流程 Runbook 之間還有一些 [語法差異](https://technet.microsoft.com/magazine/dn151046.aspx) 。

## <a name="next-steps"></a>後續步驟
* 若要開始使用圖形化 Runbook，請參閱 [我的第一個圖形化 Runbook](automation-first-runbook-graphical.md)
* 若要開始使用 PowerShell 工作流程 Runbook，請參閱 [我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)
* 若要深入了解 Runbook 類型、其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)
* 如需 PowerShell 指令碼支援功能的詳細資訊，請參閱 [Azure 自動化中的原生 PowerShell 指令碼支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

