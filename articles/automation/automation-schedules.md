---
title: "Azure 自動化中的排程 | Microsoft Docs"
description: "自動化排程是用來排程讓 Azure 自動化中的 Runbook 自動啟動。 描述如何建立和管理排程，以便以特定時間或循環排程來自動啟動 Runbook。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 446dc79202938ee96cbb090345d9c191060afe76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>在 Azure 自動化中排程 Runbook
若要排程在指定時間啟動 Azure 自動化中的 Runbook，您會將它連結到一個或多個排程。 在 Azure 傳統入口網站和 Azure 入口網站中，Runbook 的排程可以設定為執行一次，或以每小時或每日重複發生的排程來執行，您也可以將 Runbook 排程為每週、每月、一週或一個月當中的特定幾天，或每月的特定一天來執行。  Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。

> [!NOTE]
> 排程目前不支援 Azure Automation DSC 組態。
> 
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell Cmdlet
下表中的 Cmdlet 是用來在 Azure 自動化中透過 Windows PowerShell 建立和管理排程。 它們是隨著 [Azure PowerShell 模組](/powershell/azure/overview)的一部分推出。

| Cmdlet | 說明 |
|:--- |:--- |
| **Azure Resource Manager 的 Cmdlet** | |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |擷取排程。 |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |建立新排程。 |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |移除排程。 |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |設定現有排程的屬性。 |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |擷取排程的 Runbook。 |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |將 Runbook 與排程相關聯。 |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |從排程分離 Runbook。 |
| **Azure 服務管理的 Cmdlet** | |
| [Get-AzureAutomationSchedule](/powershell/module/azure/get-azureautomationschedule?view=azuresmps-3.7.0) |擷取排程。 |
| [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) |建立新排程。 |
| [Remove-AzureAutomationSchedule](/powershell/module/azure/remove-azureautomationschedule?view=azuresmps-3.7.0) |移除排程。 |
| [Set-AzureAutomationSchedule](/powershell/module/azure/set-azureautomationschedule?view=azuresmps-3.7.0) |設定現有排程的屬性。 |
| [Get-AzureAutomationScheduledRunbook](/powershell/module/azure/get-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |擷取排程的 Runbook。 |
| [Register-AzureAutomationScheduledRunbook](/powershell/module/azure/register-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |將 Runbook 與排程相關聯。 |
| [Unregister-AzureAutomationScheduledRunbook](/powershell/module/azure/unregister-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |從排程分離 Runbook。 |

## <a name="creating-a-schedule"></a>建立排程
您可以在 Azure 入口網站、傳統入口網站或使用 Windows PowerShell 為 Runbook 建立新排程。 當您使用 Azure 傳統入口網站或 Azure 入口網站將 Runbook 連結至排程時，也可以選擇建立新的排程。

> [!NOTE]
> 執行新的排程工作時，Azure 自動化會使用自動化帳戶中的最新模組。  為了避免影響 Runbook 和它們所自動化的處理序，您應該先使用專用於測試的自動化帳戶測試任何具有連結排程的 Runbook。  這會驗證您排程的 Runbook 會持續正確運作，否則，您可以進一步進行疑難排解，並套用任何必要變更，再將已更新的 Runbook 版本移轉至生產環境。  
>  除非您已選取 [模組] 中的[更新 Azure 模組](automation-update-azure-modules.md)選項進行手動更新，否則自動化帳戶不會自動取得任何新版本的模組。 
>  

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>在 Azure 入口網站中建立新排程
1. 在 Azure 入口網站中，從您的自動化帳戶選取左側 [共用資源] 區段底下的 [排程]。 
2. 在分頁的頂端按一下 [加入排程]。
4. 在 [新增排程] 窗格中，為新排程輸入 [名稱] 並選擇性地輸入 [描述]。
5. 選取 [一次] 或 [週期]，以選取排程將會執行一次或以週期性的排程執行。  如果選取 一次，請指定 開始時間，然後按一下建立。  如果選取 [週期]，則請指定 [開始時間] 和所需的 Runbook 重複頻率：依 [小時]、[天]、[週] 還是 [月] 執行。  如果您在下拉式清單中選取 [週] 或 [月]，窗格會出現 [週期選項]，一經選取，就會顯示 [週期選項] 窗格，如果您選取了 [週]，將可以進一步選取星期幾。  如果您已選取 月，則可以在行事曆上選擇要依 工作日 或當月的特定幾天，最後則是您是否要在當月最後一天執行，然後按一下確定。   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>在 Azure 傳統入口網站中建立新排程
1. 在 Azure 傳統入口網站中，選取 [自動化]，然後選取自動化帳戶的名稱。
2. 選取 [ **資產** ] 索引標籤。
3. 在視窗的底端按一下 [ **加入設定**]。
4. 按一下 [ **加入排程**]。
5. 為新新排程輸入 [名稱] 並選擇性地輸入 [描述]。 您的排程可以 [一次]、[每小時]、[每日]、[每週] 或 [每月] 執行。
6. 視您選取的排程類型而定，指定 [ **開始時間** ] 和其他選項。

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>使用 Windows PowerShell 建立新排程
您可以使用 [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) Cmdlet 在 Azure 自動化中為傳統 Runbook 建立新的排程，或使用 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) Cmdlet 在 Azure 入口網站中為 Runbook 建立新的排程。 您必須指定排程的開始時間，以及其應該執行的頻率。

下列範例命令顯示如何使用 Azure Resource Manager Cmdlet，建立會在每月 15 號到 30 號執行的排程。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

下列範例命令顯示如何使用 Azure 服務管理 Cmdlet 建立新的排程，從 2015 年 1 月 20 日起於每日下午 3:30 執行。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>將排程連結至 Runbook
Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。 如果 Runbook 有參數，您可以為它們提供值。 您必須為任何必要參數提供值，並可以提供任何選擇性參數的值。  每次此排程啟動 Runbook 時會使用這些值。  您可以將相同的 Runbook 附加到另一個排程，並指定不同的參數值。

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>使用 Azure 入口網站將排程連結至 Runbook
1. 在 Azure 入口網站中，從您的自動化帳戶選取左側 [程序自動化] 區段底下的 [Runbook]。
2. 按一下要排程的 Runbook 名稱。
3. 如果 Runbook 目前未連結至排程，則將提供您建立新排程或連結至現有排程的選項。  
4. 如果 Runbook 有參數，您可以選取 [修改執行設定 (預設值: Azure)] 選項，隨即便會顯示 [參數] 窗格供您據以輸入資訊。  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>使用 Azure 傳統入口網站將排程連結至 Runbook
1. 在 Azure 傳統入口網站中，選取 [自動化]，然後按一下自動化帳戶的名稱。
2. 選取 [ **Runbook** ] 索引標籤。
3. 按一下要排程的 Runbook 名稱。
4. 按一下 [ **排程** ] 索引標籤。
5. 如果 Runbook 目前未連結至排程，則將提供您**連結至新排程**或**連結至現有排程**的選項。  如果 Runbook 目前連結至排程，請按一下視窗底部的 **連結** 來存取這些選項。
6. 如果 Runbook 有參數，系統會提示您輸入它們的值。  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>使用 Windows PowerShell 將排程連結至 Runbook
您可以使用 [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) 將排程連結至傳統 Runbook，或使用 [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) Cmdlet 將排程連結至 Azure 入口網站中的 Runbook。  您可以使用 Parameters 參數來指定 Runbook 參數的值。 請參閱 [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) ，以取得指定參數值的詳細資訊。

下列範例命令顯示如何使用 Azure Resource Manager Cmdlet 與參數，將排程連結至 Runbook。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
下列範例命令顯示如何使用 Azure 服務管理 Cmdlet 與參數來連結排程。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>停用排程
停用排程時，與其連結的任何 Runbook 無法再依該排程執行。 您可以手動停用排程，或在建立排程時為具有頻率的排程設定到期時間。 當到達到期時間時，就會停用排程。

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>從 Azure 入口網站停用排程
1. 在 Azure 入口網站中，從您的自動化帳戶選取左側 [共用資源] 區段底下的 [排程]。
2. 按一下排程的名稱以開啟詳細資料窗格。
3. 將 [已啟用] 變更為 [否]。

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>從 Azure 傳統入口網站停用排程
您可以在 Azure 傳統入口網站中從排程的 [排程詳細資料] 頁面停用排程。

1. 在 Azure 傳統入口網站中，選取 [自動化]，然後按一下自動化帳戶的名稱。
2. 選取 [資產] 索引標籤。
3. 按一下排程的名稱以開啟其詳細資料頁面。
4. 將 [已啟用] 變更為 [否]。

### <a name="to-disable-a-schedule-with-windows-powershell"></a>使用 Windows PowerShell 停用排程
您可以使用 [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) Cmdlet 為傳統 Runbook 變更現有排程的屬性，或使用 [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) Cmdlet 為 Azure 入口網站中的 Runbook 變更現有排程的屬性。 若要停用排程，請為 **IsEnabled** 參數指定 **false**。

下列範例命令顯示如何使用 Azure Resource Manager Cmdlet 來停用 Runbook 的排程。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

下列範例命令顯示如何使用 Azure 服務管理 Cmdlet 來停用排程。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>後續步驟
* 若要在 Azure 自動化中開始使用 Runbook，請參閱 [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 

