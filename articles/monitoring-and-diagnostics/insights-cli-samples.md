---
title: "Azure 監視器 CLI 1.0 快速入門範例。 | Microsoft Docs"
description: "Azure 監視器功能的範例 CLI 1.0 命令。 Azure 監視器是一項 Microsoft Azure 服務，可讓您根據設定的遙測資料值、自動調整雲端服務、虛擬機器和 Web Apps，傳送警示通知或呼叫 Web URL。"
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: ec4512500dc3c77a40d2ebd1e6b460d5bb005811
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="azure-monitor--cross-platform-cli-10-quick-start-samples"></a>Azure 監視器跨平台 CLI 1.0 快速入門範例
本文說明可協助您存取 Azure 監視器 功能的命令列介面 (CLI) 命令範例。 Azure 監視器可讓您根據設定的遙測資料值、自動調整雲端服務、虛擬機器和 Web Apps，以及傳送警示通知，或呼叫 Web URL。

> [!NOTE]
> 自 2016 年 9 月 25 日起，「Azure 監視器」是以前所謂「Azure Insights」的新名稱。 不過，命名空間和下列命令中仍有 "insights"。
> 
> 

## <a name="prerequisites"></a>必要條件
如果您尚未安裝 Azure CLI，請參閱 [安裝 Azure CLI](../cli-install-nodejs.md)。 若您不熟悉 Azure CLI，可閱讀 [搭配使用 Mac、Linux 和 Windows 適用的 Azure CLI 與 Azure Resource Manager](../xplat-cli-azure-resource-manager.md)以深入了解。

在 Windows 中，從 [Node.js 網站](https://nodejs.org/)安裝 npm。 以「以系統管理員身分執行」權限使用 CMD.exe 完成安裝後，從安裝 npm 的資料夾中執行下列命令︰

```console
npm install azure-cli --global
```

接下來，瀏覽至您想要的資料夾/位置，在命令列輸入︰

```console
azure help
```

## <a name="log-in-to-azure"></a>登入 Azure
第一步是登入您的 Azure 帳戶。

```console
azure login
```

執行此命令之後，您必須透過螢幕上的指示來登入。 之後，您會看到您的帳戶、TenantId 和預設的訂用帳戶識別碼。所有命令都會在您的預設訂用帳戶內容中運作。

若要列出目前訂用帳戶的詳細資料，使用下列命令。

```console
azure account show
```

若要將使用中的內容變更為其他訂用帳戶，請使用下列命令。

```console
azure account set "subscription ID or subscription name"
```

若要使用 Azure Resource Manager 和 Azure 監視器命令，您必須處於 Azure Resource Manager 模式。

```console
azure config mode arm
```

若要檢視所有支援的 Azure 監視器命令清單，執行下列命令。

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>檢視訂用帳戶的活動記錄檔
若要檢視活動記錄檔事件的清單，請執行下列命令。

```console
azure insights logs list [options]
```

嘗試下列命令以檢視所有可用的選項。

```console
azure insights logs list -help
```

以下是依 resourceGroup 列出記錄檔清單的範例

```console
azure insights logs list --resourceGroup "myrg1"
```

依 caller 列出記錄檔的範例

```console
azure insights logs list --caller "myname@company.com"
```

依 caller 列出開始與結束日期之間在資源類型上的記錄檔的範例

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>使用警示
您可以在此區段中運用資訊來使用警示。

### <a name="get-alert-rules-in-a-resource-group"></a>取得資源群組中的警示規則
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>建立度量警示規則
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-webtest-alert-rule"></a>建立 WebTest 警示規則
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>刪除警示規則
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>記錄檔的設定檔
在此區段中運用資訊來使用記錄檔的設定檔。

### <a name="get-a-log-profile"></a>取得記錄檔設定檔
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>新增沒有保留期的記錄檔設定檔
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>移除記錄檔設定檔
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>新增有保留期的記錄檔設定檔
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>新增有保留期與 EventHub 的記錄檔設定檔
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>診斷
在此區段中運用資訊來使用診斷設定。

### <a name="get-a-diagnostic-setting"></a>取得診斷設定
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>停用診斷設定
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>啟用沒有保留期的診斷設定
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
利用此區段中的資訊來使用自動調整設定。 您必須修改這些範例。

### <a name="get-autoscale-settings-for-a-resource-group"></a>取得資源群組的自動調整設定
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>依名稱取得資源群組中的自動調整設定
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>設定自動調整設定
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
