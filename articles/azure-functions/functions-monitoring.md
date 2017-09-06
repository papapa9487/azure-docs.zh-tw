---
title: "監視 Azure Functions | Microsoft Docs"
description: "了解如何監視 Azure Functions。"
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, webhook, 動態計算, 無伺服器架構"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2016
ms.author: wesmc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 3ab6123b6acfdec57f1ca71b404c9e1123d1ff6d
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="monitoring-azure-functions"></a>監視 Azure Functions

## <a name="overview"></a>概觀 


每個函式的 [監視] 索引標籤可讓您檢閱每次函式執行。

![Azure Functions 監視索引標籤](./media/functions-monitoring/monitor-tab.png) 

按一下執行，您即可檢閱持續時間、輸入資料、錯誤和相關聯的記錄檔。 這對於函式的偵錯和效能調整很實用。

> [!IMPORTANT]
> 使用 Azure Functions 的[取用主控方案](functions-overview.md#pricing)時，函式應用程式中的 [監視] 磚不會顯示任何資料。 這是因為平台為您動態調整和管理計算執行個體， 所以這些計量對取用方案而言沒有意義。 若要監視函式應用程式的使用量，您應該改用本文中的指引。
> 
> 以下螢幕擷取畫面顯示一個範例︰
> 
> ![監視函式](./media/functions-monitoring/app-service-overview-monitoring.png)


## <a name="real-time-monitoring"></a>即時監視

按一下如下所示的 [即時事件串流]，即可進行即時監視。 

![監視索引標籤的即時事件串流選項](./media/functions-monitoring/monitor-tab-live-event-stream.png)

即時事件資料流會在新的瀏覽器索引標籤中以圖表顯示，如下列範例所示： 

![即時事件串流範例](./media/functions-monitoring/live-event-stream.png)


> [!NOTE]
> 有個已知問題可能會導致您的資料填入失敗。 如果您遇到此種情況，您可能需要關閉包含即時事件串流的瀏覽器索引標籤，然後再按一次 [即時事件串流]，使其正確地填入您的事件串流資料。 

即時事件串流將以圖形呈現函式的下列統計資料︰

* 每秒啟動的執行數
* 每秒完成的執行數
* 每秒失敗的執行數
* 平均執行時間 (以毫秒為單位)。

這些統計資料是即時的，但是執行資料的實際圖表可能會延遲大約 10 秒。


## <a name="monitoring-log-files-from-a-command-line"></a>從命令列監視記錄檔

您可以使用 Azure 命令列介面 (CLI) 1.0 或 PowerShell，在本機工作站上將記錄檔串流處理至命令列工作階段。

### <a name="monitoring-function-app-log-files-with-the-azure-cli-10"></a>使用 Azure CLI 1.0 監視函式應用程式記錄檔

若要開始使用，請[安裝 Azure CLI 1.0](../cli-install-nodejs.md)

使用下列命令，或[從 Azure CLI 1.0 登入 Azure](../xplat-cli-connect.md)中涵蓋的任何其他選項，登入您的 Azure 帳戶。

    azure login

使用下列命令以傳統服務管理模式啟用 Azure CLI 1.0：

    azure config mode asm

如果您有多個訂用帳戶，請使用下列命令來列出訂用帳戶，並將目前的訂用帳戶設定為包含函式應用程式的訂用帳戶。

    azure account list
    azure account set <subscriptionNameOrId>

下列命令會將函式應用程式的記錄檔串流處理至命令列：

    azure site log tail -v <function app name>

### <a name="monitoring-function-app-log-files-with-powershell"></a>使用 PowerShell 監視函式應用程式記錄檔

首先，請[安裝和設定 Azure PowerShell](/powershell/azure/overview)。

執行下列命令來新增您的 Azure 帳戶：

    PS C:\> Add-AzureAccount

如果您有多個訂用帳戶，您可以使用下列命令依照名稱加以列出，以查看目前是否根據 `IsCurrent` 屬性選取正確的訂用帳戶︰

    PS C:\> Get-AzureSubscription

如果您需要將作用中的訂用帳戶設定為包含函式應用程式的訂用帳戶，請使用下列命令︰

    PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription

使用下列命令將記錄檔串流處理至您的 PowerShell 工作階段︰

    PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail

如需詳細資訊，請參閱[法︰串流處理 Web 應用程式的記錄檔](../app-service-web/web-sites-enable-diagnostic-log.md#streamlogs)。 

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

* [測試函數](functions-test-a-function.md)
* [調整函數](functions-scale.md)


