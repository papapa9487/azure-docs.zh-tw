---
title: "診斷失敗 - Azure Logic Apps | Microsoft Docs"
description: "用以了解邏輯應用程式失敗所在的常見方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 814e6f93088cdd96b0a663d2a7494b5a11470d99
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="diagnose-logic-app-failures"></a>診斷邏輯應用程式失敗
如果您遇到關於邏輯應用程式的問題或失敗，有數種方法可協助您深入了解失敗所在之處。  

## <a name="azure-portal-tools"></a>Azure 入口網站工具
Azure 入口網站提供許多工具，在每個步驟中診斷每個邏輯應用程式。

### <a name="trigger-history"></a>觸發程序記錄

每個邏輯應用程式至少會有一個觸發程序。 如果您發現無法引發應用程式，首先查看觸發程序歷程記錄以獲得更多資訊。 您可以在邏輯應用程式的主要刀鋒視窗上存取觸發程序歷程記錄。

![找出觸發程序歷程記錄][1]

觸發程序歷程記錄會列出您的邏輯應用程式進行的所有觸發程序嘗試。 您可以按一下每個觸發程序嘗試，進一步探索詳細資料 (特別是觸發程序嘗試所產生的任何輸入或輸出)。 如果您發現失敗的觸發程序，請選取觸發程序嘗試並選擇 [輸出] 連結，以檢閱任何產生的錯誤訊息 (例如︰針對無效的 FTP 認證)。

您可能會看到不同的狀態：

* **已略過**。 此端點經過輪詢以檢查資料，並收到沒有可用資料的回應。
* **已成功**。 觸發程序收到資料可用的回應。 此狀態的原因可能是手動觸發程序、循環觸發程序或輪詢觸發程序。 此狀態通常伴隨著 [已引發] 狀態，但如果程式碼檢視中有一個條件或 SplitOn 命令不符合，則可能不會出現。
* **失敗**。 已產生錯誤。

#### <a name="start-a-trigger-manually"></a>手動啟動觸發程序

如果您希望邏輯應用程式立即檢查可用的觸發程序 (而不需等待下一個循環)，請按一下主要刀鋒視窗上的 [選取觸發程序]  來強制進行檢查。 例如，按一下這個包含 Dropbox 觸發程序的連結，會導致工作流程立即輪詢 Dropbox 中是否有新檔案。

### <a name="run-history"></a>執行記錄

每個引發的觸發程序都會導致一次執行。 您可以從主要刀鋒視窗存取執行資訊，其中包含許多有助於了解工作流程期間發生什麼情況的詳細資訊。

![找出執行記錄][2]

一次執行會顯示下列其中一個狀態：

* **已成功**。 所有動作都已成功。 如果發生失敗，已透過工作流程中後續發生的動作來處理。 也就是，設定為要在失敗動作後執行的動作已處理此失敗。
* **失敗**。 至少有一個動作發生了工作流程中後續的動作無法處理的失敗。
* **已取消**。 工作流程正執行中，但收到了取消要求。
* **執行中**。 工作流程目前正在執行中。 節流處理的流程可能會出現此狀態，或者因為目前的定價方案所致。 如需詳細資訊，請參閱[定價頁面上的動作限制](https://azure.microsoft.com/pricing/details/app-service/plans/)。 設定診斷功能 (執行歷程記錄下方顯示的圖表) 也可以提供所發生的任何節流事件的相關資訊。

當您查看執行歷程記錄時，您可以向內切入來查看詳細資訊。  

#### <a name="trigger-outputs"></a>觸發程序輸出

觸發程序輸出會顯示來自觸發程序的資料。 這些輸出可協助您判斷所有屬性是否如預期般傳回。

> [!NOTE]
> 如果您看見任何不了解的內容，請了解 Azure Logic Apps 如何[處理不同的內容類型](../logic-apps/logic-apps-content-type.md)。
> 

![觸發程序輸出範例][3]

#### <a name="action-inputs-and-outputs"></a>動作輸入和輸出

您可以深入探索動作所接收的輸入和輸出。 此資料有助於了解輸出的大小和圖形，而且有助於尋找任何可能產生的錯誤訊息。

![動作輸入和輸出][4]

## <a name="debug-workflow-runtime"></a>偵錯工作流程執行階段

除了監視執行的輸入、輸出和觸發程序以外，您可以在工作流程中加入一些有助於偵錯的步驟。 
[RequestBin](http://requestb.in) 是一個強大的工具，您可以在工作流程中將其加入為一個步驟。 使用 RequestBin，您就能設定 HTTP 要求檢查器，來判斷 HTTP 要求的確切大小、圖形和格式。 您可以建立 RequestBin 並在邏輯應用程式 HTTP POST 動作中貼上 URL，以及您想要測試的任何內文內容 (例如，運算式或另一個步驟輸出)。 執行邏輯應用程式之後，您可以重新整理 RequestBin，以查看要求從 Logic Apps 引擎產生時是如何形成的。

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png
