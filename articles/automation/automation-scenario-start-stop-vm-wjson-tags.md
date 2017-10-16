---
title: "使用 JSON 格式化標籤來排程 Azure VM 狀態 | Microsoft Docs"
description: "本文示範如何在標籤上使用 JSON 字串，自動排定 VM 的啟動和關閉。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;paulomarquesc
ms.openlocfilehash: cae4020741003be54b133efa121b3c09b859a176
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Azure 自動化案例：使用 JSON 格式化標籤來建立 Azure VM 啟動和關閉的排程
客戶通常會想要排程虛擬機器的啟動與關閉，以協助減少訂用帳戶成本或支援業務和技術需求。

下列案例可讓您在 Azure 中的資源群組層級或虛擬機器層級，使用稱為「排程」的標籤，設定自動啟動和關閉您的 VM。 您可以設定從星期日至星期六具有啟動時間和關閉時間的排程。

我們的確有一些現成可用的選項。 其中包含：

* [虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) ，具有自動調整設定以讓您相應縮小或相應放大。
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) 服務，具有內建的啟動及關閉作業排程功能。

不過，這些選項只支援特定案例，不適用於基礎結構即服務 (IaaS) VM。

當 [排程] 標籤套用至資源群組時，也會套用到該資源群組內的所有虛擬機器。 如果排程也直接套用至 VM，則最後一個排程優先，其順序如下︰

1. 套用至資源群組的排程
2. 套用至資源群組和資源群組中虛擬機器的排程
3. 套用至虛擬機器的排程

此案例基本上會採用具有指定格式的 JSON 字串，並將它加入為「排程」標籤的值。 然後，Runbook 會列出所有資源群組和虛擬機器，並根據稍早所列的案例識別每個 VM 的排程。 接下來，它會循環執行已附加排程的 VM，並評估應採取什麼動作。 例如，它會判斷哪個 VM 需要停止、關閉或忽略。

這些 Runbook 會使用 [Azure 執行身分帳戶](automation-sec-configure-azure-runas-account.md)進行驗證。

## <a name="download-the-runbooks-for-the-scenario"></a>下載案例的 Runbook
此案例包含四個 PowerShell 工作流程 Runbook，您可以從 [TechNet 資源庫](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7)或此專案的 [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) 儲存機制下載這些 Runbook。

| Runbook | 說明 |
| --- | --- |
| Test-ResourceSchedule |檢查每部虛擬機器的排程，並根據排程執行關閉或啟動。 |
| Add-ResourceSchedule |將排程標籤加入至 VM 或資源群組。 |
| Update-ResourceSchedule |以新的排程標籤取代現有的排程標籤以進行修改。 |
| Remove-ResourceSchedule |從 VM 或資源群組移除排程標籤。 |

## <a name="install-and-configure-this-scenario"></a>安裝和設定此案例
### <a name="install-and-publish-the-runbooks"></a>安裝並發佈 Runbook
下載 Runbook 之後，您可以使用[在 Azure 自動化中建立或匯入 Runbook](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation) 中的程序匯入它們。  在每個 Runbook 成功匯入到您的自動化帳戶之後予以發佈。

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>將排程加入至 Test-ResourceSchedule Runbook
請遵循以下步驟以對 Test-ResourceSchedule Runbook 啟用排程。 這個 Runbook 會確認哪些虛擬機器應該啟動、關閉或保持原狀。

1. 從 Azure 入口網站中，開啟您的自動化帳戶，然後按一下Runbook  圖格。
2. 在 [Test-ResourceSchedule] 刀鋒視窗中，按一下 [排程] 圖格。
3. 在 [排程] 刀鋒視窗上，按一下 [加入排程]。
4. 在 [排程] 刀鋒視窗中，選取 [將排程連結至您的 Runbook]。 然後選取 [建立新的排程] 。
5. 在 [新增排程] 刀鋒視窗中，輸入此排程的名稱，例如：HourlyExecution。
6. 若為 [啟動] 排程，請將開始時間設為小時遞增值。
7. 選取 [週期]，然後針對 [重複出現間隔] 選取 [1 小時]。
8. 確認 設定到期 已設為 否，然後按一下建立 以儲存新排程。
9. 在 [排程 Runbook] 選項刀鋒視窗中，選取 [參數與回合設定]。 在 Test-ResourceSchedule 的 [參數] 刀鋒視窗中，於 [SubscriptionName] 欄位 中輸入訂用帳戶的名稱。  這是 Runbook 所需的唯一參數。  完成時，請按一下 [確定] 。

完成時的 Runbook 排程應如下所示︰

![已設定的 Test-ResourceSchedule Runbook](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>格式化 JSON 字串
此解決方案基本上會採用具有指定格式的 JSON 字串，並將它加入為「排程」標籤的值。 然後，Runbook 會列出所有資源群組和虛擬機器，並識別每個虛擬機器的排程。

Runbook 會循環執行已附加排程的虛擬機器，並檢查應採取什麼動作。 以下是解決方案格式化方式的範例：

```json
{
    "TzId": "Eastern Standard Time",
    "0": {
        "S": "11",
        "E": "17"
    },
    "1": {
        "S": "9",
        "E": "19"
    },
    "2": {
        "S": "9",
        "E": "19"
    },
}
```

以下是此結構的部分詳細資訊︰

1. 此 JSON 結構的格式已經過最佳化，可解決 Azure 中單一標籤值 256 個字元的限制。
2.  代表虛擬機器的時區。 此識別碼可以在 PowerShell 工作階段中使用 TimeZoneInfo .NET 類別來取得--**[System.TimeZoneInfo]::GetSystemTimeZones()**。

   ![PowerShell 中的 GetSystemTimeZones](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * 一週日期是以零到六的數字值來表示。 值為零等於星期日。
   * 開始時間以 **S** 屬性表示，其值採用 24 小時制的格式。
   * 結束或關閉時間以 **E** 屬性表示，其值採用 24 小時制的格式。

     如果 **S** 和 **E** 屬性的值各為零 (0)，則虛擬機器會在評估時留在其目前的狀態。
3. 如果您想要跳過一週特定一天的評估，請勿加入該週當天的區段。 在下列範例中，只會評估星期一，而會忽略一周的其他天數：

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>標記資源群組或 VM
若要關閉 VM，您需要標記 VM 或其所在的資源群組。 沒有「排程」標籤的虛擬機器不會進行評估。 因此，不會加以啟動或關閉。

此解決方案有兩種方式可標記資源群組或 VM。 您可以直接從入口網站進行。 或者，您可以使用 Add-ResourceSchedule、Update-ResourceSchedule 和 Remove-ResourceSchedule Runbook。

### <a name="tag-through-the-portal"></a>透過入口網站進行標記
請依照下列步驟，在入口網站中標記虛擬機器或資源群組：

1. 壓平合併 JSON 字串，並確認沒有任何空格。  您的 JSON 字串應該如下所示︰

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. 針對要套用此排程的 VM 或資源群組選取 [標籤]  圖示。

   ![VM 標籤選項](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. 標籤會定義於金鑰/值組之後。 在 [金鑰] 欄位中輸入**排程**，然後將 JSON 字串貼到 [值] 欄位中。 按一下 [儲存] 。 新標籤現在應出現在您的資源的標記清單中。

   ![VM 排程標籤](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>從 PowerShell 進行標記
所有匯入的 Runbook 都在指令碼的開頭包含說明資訊，以描述如何直接從 PowerShell 執行 Runbook。 您可以從 PowerShell 呼叫 Add-ScheduleResource 和 Update-ScheduleResource Runbook。 若要這麼做，您可以傳遞必要的參數，以讓您能夠在入口網站外部的 VM 或資源群組上建立或更新排程標籤。

若要透過 PowerShell 建立、新增和刪除標籤，您必須先 [設定 Azure 的 PowerShell 環境](/powershell/azure/overview)。 在完成安裝後，您可以繼續進行下列步驟。

### <a name="create-a-schedule-tag-with-powershell"></a>使用 PowerShell 建立排程標籤
1. 開啟 PowerShell 工作階段。 然後使用下列範例，以使用您的「執行身分」帳戶進行驗證並指定訂用帳戶︰

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. 定義排程雜湊表。 以下是其建構方式的範例：

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. 定義 Runbook 所需的參數。 在下列範例中，我們會以 VM 為目標。

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    如果您要標記資源群組，請從 $params 雜湊表移除「VMName」  參數，如下所示︰

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. 使用下列參數執行 Add-ResourceSchedule Runbook，以建立 [排程] 標籤︰

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. 若要更新資源群組或虛擬機器的標籤，請使用下列參數執行 **Update-ResourceSchedule** Runbook︰

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>使用 PowerShell 移除排程標籤
1. 開啟 PowerShell 工作階段並執行下列命令，以使用您的「執行身分」帳戶進行驗證並選取和指定訂用帳戶︰

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. 定義 Runbook 所需的參數。 在下列範例中，我們會以 VM 為目標。

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    如果您要從資源群組移除標籤，請從 $params 雜湊表移除「VMName」  參數，如下所示︰

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. 執行 Remove-ResourceSchedule Runbook 以移除 [排程] 標籤︰

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. 若要更新資源群組或虛擬機器標籤，請使用下列參數執行 Remove-ResourceSchedule Runbook︰

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> 建議您主動監視這些 Runbook (和虛擬機器狀態)，以確認您的虛擬機器相應地進行關機並啟動。
>

若要在 Azure 入口網站中檢視 Test-ResourceSchedule Runbook 作業的詳細資料，請選取 Runbook 的 [作業]  圖格。 除了作業和任何例外狀況 (如果發生) 的一般資訊以外，作業摘要也會顯示輸入參數和輸出串流。

[作業摘要]  包括來自輸出、警告和錯誤串流的訊息。 選取 [輸出]  圖格以檢視 Runbook 執行的詳細結果。

![Test-ResourceSchedule 輸出](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>後續步驟
* 若要開始使用 PowerShell 工作流程 Runbook，請參閱 [我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)。
* 若要深入了解 Runbook 類型以及其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)。
* 如需 PowerShell 指令碼支援功能的詳細資訊，請參閱 [Azure 自動化中的原生 PowerShell 指令碼支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
* 若要深入了解 Runbook 記錄和輸出，請參閱 [Azure 自動化中的 Runbook 輸出和訊息](automation-runbook-output-and-messages.md)。
* 若要深入了解 Azure 執行身分帳戶以及如何使用它來驗證 Runbook，請參閱[使用 Azure 執行身分帳戶驗證 Runbook](automation-sec-configure-azure-runas-account.md)。
