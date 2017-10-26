---
title: "將應用程式從 BizTalk 服務移至 Azure Logic Apps | Microsoft Docs"
description: "將 Azure BizTalk 服務 (MABS) 移動或移轉至 Logic Apps"
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: ladocs; jonfan; mandia
ms.openlocfilehash: df26e4669158e5aa9e3b9a7af888d0dbbba273dd
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2017
---
# <a name="move-from-biztalk-services-to-logic-apps"></a>從 BizTalk 服務移至 Logic Apps

Microsoft Azure BizTalk 服務 (MABS) 即將停用。 您可以使用本主題，將 MABS 整合解決方案移至 Azure Logic Apps。 

## <a name="overview"></a>概觀

BizTalk 服務包含兩個子服務：

1.  Microsoft BizTalk 服務混合式連線
2.  EAI 和 EDI 橋接器架構整合

如果您想要移動混合式連線，[Azure App Service 混合式連線](../app-service/app-service-hybrid-connections.md)說明這項服務的變更和功能。 Azure 混合式連線取代了 BizTalk 服務混合式連線。 Azure 混合式連線隨附於 Azure App Service，而 Azure 入口網站中也有提供。 Azure 混合式連線還提供新的混合式連線管理員，可管理現有的 BizTalk 服務混合式連線，以及您在入口網站中建立的新混合式連線。 Azure App Service 混合式連線已正式上市 (GA)。

至於 EAI 及 EDI 橋接器架構整合，則會以 Logic Apps 取代。 Logic Apps 除了提供與 BizTalk 服務相同的所有功能，還提供更多功能。 Logic Apps 提供以雲端規模使用情況為基礎的工作流程和協調流程功能，可讓您快速輕鬆地使用瀏覽器或使用 Visual Studio 內的工具，來建置複雜的整合解決方案。

下表提供 BizTalk 服務與 Logic Apps 的功能對應。

| BizTalk 服務   | Logic Apps            | 目的                  |
| ------------------ | --------------------- | ---------------------------- |
| 連接器          | 連接器             | 傳送及接收資料   |
| 橋接器             | 邏輯應用程式             | 管線處理器           |
| 驗證階段     | XML 驗證動作      | 驗證 XML 文件的結構描述             |
| 擴充階段       | 資料權杖      | 將屬性升級為訊息或針對路由決策升級屬性             |
| 轉換階段    | 轉換動作      | 將 XML 訊息從一種格式轉換成另一種格式             |
| 解碼階段       | 一般檔案解碼動作      | 從一般檔案轉換成 XML             |
| 編碼階段       |  一般檔案編碼動作      | 從 XML 轉換成一般檔案             |
| 訊息檢查       |  Azure Functions 或 API Apps      | 在您的整合中執行自訂程式碼             |
| 路由動作      |  條件或 Switch      | 將訊息路由傳送至其中一個指定的連接器             |

BizTalk 服務中有許多不同類型的成品。

## <a name="connectors"></a>連接器
BizTalk 服務中的連接器可讓橋接器傳送及接收資料，包括啟用 HTTP 要求/回應互動的雙向橋接器。 在 Logic Apps 中，使用相同的術語。 Logic Apps 中的連接器可達成相同目的，此外還包含超過 140 種連接器，可連線到各式各樣的技術和服務，這些服務包括使用內部部署資料閘道的內部部署服務 (取代 BizTalk 服務所使用的 BizTalk 配接器服務)，以及雲端 SaaS 和 PaaS 服務 (例如 OneDrive、Office365、Dynamics CRM 等其他許多服務)。

BizTalk 服務中的來源限制在 FTP、SFTP 和服務匯流排佇列或主題訂閱。

![](media/logic-apps-move-from-mabs/sources.png)

每個橋接器預設有一個 HTTP 端點，並以橋接器的「執行階段位址」和「相對位址」屬性設定。 若要使用 Logic Apps 達成相同目的，請使用[要求和回應](../connectors/connectors-native-reqres.md)動作。

## <a name="xml-processing-and-bridges"></a>XML 處理和橋接器
BizTalk 服務中的橋接器類似於處理管線。 橋接器可以擷取從連接器收到的資料，並使用該資料執行一些工作，再將它傳送至另一個系統。 Logic Apps 會藉由支援與 BizTalk 服務相同的管線互動模式來執行相同的作業，此外還提供許多其他整合模式。 BizTalk 服務中的 [XML 要求-回覆橋接器](https://msdn.microsoft.com/library/azure/hh689781.aspx)也稱為 VETER 管線，組成此管線的階段可以：

* (V) 驗證
* (E) 擴充
* (T) 轉換
* (E) 擴充
* (R) 路由

如下圖所示，處理會在要求與回覆之間分割，並允許分別控制要求和回覆路徑 (例如針對每個路徑使用不同對應)：

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

此外，XML 單向橋接器會在處理開頭和結尾新增解碼和編碼階段，而傳遞橋接器則包含單一擴充階段。

### <a name="message-processing-and-decodingencoding"></a>訊息處理和解碼/編碼
在 BizTalk 服務中，您可以接收不同類型的 XML 訊息，並判斷所收到訊息的結構描述是否相符。 這會在接收處理管線的**訊息類型**階段中執行。 然後，解碼階段會使用偵測到的訊息類型，並搭配提供的結構描述來將它解碼。 如果結構描述是一般檔案結構描述，它會將傳入的一般檔案轉換成 XML。 

Logic Apps 提供類似的功能。 您可以使用不同的連接器觸發程序 (檔案系統、FTP、HTTP 等)，透過各種不同的通訊協定來接收一般檔案，然後使用[一般檔案解碼](../logic-apps/logic-apps-enterprise-integration-flatfile.md)動作將傳入資料轉換成 XML。 您可以將現有的一般檔案結構描述直接移至 Logic Apps，而不需要進行任何變更，再將結構描述上傳至您的整合帳戶。

### <a name="validation"></a>驗證
將傳入資料轉換成 XML 之後 (或如果 XML 是收到的訊息格式)，執行驗證來判斷訊息是否符合您的 XSD 結構描述。 若要在 Logic Apps 中執行這項作業，請使用 [XML 驗證](../logic-apps/logic-apps-enterprise-integration-xml-validation.md)動作。 同樣地，您可以從 BizTalk 服務使用相同的結構描述，而不需要進行任何變更。

### <a name="transform-messages"></a>轉換訊息
在 BizTalk 服務中，轉換階段會將一種 XML 訊息格式轉換成另一種格式。 這會藉由使用 TRFM 對應工具套用對應來完成。 在 Logic Apps 中，此程序會很類似。 轉換動作會從您的整合帳戶執行對應。 主要差異在於 Logic Apps 中的對應採用 XSLT 格式。 XSLT 可以重複使用您目前已有的 XSLT，包括針對 BizTalk Server 所建立且含有運算質的對應。 

### <a name="routing-rules"></a>路由規則
BizTalk 服務會做出由哪一個端點/連接器來傳送傳入訊息/資料的路由決策。 使用路由篩選選項，即可選取多個預先設定端點的其中一個：

![](media/logic-apps-move-from-mabs/route-filter.png)

Logic Apps 提供具有[條件](../logic-apps/logic-apps-use-logic-app-features.md)和 [Switch](../logic-apps/logic-apps-switch-case.md) 的更複雜邏輯功能，以啟用進階控制流程和路由。 「如果」只有兩個選項，使用**條件**最能達成 BizTalk 服務中的路由篩選轉換。 如果有兩個以上的選項，則使用 **switch**。

### <a name="enrich"></a>擴充
BizTalk 服務處理中的擴充階段會將屬性新增至與所接收資料相關聯的訊息內容。 例如，升級屬性以用於從資料庫尋查路由 (如下所述)，或藉由使用 XPath 運算式來擷取值。 Logic Apps 可從上述動作存取所有關聯式資料輸出，因此您可以直接複寫相同的行為。 例如，使用 `Get Row` SQL 連線動作，您可以從 SQL Server 資料庫傳回資料，並在決策動作中使用資料進行路由。 同樣地，觸發程序傳入的服務匯流排佇列訊息的屬性是可定址的，而使用 xpath 工作流程定義語言運算式的 XPath 也是。

### <a name="use-custom-code"></a>使用自訂程式碼
BizTalk 服務讓您能夠在自己的組件中[執行上傳的自訂程式碼](https://msdn.microsoft.com/library/azure/dn232389.aspx)。 這會由 [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx) 介面來實作。 橋接器中的每個階段包含兩個屬性 (On Enter Inspector 和 On Exit Inspector)，提供您建立來實作此介面的 .Net 類型。 自訂程式碼可讓您對資料執行更複雜的處理，以及在執行通用商務邏輯的組件中重複使用現有的程式碼。 

Logic Apps 提供兩個執行自訂程式碼的主要方法：Azure Functions 和 API Apps。 您可以建立 Azure Functions，並從 Logic Apps 呼叫。 請參閱[透過 Azure Functions 新增並執行適用於 Logic Apps 的自訂程式碼](../logic-apps/logic-apps-azure-functions.md)。 使用 Azure App Service 一部分的 API Apps，可建立您自己的觸發程序和動作。 深入了解[建立自訂 API 來與 Logic Apps 搭配使用](../logic-apps/logic-apps-create-api-app.md)。 

如果您的組件中有從 BizTalk 服務呼叫的自訂程式碼，您可以將此程式碼移至 Azure Functions，或使用 API Apps 建立自訂 API，視您實作的項目而定。 例如，如果您有包裝其他服務的程式碼，且 Logic Apps 沒有該服務的連接器，請建立 API 應用程式，並使用 API 應用程式在邏輯應用程式內提供的動作。 如果您有 Helper 函式或程式庫，則 Azure Functions 可能是最好的選擇。

### <a name="edi-processing-and-trading-partner-management"></a>EDI 處理和交易夥伴管理
BizTalk 服務包含 EDI 和 B2B 處理，並支援 AS2 (Applicability Statement 2)、X12 和 EDIFACT。 Logic Apps 也是。 在 BizTalk 服務中，您可以建立 EDI 橋接器，並在專用追蹤與管理入口網站中建立/管理交易夥伴和協議。

在 Logic Apps 中，這項功能隨附於[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)。 這包含適用於 EDI和 B2B 處理的整合帳戶和 B2B 動作。 [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)可用來建立及管理[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)和[協議](../logic-apps/logic-apps-enterprise-integration-agreements.md)。 建立整合帳戶之後，您可以將一或多個邏輯應用程式與該帳戶產生關聯。 產生關聯之後，您可以使用 B2B 動作來存取邏輯應用程式內的交易夥伴資訊。 提供下列動作：

* AS2 編碼
* AS2 解碼
* X12 編碼
* X12 解碼
* EDIFACT 編碼
* EDIFACT 解碼

不同於 BizTalk 服務，這些動作會從傳輸通訊協定分離。 因此當您建立 Logic Apps 時，您有更大的彈性來選擇要用來傳送及接收資料的連接器。 例如，您可以接收電子郵件附件形式的 X12 檔案，然後在邏輯應用程式中處理這些檔案。 

## <a name="manage-and-monitor"></a>管理和監視
除了交易夥伴管理，BizTalk 服務的專用入口網站還提供追蹤功能，可監視問題並進行疑難排解。 

Logic Apps 在[Azure 入口網站](../logic-apps/logic-apps-monitor-your-logic-apps.md)中提供更豐富的追蹤和監視功能，並提供 [Operations Management Suite B2B 解決方案](../logic-apps/logic-apps-monitor-b2b-message.md)，包括可在您移動時留意事物的行動裝置應用程式。

## <a name="high-availability"></a>高可用性
若要在 BizTalk 服務中達到高可用性 (HA)，您可以在指定區域中使用多個執行個體來分攤處理負載。 使用 Logic Apps，區域中會內建 HA，無需額外費用。 若要在區域之外對 BizTalk 服務中的 B2B 處理進行災害復原，則需要備份和還原程序。 在 Logic Apps 中，提供跨區域主動/被動 [DR 功能](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)，可在不同區域的整合帳戶之間同步處理 B2B 資料，以確保商務持續性。

## <a name="next"></a>下一頁
* [什麼是 Logic Apps](logic-apps-what-are-logic-apps.md)
* [建立第一個邏輯應用程式](logic-apps-create-a-logic-app.md)，或使用[預先建置的範本](logic-apps-create-logic-apps-from-templates.md)來快速上手  
* [檢視可在邏輯應用程式中使用的所有可用連接器](../connectors/apis-list.md)
