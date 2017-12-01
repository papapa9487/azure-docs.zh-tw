---
title: "「停機期間啟動/停止 VM」解決方案 | Microsoft Docs"
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
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: 2ff2208f62c24c460c9d17533e28fd007549828b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Azure 自動化中的「停機期間啟動/停止 VM」解決方案

「停機期間啟動/停止 VM」解決方案可根據使用者定義的排程啟動/停止 Azure 虛擬機器、透過 Log Analytics 提供深入解析，以及運用 [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview) \(英文\) 傳送選擇性的電子郵件。 針對大多數的案例，它皆能同時支援 Azure Resource Manager 和傳統 VM。 

此解決方案可針對想要運用無伺服器的低成本資源來降低成本的客戶，提供非集中式的自動化功能。 功能包括：

* 對 VM 進行排程以啟動/停止
* 使用 Azure 標記以遞增順序對 VM 進行排程以啟動/停止 (不支援傳統 VM)
* 根據低 CPU 使用率自動停止 VM

## <a name="prerequisites"></a>必要條件

- Runbook 會使用 [Azure 執行身分帳戶](automation-offering-get-started.md#authentication-methods)。  執行身分帳戶是慣用的驗證方法，因為它會使用憑證驗證，而不是過期或經常變更的密碼。  

- 這個方案只能管理與「自動化」帳戶位於相同訂用帳戶中的 VM。  

- 這個解決方案只能部署到下列 Azure 區域：澳大利亞東南部、加拿大中部、印度中部、美國東部、日本東部、東南亞、英國南部和西歐。  
    
    > [!NOTE]
    > 管理 VM 排程的 Runbook 能夠以任何區域中的 VM 為目標。  

- 若要在啟動和停止 VM 的 Runbook 完成時傳送電子郵件通知，從 Azure Marketplace 進行上架時，您必須選取 [是] 以部署 SendGrid。 

    > [!IMPORTANT]
    > SendGrid 為協力廠商服務，如需 SendGrid 支援，請連絡 [SendGrid](https://sendgrid.com/contact/) \(英文\)。  
    >
   
    SendGrid 的限制如下：

    * 每個訂用帳戶的每個使用者最多可以有一個 SendGrid 帳戶
    * 每個訂用帳戶最多有兩個 SendGrid 帳戶

如果您已部署此解決方案的舊版，就必須先從帳戶中刪除它，才能部署此版本。  

## <a name="solution-components"></a>方案元件

此解決方案包括預先設定的 Runbook、排程，以及與 Log Analytics 的整合，可讓您針對業務需求量身打造虛擬機器的啟動和關機時間。 

### <a name="runbooks"></a>Runbook

下表列出部署至您自動化帳戶的 Runbook。  不建議您對 Runbook 程式碼進行變更，請自行撰寫 Runbook 來取得新功能。

> [!NOTE]
> 請勿直接執行任何名稱結尾附加有 “Child” 名稱的 Runbook。
>

所有的父系 Runbook 都包括 *WhatIf* 參數，當將它設為 **True** 時，即可支援詳述在不使用 *WhatIf* 參數執行的情況下，該 Runbook 會採取的確切行為，並驗證是否已將正確的 VM 作為目標。  將 *WhatIf* 設為 **False** 時，Runbook 只會執行其定義的動作。 

|**Runbook** | **參數** | **說明**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 只能從父系 Runbook 呼叫。 針對 AutoStop 案例以每個資源為基礎建立警示。| 
|AutoStop_CreateAlert_Parent | WhatIf：True 或 False <br> VMList | 在目標訂用帳戶或資源群組中的 VM 上建立或更新 Azure 警示規則。 <br> VMList：以逗號分隔的 VM 清單。  例如，*vm1,vm2,vm3*| 
|AutoStop_Disable | 無 | 停用 AutoStop 警示和預設排程。| 
|AutoStop_StopVM_Child | WebHookData | 只能從父系 Runbook 呼叫。 警示規則會呼叫此 Runbook，而此 Runbook 會執行停止 VM 的工作。|  
|Bootstrap_Main | 無 | 單次使用以設定啟動程序設定 (例如 webhookURI)，這些設定通常無法從 Azure Resource Manager 存取。 如果已成功完成部署，系統會自動移除此 Runbook。|  
|ScheduledStartStop_Child | VMName <br> Action：Stop 或 Start <br> resourceGroupName | 只能從父系 Runbook 呼叫。 針對排程的停止，實際執行停止或啟動。|  
|ScheduledStartStop_Parent | Action：Stop 或 Start <br> WhatIf：True 或 False | 這會對訂用帳戶中的所有 VM 生效，除非您編輯 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames**，這會將它限制為僅對這些目標資源群組執行。 您也可以更新 **External_ExcludeVMNames** 變數來排除特定的 VM。 此 Runbook 中的 WhatIf 行為與其他 Runbook 相同。|  
|SequencedStartStop_Parent | Action：Stop 或 Start <br> WhatIf：True 或 False | 在每部您要排序啟動\\停止活動的 VM 上，建立名稱為 **SequenceStart** 的標記，以及另一個名稱為 **SequenceStop** 的標記。 標記值應該是正整數 (1,2,3)，對應至您要依遞增順序啟動\\停止的順序。 此 Runbook 中的 WhatIf 行為與其他 Runbook 相同。 <br> **注意：VM 必須位於定義於 Azure 自動化變數中 External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 的資源群組內，並具有適當的標記以使動作生效。**|

### <a name="variables"></a>變數

下表列出在您自動化帳戶中建立的變數。  建議您只修改名稱前面具有 **External** 的變數，若修改名稱前面具有 **Internal** 的變數，將會導致非預期的效果。  

|**Variable** | **說明**|
---------|------------|
|External_AutoStop_Condition | 這是設定觸發警示之條件所需的條件運算子。 可接受的值為 **GreaterThan**、**GreaterThanOrEqual**、**LessThan**、**LessThanOrEqual**。|  
|External_AutoStop_Description | 在 CPU % 超出閾值的情況下停止 VM 的警示。|  
|External_AutoStop_MetricName | 要設定 Azure 警示規則的效能度量名稱。| 
|External_AutoStop_Threshold | 適用於在變數 *External_AutoStop_MetricName* 中指定之 Azure 警示規則的閾值。 百分比值的範圍為 1 至 100。|  
|External_AutoStop_TimeAggregationOperator | 將會套用至選取的範圍大小以評估條件的時間彙總運算子。 可接受的值為 **Average**、**Minimum**、**Maximum**、**Total**、**Last**。|  
|External_AutoStop_TimeWindow | Azure 將分析選取之度量以觸發警示的範圍大小。 此參數接受時間範圍格式的輸入。 可能的值為五分鐘至六小時。|  
|External_EmailFromAddress | 指定電子郵件的寄件者。|  
|External_EmailSubject | 指定電子郵件的主旨列文字。|  
|External_EmailToAddress | 指定電子郵件的收件者。 使用逗號分隔名稱。|  
|External_ExcludeVMNames | 輸入要排除的 VM 名稱，請使用不含空格的逗號來分隔名稱。|  
|External_IsSendEmail | 指定完成時傳送電子郵件通知的選項。  針對是否傳送電子郵件，指定 **Yes** 或 **No**。  如果您沒有在初始部署期間建立 SendGrid，此選項應為 **No**。|  
|External_Start_ResourceGroupNames | 使用逗號分隔值指定一或多個作為啟動動作目標的資源群組。|  
|External_Stop_ResourceGroupNames | 使用逗號分隔值指定一或多個作為停止動作目標的資源群組。|  
|Internal_AutomationAccountName | 指定自動化帳戶的名稱。|  
|Internal_AutoSnooze_WebhookUri | 指定針對 AutoStop 案例呼叫的 Webhook URI。|  
|Internal_AzureSubscriptionId | 指定 Azure 訂用帳戶識別碼。|  
|Internal_ResourceGroupName | 指定 Azure 自動化帳戶資源群組名稱。|  
|Internal_SendGridAccountName | 指定 SendGrid 帳戶名稱。|  
|Internal_SendGridPassword | 指定 SendGrid 帳戶密碼。|  

<br>

在所有情況下，**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 變數皆為設定目標 VM 的必要項目 (除了為 **AutoStop_CreateAlert_Parent** Runbook 提供以逗號分隔的 VM 清單以外)。 也就是說，您的 VM 必須位於目標資源群組，才會發生啟動/停止動作。 此邏輯的運作方式有點類似 Azure 原則，其中您可以將訂用帳戶或資源群組設為目標，並讓新建立的 VM 繼承動作。 此方法可免去針對每部 VM 維護個別排程的必要，並可大規模地管理啟動/停止。

### <a name="schedules"></a>排程

下表列出在您的自動化帳戶中建立的各個預設排程。  您可以修改它們，或建立自己的自訂排程。  這些排程預設皆為停用，除了 **Scheduled_StartVM** 和 **Scheduled-StopVM** 之外。

不建議啟用所有的排程，因為這可能會使排程所執行的動作出現重疊。  相反地，您應該先判斷要執行哪些最佳化，並據以做出相對應的修改。  如需進一步說明，請參閱＜概觀＞一節中的範例案例。   

|**ScheduleName** | **頻率** | **說明**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每隔八小時執行 | 每隔 8 小時會執行 AutoStop_CreateAlert_Parent Runbook，這會停止 VM 在 Azure 自動化變數中 “External_Start_ResourceGroupNames”、“External_Stop_ResourceGroupNames” 和 “External_ExcludeVMNames” 中的基底值。  或者，您可以使用 "VMList" 參數指定以逗號分隔的 VM 清單。|  
|Scheduled_StopVM | 使用者定義時間，每天 | 每天會在指定時間搭配參數 “Stop” 執行 Scheduled_Parent Runbook。  將會自動停止符合由資產變數所定義之規則的所有 VM。  建議您啟用姊妹排程 Scheduled-StartVM。|  
|Scheduled_StartVM | 使用者定義時間，每天 | 每天會在指定時間搭配參數 *Start* 執行 Scheduled_Parent Runbook。  將會自動啟動符合定義之規則並屬於適當變數的所有 VM。  建議您啟用姊妹排程 **Scheduled-StopVM**。|
|Sequenced-StopVM | 上午 1:00 (UTC)，每星期五 | 每星期五會在指定時間搭配參數 *Stop* 執行 Sequenced_Parent Runbook。  將會以循序方式 (遞增) 停止具有定義之 **SequenceStop** 標記並屬於適當變數的所有 VM。  如需標記值和資產變數的詳細資料，請參閱＜Runbook＞一節。  建議您啟用姊妹排程 **Sequenced-StartVM**。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | 每星期一會在指定時間搭配參數 *Start* 執行 Sequenced_Parent Runbook。  將會以循序方式 (遞減) 啟動具有定義之 **SequenceStart** 標記並屬於適當變數的所有 VM。  如需標記值和資產變數的詳細資料，請參閱＜Runbook＞一節。  建議您啟用姊妹排程 **Sequenced-StopVM**。|

<br>

## <a name="configuration"></a>組態

執行下列步驟，將在離峰期間啟動/停止 VM [預覽] 方案新增至您的自動化帳戶，然後設定變數以自訂方案。

1. 在 Azure 入口網站中，按一下 [新增]。<br> ![Azure 入口網站](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. 在 [Marketplace] 窗格中，輸入**啟動 VM**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 從搜尋結果選取 [停機期間啟動/停止 VM [預覽]]。  
3. 在所選解決方案的 [停機期間啟動/停止 VM [預覽]] 窗格中，檢閱摘要資訊，然後按一下 [建立]。  
4. [新增解決方案] 窗格隨即出現，系統會在其中提示您設定解決方案，以將它匯入您的自動化訂用帳戶。<br><br> ![VM 管理的新增方案刀鋒視窗](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  選取 [新增解決方案] 刀鋒視窗上的 [工作區]，並在此選取連結至和自動化帳戶相同之 Azure 訂用帳戶的 OMS 工作區，或是建立新的工作區。  如果您沒有工作區，可以選取 [建立新工作區]，然後在 [OMS 工作區] 窗格上執行下列動作︰ 
   - 指定新 [OMS 工作區] 的名稱。
   - 如果選取的預設值不合適，請從下拉式清單中選取要連結的 [訂用帳戶]。
   - 對於 [資源群組]，您可以建立新的資源群組，或選取現有的資源群組。  
   - 選取 [位置] 。  目前提供選取的位置只有**澳大利亞東南部**、**加拿大中部**、**印度中部**、**美國東部**、**日本東部**、**東南亞**、**英國南部**和**西歐**。
   - 選取 **定價層**。  此方案以兩種定價層提供︰免費和 OMS 付費層。  免費層會限制每日可收集的資料量、保留期和 Runbook 作業執行階段分鐘數。  OMS 付費層不會限制每日可收集的資料量。  

        > [!NOTE]
        > 儘管系統會顯示 [每 GB (獨立)] 付費層作為選項，但它並不適用。  如果您選取它並在您的訂用帳戶中繼續建立此解決方案，它將會失敗。  當此解決方案正式發行時，將會解決這個問題。<br>如果您使用此解決方案，則它只會使用自動化工作分鐘數並記錄擷取。  方案不會將其他 OMS 節點新增至您的環境。  

6. 在 [OMS 工作區] 刀鋒視窗上提供必要資訊之後，按一下 [建立]。  在驗證資訊及建立工作區時，您可以在功能表的 [通知] 底下追蹤其進度。  您會回到 [新增方案] 刀鋒視窗。  
7. 在 [新增方案] 刀鋒視窗中，選取 [自動化帳戶]。  如果您要建立新的 OMS 工作區，您也必須建立將與先前指定的新 OMS 工作區產生關聯的新自動化帳戶，包括您的 Azure 訂用帳戶、資源群組和區域。  您可以選取 [建立自動化帳戶]，然後在 [新增自動化帳戶] 刀鋒視窗上提供下列資訊︰ 
  - 在 [名稱] 欄位中輸入自動化帳戶的名稱。

    系統會根據所選的 OMS 工作區自動填入所有其他選項，您無法修改這些選項。  Azure 執行身分帳戶是此方案內含 Runbook 的預設驗證方法。  一旦您按一下 [確定]，就會驗證組態選項並建立自動化帳戶。  您可以在功能表的 [通知] 底下追蹤其進度。 

    不然，您可以選取現有的自動化執行身分帳戶。  請注意，您選取的帳戶不能已經連結到另一個 OMS 工作區，否則刀鋒視窗中會顯示一則訊息來通知您。  如果帳戶已經連結，您必須選取不同的自動化執行身分帳戶或建立一個新帳戶。<br><br> ![自動化帳戶已經連結至 OMS 工作區](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 最後在 [新增方案] 刀鋒視窗上選取 [組態]，[參數] 刀鋒視窗隨即出現。<br><br> ![解決方案的 [參數] 窗格](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  在 [參數] 刀鋒視窗上，系統會提示您︰  
   - 指定 [目標資源群組名稱]，這個資源群組名稱內含將由此方案管理的 VM。  您可以輸入多個名稱，然後用逗號加以分隔 (值不區分大小寫)。  如果您想要以訂用帳戶的所有資源群組中的 VM 為目標，則可使用萬用字元。
   - 指定 [VM 排除清單 (字串)]，這是來自目標資源群組的一或多個虛擬機器名稱。  您可以輸入多個名稱，然後用逗號加以分隔 (值不區分大小寫)。  支援使用萬用字元。
   - 選取 [排程]，這是一個週期性日期和時間，可用於啟動及停止目標資源群組中的 VM。  排程會預設設定為 UTC 時區，無法選取不同的區域。  在設定方案後，如果您想要將排程設定為特定時區，請參閱下方的[修改啟動和關閉排程](#modifying-the-startup-and-shutdown-schedule)。
   - 若要從 SendGrid 接收**電子郵件通知**，請接受預設值 [是]，並提供有效的電子郵件地址。  如果您選取 [否]，且稍後決定要接收電子郵件通知，您將需要再次從 Azure Marketplace 重新部署解決方案。  

10. 設定好方案所需的初始設定後，選取 [建立]。  系統會驗證所有的設定，然後嘗試在您的訂用帳戶中部署此方案。  此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。 

## <a name="collection-frequency"></a>收集頻率

系統每隔五分鐘會將自動化作業記錄和作業資料流資料擷取至 Log Analytics 存放庫。  

## <a name="using-the-solution"></a>使用解決方案

當您從 Azure 入口網站的 Log Analytics 工作區中新增 VM 管理解決方案時，[StartStopVM 檢視] 圖格會新增至儀表板。  此圖格會顯示解決方案中已啟動並順利完成的 Runbook 作業計數和圖形表示。<br><br> ![VM 管理 StartStopVM 檢視圖格](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

在您的自動化帳戶中，您可以選取 [工作區] 選項，並在 [Log Analytics] 頁面上選取左窗格中的 [解決方案] 來存取和管理解決方案。  在 [解決方案] 頁面上，從清單中選取 [Start-Stop-VM[工作區]] 解決方案。<br><br> ![Log Analytics 中的 [解決方案] 清單](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

選取此解決方案將會顯示 [Start-Stop-VM[工作區]] 解決方案刀鋒視窗。就像在您的 Log Analytics 工作區中一樣，您可以在其中檢閱重要的詳細資料，例如 [StartStopVM] 圖格，該圖格會顯示解決方案中已啟動並順利完成的 Runbook 作業計數和圖形表示。<br><br> ![自動更新管理解決方案頁面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

您也可以在此執行作業記錄的進一步分析，方法是按一下環圈圖格，然後解決方案儀表板將會顯示作業歷程記錄、預先定義的記錄搜尋查詢，然後切換至 Log Analytics 進階入口網站，以根據您的搜尋查詢進行搜尋。  

所有的父系 Runbook 都包括 *WhatIf* 參數，當將它設為 **True** 時，即可支援詳述在不使用 *WhatIf* 參數執行的情況下，該 Runbook 會採取的確切行為，並驗證是否已將正確的 VM 作為目標。  將 *WhatIf* 設為 **False** 時，Runbook 只會執行其定義的動作。  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>案例 1：每天停止/啟動訂用帳戶或目標資源群組上的 VM 

這是您首次部署解決方案時的預設設定。  例如，您可以將它設定為在您晚上離開公司時停止訂用帳戶上的所有 VM，並在您早上回到辦公室時啟動它們。 當您在部署期間設定排程 **Scheduled-StartVM" 和 **Scheduled-StopVM** 時，它們會啟動和停止目標 VM。
>[!NOTE]
>時區是您設定排程表時間參數時所在的目前時區，但它會以 UTC 格式儲存在 Azure 自動化中。  您不需要執行任何時區轉換，因為它會在部署期間處理。

您可藉由設定兩個變數：**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames 和 **External_ExcludeVMNames** 來控制有哪些 VM 包含於範圍中。  

>[!NOTE]
> 變數 **Target ResourceGroup Names**" 的值會儲存為 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 兩個變數的值。 如需進一步的細微設定，您可以修改這些變數來以不同的資源群組為目標。  針對啟動動作請改用 **External_Start_ResourceGroupNames**，而針對停止動作請改用 **External_Stop_ResourceGroupNames**。 新的 VM 會自動加入至啟動和停止排程。

若要測試及驗證設定，請手動啟動 **ScheduledStartStop_Parent** Runbook，並將 *ACTION* 參數設為 **start** 或 **stop**，然後將 *WhatIf* 參數設為 **true**。<br><br> ![設定 Runbook 的參數](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> 這可讓您預覽將會生效的動作，並在對生產 VM 進行實作之前視需要做出變更。  當您滿意之後，便可將參數設定為 **false** 並手動執行 Runbook，或是讓自動化排程 **Schedule-StartVM** 和 **Schedule-StopVM** 依照您指定的排程自動執行。

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>案例 2：使用標記循序停止/啟動訂用帳戶上的 VM
在於多部 VM 上包含兩個或多個元件並支援分散式工作負載的環境中，能夠決定個別元件的啟動/停止順序是非常重要的。  您可以執行下列步驟來達成此目的。

1. 針對訂用帳戶中於 **External_Start_ResourceGroupNames** 和 **External_Stop*ResourceGroupNames** 變數中設為目標的 VM，新增具有正整數值的 **SequenceStart** 和 **SequenceStop** 標記。  啟動和停止動作將會依遞增順序執行。  若要了解如何標記 VM，請參閱[在 Azure 中標記 Windows 虛擬機器](../virtual-machines/windows/tag.md)和[在 Azure 中標記 Linux 虛擬機器](../virtual-machines/linux/tag.md)
2. 修改排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 以符合您所需的日期和時間，然後啟用排程。  

若要測試及驗證設定，請手動啟動 **SequencedStartStop_Parent** Runbook，並將 *ACTION* 參數設為 **start** 或 **stop**，然後將 *WhatIf* 參數設為 **True**。<br><br> ![設定 Runbook 的參數](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> 這可讓您預覽將會生效的動作，並在對生產 VM 進行實作之前視需要做出變更。  當您滿意之後，便可將參數設定為 **false** 並手動執行 Runbook，或是讓自動化排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 依照您指定的排程自動執行。  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>案例 3：根據 CPU 使用率自動停止/啟動訂用帳戶上的 VM
為了協助管理執行訂用帳戶中 VM 的成本，此解決方案可評估非尖峰期間 (例如下班時間) 未使用的 Azure VM，並在處理器使用率低於 x% 時自動將它們關閉。  

根據預設，解決方案已預先設定為對百分比 CPU 度量進行評估，並以平均使用率為 5% 或更低作為閥值。  這是由下列變數進行控制，並可在預設值不符合需求時加以修改：

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

您只能將動作的目標設為某個訂用帳戶和資源群組，或是特定的 VM 清單，但不可同時設定為兩者。  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定停止動作目標

1. 設定 **External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 變數來指定目標 VM。  
2. 啟用並更新 **Schedule_AutoStop_CreateAlert_Parent** 排程。
3. 執行 **AutoStop_CreateAlert_Parent** Runbook，並將 *ACTION* 參數設為 **start**，然後將 *WhatIf* 參數設為 **True** 以預覽變更。

#### <a name="target-stop-action-by-vm-list"></a>透過 VM 清單設定停止動作目標

1. 執行 **AutoStop_CreateAlert_Parent** Runbook，並將 *ACTION* 參數設為 **start**，在 *VMList* 參數中加入以逗號分隔的 VM 清單，然後將 *WhatIf* 參數設為 **True** 以預覽變更。  
2. 使用以逗號分隔的 VM 清單 (VM1,VM2,VM3) 設定 **External_ExcludeVMNames** 參數。
3. 此案例不會接受 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupnames** 變數。  針對此案例，您將需要建立自己的自動化排程。 如需詳細資訊，請參閱[在 Azure 自動化中排程 Runbook](../automation/automation-schedules.md)。

您已具有會根據 CPU 使用率停止 VM 的排程，現在您需要啟用下列其中一個排程來啟動它們。

* 透過訂用帳戶和資源群組設定啟動動作目標。  請參閱[案例 #1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) 中的步驟以測試和啟用 **Scheduled-StartVM** 排程。
* 透過訂用帳戶、資源群組和標記設定啟動動作目標。  請參閱[案例 #2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) 中的步驟以測試和啟用 "Sequenced-StartVM" 排程。


### <a name="configuring-e-mail-notifications"></a>設定電子郵件通知

若要在部署解決方案後設定電子郵件通知，您可以修改下列三個變數：

* External_EmailFromAddress：指定寄件人電子郵件地址
* External_EmailToAddress：要接收通知電子郵件的逗號分隔電子郵件地址清單 (user@hotmail.com, user@outlook.com)
* External_IsSendEmail：若設為 [是]，您將會收到電子郵件；若要停用電子郵件通知，請將值設為 [否]。   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>修改啟動和關機排程

在此解決方案中管理啟動和關機排程的步驟，與[在 Azure 自動化中排程 Runbook](automation-schedules.md) 中所概述的步驟相同。     

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
尋找 ScheduledStartStop_Parent Runbook 已順利完成的作業 | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
尋找 SequencedStartStop_Parent Runbook 已順利完成的作業 | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>移除解決方案

如果您決定不再需要使用解決方案，您可以將它從自動化帳戶中刪除。  刪除解決方案只會移除 Runbook，而不會刪除在新增解決方案時所建立的排程或變數。  如果您未搭配其他 Runbook 使用這些資產，則必須以手動方式加以刪除。  

若要刪除解決方案，請執行下列步驟：

1.  從您的自動化帳戶，選取左窗格中的 [工作區]。  
2.  在 [解決方案] 頁面上，選取 [Start-Stop-VM[工作區]] 解決方案。  在 [VMManagementSolution[工作區]] 頁面上，按一下功能表中的 [刪除]。<br><br> ![刪除 VM 管理解決方案](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  在 [刪除解決方案] 視窗中，確認您要刪除解決方案。
4.  在驗證資訊並刪除解決方案後，您可以在功能表的 [通知] 底下追蹤其進度。  移除解決方案的程序啟動之後，您將會返回 [解決方案] 頁面。  

此程序並不會刪除自動化帳戶和 Log Analytics 工作區。  如果您不想保留 Log Analytics 工作區，則必須手動刪除它。  此作業也可以從 Azure 入口網站完成。   從 Azure 入口網站的首頁畫面，選取 [Log Analytics]，然後在 [Log Analytics] 刀鋒視窗上選取工作區，並從 [工作區設定] 刀鋒視窗的功能表選取 [刪除]。  
      
## <a name="next-steps"></a>後續步驟

- 若要深入了解如何建構不同的搜尋查詢，以及使用 Log Analytics 檢閱自動化工作記錄檔，請參閱 [Log Analytics 中的記錄檔搜尋](../log-analytics/log-analytics-log-searches.md)
- 若要深入了解 Runbook 執行方式、如何監視 Runbook 工作，以及其他技術性詳細資料，請參閱 [追蹤 Runbook 工作](automation-runbook-execution.md)
- 若要深入了解 Log Analytics 和資料收集來源，請參閱[在 Log Analytics 中收集 Azure 儲存體資料概觀](../log-analytics/log-analytics-azure-storage.md)






   

