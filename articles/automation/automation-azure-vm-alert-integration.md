---
title: " 使用自動化 Runbook 補救 Azure VM 警示 | Microsoft Docs"
description: "本文示範如何使用 Azure 自動化 Runbook 整合 Azure 虛擬機器警示，並自動補救問題"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: csand;magoedte
ms.openlocfilehash: 18cccc88ab74235722e2f4886671fc483ab67da8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Azure 自動化案例 - 補救 Azure VM 警示
Azure 自動化和 Azure 虛擬機器發行了一項新功能，可讓您設定虛擬機器 (VM) 警示以便執行自動化 Runbook。 這項新功能可讓您自動執行標準補救以回應 VM 警示，例如重新啟動或停止 VM。

先前，在建立 VM 警示規則期間，您能夠 [指定自動化 Webhook](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) 給 Runbook，以便在觸發警示時執行 Runbook。 不過，您需要進行建立 Runbook 的工作，為 Runbook 建立 Webhook，然後在警示規則建立期間複製並貼上 Webhook。 在這個新版本中，此程序變得很容易，因為您在建立警示規則時可以直接從清單中選擇 Runbook，還可以選擇自動化帳戶來執行 Runbook，或輕鬆地建立帳戶。

在本文中，我們說明設定 Azure VM 警示及設定每當警示觸發時就執行自動化 Runbook 是多麼容易。 範例案例包括：當記憶體使用量由於 VM 上的應用程式有記憶體流失而超過某個臨界值時重新啟動 VM，或是當 CPU 使用者時間在過去一小時已低於 1% 且不在使用中時停止 VM。 我們也將說明在您的自動化帳戶中自動建立服務主體，如何簡化在 Azure 警示補救中使用 Runbook。

## <a name="create-an-alert-on-a-vm"></a>在 VM 上建立警示
執行下列步驟來設定警示，以在符合其臨界值時啟動 Runbook。

> [!NOTE]
> 在此版本中，我們只支援 V2 虛擬機器，即將新增傳統 VM 的支援。  
> 
> 

1. 登入 Azure 入口網站，然後按一下 [虛擬機器] 。  
2. 選取其中一部虛擬機器。  
3. 在虛擬機器畫面的 [監視] 區段中，按一下 [警示規則]。
4. 在 [警示規則] 窗格上，按一下 [新增警示]。

這會開啟 [新增警示規則] 頁面，您可以在此設定警示的條件，並選擇下列其中一個或所有選項︰傳送電子郵件給某人、使用 Webhook 將警示轉寄至另一個系統，及/或執行自動化 Runbook 以回應修補問題的嘗試。

## <a name="configure-a-runbook"></a>設定 Runbook
若要設定 Runbook 以在符合 VM 警示臨界值時執行，請選取 [自動化 Runbook] 。 在 [設定 Runbook] 頁面中，您可以選取要執行的 Runbook 以及用來執行 Runbook 的自動化帳戶。

![設定自動化 Runbook 並建立新的自動化帳戶](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> 在此版本中，您可以從服務所提供的三個 Runbook 進行選擇 – 重新啟動 VM、停止 VM 或移除 VM (刪除它)。  在未來版本中將提供選取其他 Runbook 或您自己的其中一個 Runbook 的功能。
> 
> 

![可從中選擇的 Runbook](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

在您選取其中一個可用的 Runbook 之後，[自動化帳戶]  下拉式清單隨即出現，以便您選取用來執行 Runbook 的自動化帳戶。 Runbook 必須在您的 Azure 訂用帳戶中的 [自動化帳戶](automation-security-overview.md) 內容中執行。 您可以選取已經建立的自動化帳戶，也可以為自己建立新的自動化帳戶。

所提供的 Runbook 會使用服務主體進行Azure 驗證。 如果您選擇在其中一個現有的自動化帳戶中執行 Runbook，我們會自動為您建立服務主體。 如果您選擇建立新的自動化帳戶，則我們會自動建立帳戶和服務主體。 在這兩種情況下，也會在自動化帳戶中建立兩項資產 – 名為 **AzureRunAsCertificate** 的憑證資產和名為 **AzureRunAsConnection** 的連接資產。 Runbook 會使用 **AzureRunAsConnection** 向 Azure 驗證，以便對虛擬機器執行管理動作。

> [!NOTE]
> 服務主體會建立於訂用帳戶範圍並獲得參與者角色。 需要此角色，有權執行自動化 Runbook 的帳戶才能管理 Azure VM。  建立自動化帳戶及/或服務主體是一次性事件。 一旦建立，您可以使用該帳戶來對其他 Azure VM 警示執行 Runbook。
> 
> 

當您按一下 [確定]  時，便會設定警示，如果您選取要建立新的自動化帳戶的選項，則會隨著服務主體一起建立。  這可能需要幾秒鐘才會完成。  

![正在設定的 Runbook](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

完成設定之後，您在 [新增警示規則] 頁面中會看到 Runbook 名稱。

![已設定的 Runbook](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

在 [新增警示規則] 頁面中，按一下 [確定]。  將會建立警示規則，並於虛擬機器處於執行中狀態時啟動。

### <a name="enable-or-disable-a-runbook"></a>啟用或停用 Runbook
如果您有針對警示設定的 Runbook，您可以將它停用，而不需移除 Runbook 組態。 這可讓警示保持執行中，而且您或許可測試某些警示規則，稍後重新啟用 Runbook。

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>建立與 Azure 警示搭配運作的 Runbook
當您選擇 Runbook 做為 Azure 警示規則的一部分時，Runbook 內必須有負責管理所收到之警示資料的邏輯。  在警示規則中設定 Runbook 時，便會為 Runbook 建立 Webhook；該 Webhook 接著會在每次觸發警示時用來啟動 Runbook。  實際用來啟動 Runbook 的呼叫是 Webhook URL 的 HTTP POST 要求。 POST 要求的本文包含 JSON 格式的物件，此物件中包含與警示相關的實用屬性。  如下面所見，警示資料包含 subscriptionID、resourceGroupName、resourceName 和 resourceType 等詳細資料。

### <a name="example-of-alert-data"></a>警示資料的範例
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

當自動化 Webhook 服務收到 HTTP POST 時，它會擷取警示資料，並將它傳遞給 WebhookData Runbook 輸入參數中的 Runbook。  以下範例 Runbook 會示範如何使用 WebhookData 參數，以及擷取警示資料並用它來管理觸發警示的 Azure 資源。

### <a name="example-runbook"></a>範例 Runbook
```
#  This runbook restarts an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>摘要
當您在 Azure VM 上設定警示時，您現在能夠輕鬆地設定自動化 Runbook，以在警示觸發時自動執行補救動作。 在此版本中，您可以根據您的警示情況，從 Runbook 中選擇重新啟動、停止或刪除 VM。 為了讓您能夠控制警示觸發時自動執行的動作 (通知、疑難排解、補救)，這只是開始。

## <a name="next-steps"></a>後續步驟
* 若要開始使用圖形化 Runbook，請參閱 [我的第一個圖形化 Runbook](automation-first-runbook-graphical.md)
* 若要開始使用 PowerShell 工作流程 Runbook，請參閱 [我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)
* 若要深入了解 Runbook 類型、其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)

