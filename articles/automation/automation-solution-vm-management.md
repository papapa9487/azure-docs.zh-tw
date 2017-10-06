---
title: "在離峰期間啟動/停止 VM [預覽] 方案 | Microsoft Docs"
description: "VM 管理方案會依照排程啟動和停止 Azure Resource Manager 虛擬機器，並從 Log Analytics 主動監視。"
services: automation
documentationCenter: 
authors: eslesar
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: e44f04b3492ac07822b0842864f84a5f16dc3f5b
ms.contentlocale: zh-tw
ms.lasthandoff: 06/03/2017

---

# <a name="startstop-vms-during-off-hours-preview-solution-in-automation"></a>自動化中的在離峰期間啟動/停止 VM [預覽] 方案

在離峰期間啟動/停止 VM [預覽] 方案會依照使用者定義的排程啟動和停止 Azure Resource Manager 虛擬機器，並透過 OMS Log Analytics 提供成功啟動和停止虛擬機器的自動化作業深入解析。  

## <a name="prerequisites"></a>必要條件

- Runbook 會使用 [Azure 執行身分帳戶](automation-offering-get-started.md#authentication-methods)。  執行身分帳戶是慣用的驗證方法，因為它會使用憑證驗證，而不是過期或經常變更的密碼。  

- 這個方案只能管理與「自動化」帳戶位於相同訂用帳戶中的 VM。  

- 這個方案只會部署到下列 Azure 區域 - 澳大利亞東南部、美國東部、東南亞和西歐。  管理 VM 排程的 Runbook 可以任何區域中的 VM 為目標。  

- 若要在啟動和停止 VM Runbook 完成時傳送電子郵件通知，則需要有 Office 365 企業級訂用帳戶。  

## <a name="solution-components"></a>方案元件

這個方案是由下列將匯入和新增至自動化帳戶的資源所組成。

### <a name="runbooks"></a>Runbook

Runbook | 說明|
--------|------------|
CleanSolution-MS-Mgmt-VM | 當您刪除訂用帳戶中的方案時，此 Runbook 將會移除所有內含的資源和排程。|  
SendMailO365-MS-Mgmt | 此 Runbook 會透過 Office 365 Exchange 傳送電子郵件。|
StartByResourceGroup-MS-Mgmt-VM | 此 Runbook 旨在啟動位於指定 Azure 資源群組清單中的 VM (傳統與 ARM 型 VM)。
StopByResourceGroup-MS-Mgmt-VM | 此 Runbook 旨在停止位於指定 Azure 資源群組清單中的 VM (傳統與 ARM 型 VM)。|
<br>

### <a name="variables"></a>變數

變數 | 說明|
---------|------------|
**SendMailO365-MS-Mgmt** Runbook ||
SendMailO365-IsSendEmail-MS-Mgmt | 指定 StartByResourceGroup-MS-Mgmt-VM 和 StopByResourceGroup-MS-Mgmt-VM Runbook 是否可以在完成時傳送電子郵件通知。  選取 [True] 可啟用電子郵件警示，而選取 [False] 則為停用。 預設值為 [False]。| 
**StartByResourceGroup-MS-Mgmt-VM** Runbook ||
StartByResourceGroup-ExcludeList-MS-Mgmt-VM | 輸入要從管理作業中排除的 VM 名稱；使用不含空格的分號 (;) 分隔名稱。 值會區分大小寫並支援萬用字元 (星號)。|
StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | 可以附加至電子郵件訊息本文開頭的文字。|
StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | 指定包含電子郵件 Runbook 的自動化帳戶名稱。  **請勿修改此變數。**|
StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt | 指定電子郵件 Runbook 的名稱。  此變數由 StartByResourceGroup-MS-Mgmt-VM 和 StopByResourceGroup-MS-Mgmt-VM Runbook 用來傳送電子郵件。  **請勿修改此變數。**|
StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | 指定包含電子郵件 Runbook 的資源群組名稱。  **請勿修改此變數。**|
StartByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | 指定電子郵件的主旨列文字。|  
StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | 指定電子郵件的收件者。  使用不含空格的分號 (;) 輸入不同的名稱。|
StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | 輸入要從管理作業中排除的 VM 名稱；使用不含空格的分號 (;) 分隔名稱。 值會區分大小寫並支援萬用字元 (星號)。  預設值 (星號) 將包含訂用帳戶中的所有資源群組。|
StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | 指定包含此方案所要管理之 VM 的訂用帳戶。  這必須是此方案的自動化帳戶所在的相同訂用帳戶。|
**StopByResourceGroup-MS-Mgmt-VM** Runbook ||
StopByResourceGroup-ExcludeList-MS-Mgmt-VM | 輸入要從管理作業中排除的 VM 名稱；使用不含空格的分號 (;) 分隔名稱。 值會區分大小寫並支援萬用字元 (星號)。|
StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | 可以附加至電子郵件訊息本文開頭的文字。|
StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | 指定包含電子郵件 Runbook 的自動化帳戶名稱。  **請勿修改此變數。**|
StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | 指定包含電子郵件 Runbook 的資源群組名稱。  **請勿修改此變數。**|
StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | 指定電子郵件的主旨列文字。|  
StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | 指定電子郵件的收件者。  使用不含空格的分號 (;) 輸入不同的名稱。|
StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | 輸入要從管理作業中排除的 VM 名稱；使用不含空格的分號 (;) 分隔名稱。 值會區分大小寫並支援萬用字元 (星號)。  預設值 (星號) 將包含訂用帳戶中的所有資源群組。|
StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | 指定包含此方案所要管理之 VM 的訂用帳戶。  這必須是此方案的自動化帳戶所在的相同訂用帳戶。|  
<br>

### <a name="schedules"></a>排程

排程 | 說明|
---------|------------|
StartByResourceGroup-Schedule-MS-Mgmt | 排程可執行此方案所管理之 VM 啟動的 StartByResourceGroup Runbook。 建立時會預設為 UTC 時區。|
StopByResourceGroup-Schedule-MS-Mgmt | 排程可執行此方案所管理之 VM 關閉的 StopByResourceGroup Runbook。 建立時會預設為 UTC 時區。|

### <a name="credentials"></a>認證

認證 | 說明|
-----------|------------|
O365Credential | 指定有效的 Office 365 使用者帳戶以傳送電子郵件。  只有在 SendMailO365-IsSendEmail-MS-Mgmt 變數設定為 [True] 時才需要。

## <a name="configuration"></a>組態

執行下列步驟，將在離峰期間啟動/停止 VM [預覽] 方案新增至您的自動化帳戶，然後設定變數以自訂方案。

1. 從 Azure 入口網站的主畫面中選取 [Marketplace] 圖格。  如果圖格已不再釘選到您的主畫面，請從左導覽窗格中選取 [新增]。  
2. 在 [Marketplace] 刀鋒視窗的搜尋方塊中輸入 [啟動 VM]，然後從搜尋結果中選取 [在離峰期間啟動/停止 VM [預覽]] 方案。  
3. 在所選方案的 [在離峰期間啟動/停止 VM [預覽]] 刀鋒視窗中，檢閱摘要資訊，然後按一下 [建立]。  
4. [新增方案] 刀鋒視窗隨即出現，系統會提示您先設定方案，才可以將它匯入自動化訂用帳戶。<br><br> ![VM 管理的新增方案刀鋒視窗](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5.  在 [新增方案] 刀鋒視窗中，選取 [工作區] 並在此選取連結到自動化帳戶所在相同 Azure 訂用帳戶的 OMS 工作區，或建立新的 OMS 工作區。  如果您沒有 OMS 工作區，您可以選取 [建立新工作區]，然後在 [OMS 工作區] 刀鋒視窗上執行下列動作︰ 
   - 指定新 [OMS 工作區] 的名稱。
   - 如果選取的預設值不合適，請從下拉式清單中選取要連結的 [訂用帳戶]。
   - 對於 [資源群組]，您可以建立新的資源群組，或選取現有的資源群組。  
   - 選取 [位置] 。  目前可供選取的位置只有 [澳大利亞東南部]、[美國東部]、[東南亞] 和 [西歐]。
   - 選取 **定價層**。  此方案以兩種定價層提供︰免費和 OMS 付費層。  免費層會限制每日可收集的資料量、保留期和 Runbook 作業執行階段分鐘數。  OMS 付費層不會限制每日可收集的資料量。  

        > [!NOTE]
        > 儘管會顯示獨立付費層做為選項，但它並不適用。  如果您選取它並在您的訂用帳戶中繼續建立此解決方案，它將會失敗。  當此解決方案正式發行時，將會解決這個問題。<br>如果您使用此解決方案，則它只會使用自動化工作分鐘數並記錄擷取。  方案不會將其他 OMS 節點新增至您的環境。  

6. 在 [OMS 工作區] 刀鋒視窗上提供必要資訊之後，按一下 [建立]。  在驗證資訊及建立工作區時，您可以在功能表的 [通知] 底下追蹤其進度。  您會回到 [新增方案] 刀鋒視窗。  
7. 在 [新增方案] 刀鋒視窗中，選取 [自動化帳戶]。  如果您要建立新的 OMS 工作區，您也必須建立將與先前指定的新 OMS 工作區產生關聯的新自動化帳戶，包括您的 Azure 訂用帳戶、資源群組和區域。  您可以選取 [建立自動化帳戶]，然後在 [新增自動化帳戶] 刀鋒視窗上提供下列資訊︰ 
  - 在 [名稱] 欄位中輸入自動化帳戶的名稱。

    系統會根據所選的 OMS 工作區自動填入所有其他選項，您無法修改這些選項。  Azure 執行身分帳戶是此方案內含 Runbook 的預設驗證方法。  一旦您按一下 [確定]，就會驗證組態選項並建立自動化帳戶。  您可以在功能表的 [通知] 底下追蹤其進度。 

    不然，您可以選取現有的自動化執行身分帳戶。  請注意，您選取的帳戶不能已經連結到另一個 OMS 工作區，否則刀鋒視窗中會顯示一則訊息來通知您。  如果帳戶已經連結，您必須選取不同的自動化執行身分帳戶或建立一個新帳戶。<br><br> ![自動化帳戶已經連結至 OMS 工作區](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 最後在 [新增方案] 刀鋒視窗上選取 [組態]，[參數] 刀鋒視窗隨即出現。  在 [參數] 刀鋒視窗上，系統會提示您︰  
   - 指定 [目標資源群組名稱]，這個資源群組名稱內含將由此方案管理的 VM。  您可以輸入多個名稱，然後使用分號加以分隔 (這些值需區分大小寫)。  如果您想要以訂用帳戶的所有資源群組中的 VM 為目標，則可使用萬用字元。
   - 選取 [排程]，這是一個週期性日期和時間，可用於啟動及停止目標資源群組中的 VM。  排程會預設設定為 UTC 時區，無法選取不同的區域。  在設定方案後，如果您想要將排程設定為特定時區，請參閱下方的[修改啟動和關閉排程](#modifying-the-startup-and-shutdown-schedule)。    

10. 設定好方案所需的初始設定後，選取 [建立]。  系統會驗證所有的設定，然後嘗試在您的訂用帳戶中部署此方案。  此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。 

## <a name="collection-frequency"></a>收集頻率

自動化作業記錄檔和工作串流資料會每隔五分鐘擷取到 OMS 儲存機制中。  

## <a name="using-the-solution"></a>使用方案

當您在 OMS 工作區中新增 VM 管理方案時，[StartStopVM 檢視] 圖格會新增至 OMS 儀表板。  此圖格會顯示方案中已啟動和順利完成的 Runbook 作業計數和圖形表示。<br><br> ![VM 管理 StartStopVM 檢視圖格](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

在自動化帳戶中，您可以存取和管理方案，方法是選取 [方案] 圖格，然後從 [方案] 刀鋒視窗的清單中選取 [Start-Stop-VM[工作區]] 方案。<br><br> ![自動化方案清單](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

選取此方案將會顯示 [Start-Stop-VM[工作區]] 方案刀鋒視窗，您可以在其中檢閱重要的詳細資料，例如 **StartStopVM** 圖格，就像在您的 OMS 工作區中，該圖格會顯示方案中已啟動和順利完成的 Runbook 作業計數和圖形表示。<br><br> ![自動化 VM 方案刀鋒視窗](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

您也可以從這裡開啟 OMS 工作區，並進行進一步的工作記錄分析。  只需按一下 [所有設定]，在 [設定] 刀鋒視窗中選取 [快速入門]，然後在 [快速入門] 刀鋒視窗中選取 [OMS 入口網站]。   這樣會開啟新的索引標籤或新的瀏覽器工作階段，並顯示與您的自動化帳戶和訂用帳戶相關聯的 OMS 工作區。  


### <a name="configuring-e-mail-notifications"></a>設定電子郵件通知

若要在啟動和停止 VM Runbook 完成時啟用電子郵件通知，您必須修改 **O365Credential** 認證以及至少下列的變數︰

 - SendMailO365-IsSendEmail-MS-Mgmt
 - StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
 - StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

若要設定 **O365Credential** 認證，請執行下列步驟︰

1. 從您的自動化帳戶，按一下視窗頂端的 [所有設定]。 
2. 在 [設定] 刀鋒視窗的 [自動化資源] 區段之下，選取 [資產]。 
3. 在 [資產] 刀鋒視窗上選取 [認證] 圖格，然後從 [認證] 刀鋒視窗選取 [O365Credential]。  
4. 輸入有效的 Office 365 使用者名稱和密碼，然後按一下 [儲存] 以儲存變更。  

若要設定先前醒目提示的變數，請執行下列步驟︰

1. 從您的自動化帳戶，按一下視窗頂端的 [所有設定]。 
2. 在 [設定] 刀鋒視窗的 [自動化資源] 區段之下，選取 [資產]。 
3. 在 [資產] 刀鋒視窗上選取 [變數] 圖格，接著從 [變數] 刀鋒視窗中選取上面所列的變數，然後遵循先前在[變數](##variables)一節中為其指定的描述來修改其值。  
4. 按一下 [儲存] 以儲存對變數所做的變更。   

### <a name="modifying-the-startup-and-shutdown-schedule"></a>修改啟動和關閉排程

在此解決方案中管理啟動和關閉排程會遵循[在 Azure 自動化中排程 Runbook](automation-schedules.md) 中所概述的相同步驟。  請記住，您無法修改排程設定。  您必須停用現有的排程並建立新的排程，然後連結至您希望排程套用的 **StartByResourceGroup-MS-Mgmt-VM** 或 **StopByResourceGroup-MS-Mgmt-VM** Runbook。   

## <a name="log-analytics-records"></a>Log Analytics 記錄

自動化會在 OMS 儲存機制中建立兩種類型的記錄。

### <a name="job-logs"></a>作業記錄檔

屬性 | 說明|
----------|----------|
呼叫者 |  起始作業的人員。  可能的值為電子郵件地址或排程作業的系統。|
類別 | 資料類型的分類。  對自動化來說，該值是 JobLogs。|
CorrelationId | Runbook 作業之相互關聯識別碼的 GUID。|
JobId | Runbook 作業之識別碼的 GUID。|
operationName | 指定在 Azure 中執行的作業類型。  對自動化來說，該值是 Job。|
resourceId | 指定 Azure 中的資源類型。  對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。  對自動化來說，此值是 Azure 自動化。|
ResourceType | 指定 Azure 中的資源類型。  對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
resultType | Runbook 作業的狀態。  可能的值包括：<br>- Started (已啟動)<br>- Stopped (已停止)<br>- Suspended (暫止)<br>- Failed (失敗)<br>- Succeeded|
resultDescription | 說明 Runbook 作業的結果狀態。  可能的值包括：<br>- Job is started (工作已啟動)<br>- Job Failed (工作失敗)<br>- Job Completed|
RunbookName | 指定 Runbook 的名稱。|
SourceSystem | 指定所提交資料的來源系統。  對自動化來說，此值會是 :OpsManager|
StreamType | 指定事件的類型。 可能的值包括：<br>- Verbose<br>- Output<br>- Error<br>- Warning|
SubscriptionId | 指定作業的訂用帳戶 ID。
時間 | Runbook 作業的執行日期和時間。|


### <a name="job-streams"></a>作業串流

屬性 | 說明|
----------|----------|
呼叫者 |  起始作業的人員。  可能的值為電子郵件地址或排程作業的系統。|
類別 | 資料類型的分類。  對自動化來說，該值是 JobStreams。|
JobId | Runbook 作業之識別碼的 GUID。|
operationName | 指定在 Azure 中執行的作業類型。  對自動化來說，該值是 Job。|
ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
resourceId | 指定 Azure 中的資源識別碼。  對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。  對自動化來說，此值是 Azure 自動化。|
ResourceType | 指定 Azure 中的資源類型。  對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
resultType | Runbook 作業在產生事件時的結果。  可能的值包括：<br>- InProgres|
resultDescription | 包含來自 Runbook 的輸出串流。|
RunbookName | Runbook 的名稱。|
SourceSystem | 指定所提交資料的來源系統。  對自動化來說，此值會是 OpsManager|
StreamType | 作業串流的類型。 可能的值包括：<br>-Progress (進度)<br>- Output (輸出)<br>- Warning (警告)<br>- Error (錯誤)<br>- Debug (偵錯)<br>- Verbose|
時間 | Runbook 作業的執行日期和時間。|

當您執行的記錄檔搜尋傳回記錄 **JobLogs** 或 **JobStreams** 類別的目錄時，您可以選取 **JobLogs** 或 **JobStreams** 檢視，其中顯示一組彙總搜尋所傳回更新的圖格。

## <a name="sample-log-searches"></a>記錄檔搜尋範例

下表提供此方案所收集之作業記錄的記錄檔搜尋範例。 

查詢 | 說明|
----------|----------|
尋找已順利完成的 Runbook StartVM 作業 | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded &#124; measure count() by JobId_g|
尋找已順利完成的 Runbook StopVM 作業 | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed &#124; measure count() by JobId_g
顯示 StartVM 和 StopVM Runbook 不同時間的作業狀態 | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") | measure Count() by ResultType interval 1day|

## <a name="removing-the-solution"></a>移除解決方案

如果您決定不再需要使用解決方案，您可以將它從自動化帳戶中刪除。  刪除解決方案只會移除 Runbook，而不會刪除在新增解決方案時所建立的排程或變數。  如果您未搭配其他 Runbook 使用這些資產，則必須以手動方式加以刪除。  

若要刪除解決方案，請執行下列步驟：

1.  從您的自動化帳戶選取 [解決方案] 圖格。  
2.  在 [解決方案] 刀鋒視窗上，選取 **Start-Stop-VM[Workspace]** 解決方案。  在 **VMManagementSolution[Workspace]** 刀鋒視窗上，按一下功能表中的 [刪除]。<br><br> ![刪除 VM 管理解決方案](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  在 [刪除解決方案] 視窗中，確認您要刪除解決方案。
4.  在驗證資訊並刪除解決方案後，您可以在功能表的 [通知] 底下追蹤其進度。  在移除解決方案的程序開始後，您會回到 **VMManagementSolution[Workspace]** 刀鋒視窗。  

自動化帳戶和 OMS 工作區不會在此程序中刪除。  如果您不想保留 OMS 工作區，則必須以手動方式加以刪除。  此作業也可以從 Azure 入口網站完成。   從 Azure 入口網站的首頁畫面，選取 [Log Analytics]，然後在 [Log Analytics] 刀鋒視窗上選取工作區，並從 [工作區設定] 刀鋒視窗的功能表選取 [刪除]。  
      
## <a name="next-steps"></a>後續步驟

- 若要深入了解如何建構不同的搜尋查詢，以及使用 Log Analytics 檢閱自動化工作記錄檔，請參閱 [Log Analytics 中的記錄檔搜尋](../log-analytics/log-analytics-log-searches.md)
- 若要深入了解 Runbook 執行方式、如何監視 Runbook 工作，以及其他技術性詳細資料，請參閱 [追蹤 Runbook 工作](automation-runbook-execution.md)
- 若要深入了解 OMS Log Analytics 和資料收集來源，請參閱 [在 Log Analytics 中收集 Azure 儲存體資料概觀](../log-analytics/log-analytics-azure-storage.md)






   


