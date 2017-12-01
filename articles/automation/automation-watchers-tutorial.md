---
title: "在 Azure 自動化帳戶中建立監看員工作 | Microsoft Docs"
description: "了解如何在 Azure 自動化帳戶中建立監看員工作，以監看在資料夾中建立的新檔案。"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Azure 自動化監看員工作可讓您回應發生在本機資料中心的事件

在本教學課程中，您會了解如何建立新的監看員工作以：

> [!div class="checklist"]
> * 建立會監看目錄中新檔案的監看員 Runbook。
> * 建立自動化變數以記錄檔案最後由監看員處理的時間。
> * 建立會於監看員 Runbook 發現新檔案時呼叫的動作 Runbook。
> * 建立會選取監看員 Runbook 和動作 Runbook 的監看員工作。
> * 透過將新檔案新增到目錄以觸發監看員。
> * 檢查會顯示新檔案資訊的動作 Runbook 輸出。  

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，需要有下列項目。
+ Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
+ [自動化帳戶](automation-offering-get-started.md)以保存監看員和動作 Runbook，以及監看員工作。
+ 執行監看員工作的[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)。

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>建立會監看新檔案的監看員 Runbook
1.  開啟自動化帳戶，然後按一下 [Runbook] 頁面。
2.  按一下 [瀏覽資源庫] 按鈕。
![UI 中的 Runbook 清單](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  搜尋 “Watch-NewFile”，然後將 Runbook 匯入至自動化帳戶。
![UI 中的 Runbook 發佈](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  按一下 [編輯] 以檢視 Runbook 來源，然後按一下 [發佈] 按鈕。

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>建立自動化變數以記錄檔案最後由監看員處理的時間
1.  開啟 [共用資源] 底下的變數頁面，然後按一下 [新增變數]。![UI 中的變數列出](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  輸入 “Watch-NewFileTimestamp” 作為名稱
3.  選取 [類型] 作為 [日期時間]，然後按一下 [建立] 按鈕。
![UI 中的浮水印變數建立](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>建立會於監看員 Runbook 發現新檔案時呼叫的動作 Runbook
1.  按一下 [程序自動化] 類別下的 [Runbook] 頁面。
2.  按一下 [瀏覽資源庫] 按鈕。
3.  搜尋 “Process-NewFile”，然後將 Runbook 匯入至自動化帳戶。
4.  按一下 [編輯] 以檢視 Runbook 來源，然後按一下 [發佈] 按鈕。
![UI 中的程序監看員](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>建立會選取監看員 Runbook 和動作 Runbook 的監看員工作
1.  開啟 [監看員工作] 頁面，然後按一下 [新增監看員工作] 按鈕。
![UI 中的監看員清單](media/automation-watchers-tutorial/WatchersList.png)
2.  輸入 “WatchMyFolder” 作為名稱。
3.  選取 [設定監看員]，然後選取 [Watch-NewFile] Runbook。
![UI 中的監看員設定](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  針對參數輸入下列值︰
    *   FOLDERPATH： 混合式背景工作角色上的資料夾，會於其中建立新檔案
    *   EXTENSION： 保留空白以處理所有副檔名。
    *   RECURSE： 保留預設值。
    *   執行設定： 挑選混合式背景工作角色。
5.  按一下 [確定]，然後按一下 [選取] 以返回監看員頁面。
6.  選取 [設定動作]，然後選取 [Process-NewFile] Runbook。
![UI 中的監看員動作設定](media/automation-watchers-tutorial/ConfigureAction.png)
7.  針對參數輸入下列值︰
    *   EVENTDATA： 保留空白。 資料會從監看員 Runbook 傳入。
    *   執行設定： 保留為 [Azure]，因為此 Runbook 是在自動化服務中執行。
8.  按一下 [確定]，然後按一下 [選取] 以返回監看員頁面。
9.  按一下 [確定] 以建立監看員工作。

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>透過將新檔案新增到目錄以觸發監看員
1.  遠端連線至混合式背景工作角色
2.  將新文字檔新增到受監看員工作監視的資料夾。

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>檢查會顯示新檔案資訊的動作 Runbook 輸出
1.  按一下 [WatchMyFolder] 的監看員工作
2.  按一下 [檢視監看員資料流] 以看見監看員已找到新檔案，並已啟動動作 Runbook。
3.  按一下 [檢視監看員動作作業] 以查看動作 Runbook 作業。
![UI 中的監看員動作作業](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>後續步驟：

如需詳細資訊，請參閱[我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。








