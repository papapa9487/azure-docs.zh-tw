---
title: "Azure Functions 的最佳作法 | Microsoft Docs"
description: "了解 Azure Functions 的最佳作法與模式。"
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 模式, 最佳作法, 函數, 事件處理, webhook, 動態計算, 無伺服器架構"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/13/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e0d4a29f59e48cac675e567fd84384b1b60d35f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>將 Azure Functions 效能和可靠性最佳化

本文提供指引來改善函式應用程式的效能和可靠性。 


## <a name="avoid-long-running-functions"></a>避免長時間執行的函式

大型長時間執行的函式可能會造成非預期的逾時問題。 函式可能會因為許多 Node.js 相依性而變大。 匯入相依性也可能會造成載入時間增加，而導致未預期的逾時。 系統會以明確和隱含方式載入相依性。 您的程式碼載入的單一模組可能會載入其本身的其他模組。  

在可能時，將大型函式重構為較小的函式集，共用運作並快速傳回回應。 例如，Webhook 或 HTTP 觸發程序函式可能要求在特定時間限制內的通知回應；Webhook 通常需要立即的回應。 您可以將 HTTP 觸發程序承載傳遞到要由佇列觸發程序函式處理的佇列中。 此方法可讓您延後實際工作，並傳回立即回應。


## <a name="cross-function-communication"></a>跨函式通訊

整合多個函式時，對跨函式通訊使用儲存體佇列通常是最佳作法。  主要原因是儲存體佇列更便宜和容易佈建。 

儲存體佇列中個別訊息大小限制在 64 KB。 如果您需要在函式之間傳遞更大型的訊息，Azure 服務匯流排佇列可用來支援大小上限為 256 KB 的訊息。

如果您需要在處理之前篩選訊息，服務匯流排主題會很實用。

事件中樞對於支援大量通訊很有用。


## <a name="write-functions-to-be-stateless"></a>撰寫無狀態函式 

若可能，Functions 應該是無狀態和具有等冪性。 將任何必要的狀態資訊與您的資料產生關聯。 例如，正在處理訂單就可能具有相關聯的 `state` 成員。 函式本身保持無狀態時，函式可以依據該狀態處理訂單。 

特別建議計時器觸發程序使用等冪函式。 例如，如果您有一天必須執行一次的項目，請編寫它，使得它可在一天中的任何時間執行，並具有相同的結果。 特定日沒有工作時，就可以結束函式。 如果先前的執行無法完成，下一次執行應該會定停止的位置開始。


## <a name="write-defensive-functions"></a>編寫防禦性函式

假設您的函式可能隨時會遇到例外狀況。 設計您的函式，使得它能夠在下一次執行期間從先前的失敗點繼續執行。 假設需要執行下列動作的案例︰

1. 查詢資料庫中的 10,000 個資料列。
2. 對每個資料列建立佇列訊息，來進一步處理向下一行。
 
根據系統的複雜程度，您可能會有︰相關的下游服務行為不當、網路中斷或到達配額限制等等。這所有方面都隨時會影響您的函式。 您必須設計您的函式，以對其做好準備。

如果在插入這些項目中的 5,000 個至佇列以進行處理之後發生失敗，您的程式碼如何因應？ 追蹤集合中您已完成的項目。 否則，您可能下一次又將它們插入。 這對您的工作流程會有嚴重影響。 

如果佇列項目已經過處理，請讓您的函式成為無作業。

利用已針對您在 Azure Functions 平台中所使用元件提供的防禦性措施。 例如，請參閱文件中**處理有害的佇列訊息**中的 [Azure 儲存體佇列觸發程序](functions-bindings-storage-queue.md#trigger)。
 

## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>不要在相同函式應用程式中混用測試和實際執行程式碼

函式應用程式內的 Functions 會共用資源。 例如，共用記憶體。 如果您在生產環境中使用函式應用程式，請勿對它新增與測試相關的函式和資源。 在實際執行程式碼執行期間可能導致發生未預期的額外負荷。

對於在實際執行函式應用程式中載入的項目，請務必小心。 記憶體會在應用程式中的每個函式間平均分配。

如果您在多個 .Net 函式中參考某個共用組件，請將它置於通用的共用資料夾中。 使用類似下列的範例陳述式來參考組件︰ 

    #r "..\Shared\MyAssembly.dll". 

否則，很容易不小心在函式之間部署相同二進位檔但不同行為的多個測試版本。

請勿在實際執行程式碼中使用詳細資訊記錄。 它對效能會有負面影響。



## <a name="use-async-code-but-avoid-blocking-calls"></a>使用非同步程式碼但避免封鎖呼叫

非同步程式設計是建議的最佳作法。 不過，請務必避免在 `Task` 執行個體上參考 `Result` 屬性或呼叫 `Wait` 方法。 這個方法可能會導致執行緒耗盡。


[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

因為 Azure Functions 使用 Azure App Service，所以您也應該充分了解 Azure App Service 指導方針。
* [HTTP 效能最佳化的模式與做法](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)

