---
title: "適用於 Azure Logic Apps 的連接器 | Microsoft Docs"
description: "選擇所有受 Microsoft 管理的可用連接器，以建置和建立邏輯應用程式"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: mandia; ladocs
ms.openlocfilehash: c14ac7592efabfec8668d7437463e2d8771ee072
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connectors-list"></a>連接器清單
> [!TIP]
> [A-Z 完整清單](#az) (在本主題中) 會列出您可以在邏輯應用程式中使用的所有可用連接器。 [連接器詳細資料](/connectors/)既會列出 Swagger 中所定義的任何觸發程序和動作，也會列出每個連接器的限制。

連接器是建立邏輯應用程式時不可或缺的部分。 使用這些連接器，您可以真的擴展內部部署和雲端應用程式，對您建立的資料以及已經有的資料執行不同的動作。 連接器可分為下列幾類︰ 

* **標準連接器**︰當您使用邏輯應用程式時自動取得和納入。 例如：服務匯流排、Power BI、Oracle Database、OneDrive 等等。

* **整合帳戶連接器**︰當您購買整合帳戶時取得。 使用這些連接器，您可以轉換和驗證 XML、透過 AS2 / X12 / EDIFACT 處理企業對企業訊息，以及進行一般檔案的編碼和解碼。 如果您使用 BizTalk Server，這些連接器就很適合用於將 BizTalk 工作流程擴展至 Azure。  

    BizTalk Server 也有 [Logic Apps 配接器](https://msdn.microsoft.com/library/mt787163.aspx)，其包含從邏輯應用程式接收和傳送至邏輯應用程式。

* **企業連接器**︰包含 MQ 和 SAP。 需要支付額外費用。 

[Logic Apps 價格](https://azure.microsoft.com/pricing/details/logic-apps/)和[計價模式](../logic-apps/logic-apps-pricing.md)會提供有關成本的更多詳細資料。 

## <a name="popular-connectors"></a>熱門的連接器
有數千個應用程式和數百萬次執行作業使用這些連接器，成功處理資料和資訊。 下表列出最熱門以及使用者喜愛的一些連接器：

| |  |  |  |
| --- | --- | --- | --- |
| [![API 圖示][AzureBlobStorageicon]<br/>**Azure Blob<br/>儲存體**][AzureBlobStoragedoc] | 如果您想要透過儲存體帳戶自動執行任何工作，您應該看看此連接器。 支援 CRUD (建立、讀取、更新、刪除) 作業。 | [![API 圖示][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | 建立可執行 C# 或 node.js 之自訂程式碼片段的函式，然後在邏輯應用程式中使用這些函式。  |
| [![API 圖示][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | 詢問度最高的連接器之一。 它具有觸發程序和動作，有助於自動執行潛在客戶的工作流程等等。 | [![API 圖示][Event-Hubs-icon]<br/>**事件中樞**][event-hubs-doc] | 在事件中樞上取用和發佈事件。 例如，您可以使用事件中樞從邏輯應用程式取得輸出，然後將輸出傳送給即時分析提供者。 |
| [![API 圖示][FTPicon]<br/>**FTP**][FTPdoc] | 如果可從網際網路存取您的 FTP 伺服器，您可以自動執行工作流程，以便使用檔案和資料夾。 <br/><br/>SFTP 也適用於 SFTP 連接器。 | [![API 圖示][HTTPicon]<br/>**HTTP**][httpdoc] | 使用邏輯應用程式透過 HTTP 與任何端點通訊。 |
| [![API 圖示][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | 許多觸發程序，以及更多的動作會使用 Office 365 電子郵件與您工作流程內的事件。 <br/><br/>此連接器包含*核准電子郵件*動作，可核准休假要求、費用報告等。 <br/><br/>Office 365 使用者也可使用 Office 365 使用者連接器。| [![API 圖示][HTTP-Requesticon]<br/>**要求/回應**][HTTP-Requestdoc] | 此連接器會提供 HTTPS URL。 當邏輯應用程式收到此 URL 的要求時，邏輯應用程式就會啟動。 |
| [![API 圖示][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | 使用 Salesforce 帳戶輕鬆登入，以取得物件 (例如潛在客戶等等) 的存取權。 |  [![API 圖示][Service-Busicon]<br/>**服務匯流排**][Service-Busdoc] | 邏輯應用程式中最熱門的連接器，其包含的觸發程序和動作可進行非同步傳訊，以及發佈/訂閱佇列、訂用帳戶和主題。 |
|  [![API 圖示][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | 如果您使用 SharePoint 執行任何動作，並且受惠於自動化作業，我們建議看看此連接器。 可以搭配內部部署 SharePoint 和 SharePoint Online 使用 | [![API 圖示][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | 最常用的連接器之一，它可以連線到內部部署 SQL Server 和 Azure SQL Database。 | 
| [![API 圖示][Twittericon]<br/>**Twitter**][Twitterdoc] | 使用 Twitter 帳戶輕鬆登入，然後在新推文發佈時啟動工作流程。 接著，將這些推文儲存至 SQL Database 或 SharePoint 清單。 | | | 

## <a name="integration-account-connectors"></a>整合帳戶連接器 

企業整合套件 (EIP) 包含 BizTalk Server 社群所熟知的連接器。 當您購買[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)時，您也會取得下列連接器︰ 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API 圖示][as2icon]<br/>**AS2</br>解碼**][as2decode] | [![API 圖示][as2icon]<br/>**AS2</br>編碼**][as2encode] | [![API 圖示][x12icon]<br/>**EDIFACT</br>解碼**][EDIFACTdecode] | [![API 圖示][x12icon]<br/>**EDIFACT</br>編碼**][EDIFACTencode] |
[![API 圖示][flatfileicon]<br/>**一般檔案</br>編碼**][flatfiledoc] | [![API 圖示][flatfiledecodeicon]<br/>**一般檔案</br>解碼**][flatfiledecodedoc] | [![API 圖示][integrationaccounticon]<br/>**整合<br/>帳戶**][integrationaccountdoc] | [![API 圖示][xmltransformicon]<br/>**轉換<br/>XML**][xmltransformdoc] |
| [![API 圖示][x12icon]<br/>**X12</br>解碼**][x12decode] | [![API 圖示][x12icon]<br/>**X12</br>編碼**][x12encode] | [![API 圖示][xmlvalidateicon]<br/>**XML<br/>驗證**][xmlvalidatedoc] | |

## <a name="enterprise-connectors"></a>企業連接器

連線至您的邏輯應用程式內的企業應用程式。

|  |  |
| --- | --- |
|[![API 圖示][MQicon]<br/>**MQ**][mqdoc]|[![API 圖示][SAPicon]<br/>**SAP**][sapconnector]|


## <a name="az"></a>A-Z 完整清單

[連接器詳細資料](/connectors/)既會列出 Swagger 中所定義的任何觸發程序和動作，也會列出每個連接器的限制。

| | | | | | | | | | | | | |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [**1**](#1) | [**A**](#a) | [**B**](#b) | [**C**](#c) | [**D**](#d) | [**E**](#e) | [**F**](#f) | [**G**](#g) | [**H**](#h) | [**I**](#i) | [**J**](#j) | [**L**](#l) | [**M**](#m) |
| [**N**](#n) | [**O**](#o) | [**P**](#p) | [**R**](#r) | [**S**](#s) | [**T**](#t) | [**U**](#u) | [**V**](#v) | [**W**](#w) | [**X**](#x) | [**Y**](#y) | [**Z**](#z) | | 

| | |
|---|---|
|<a name="1"></a>10to8 Appointment Scheduling<br/><br/><a name="a"></a>Act!<br/>Adobe Creative Cloud<br/>appFigures<br/>[AS2][as2doc]<br/>Asana<br/>Azure Active Directory (AD)<br/>Azure API 管理<br/>Azure App Service<br/>Azure 應用程式<br/>Azure 自動化<br/>[Azure Blob 儲存體][azureblobstoragedoc]<br/>Azure Data Lake<br/>Azure DocumentDB (Cosmos DB)<br/>[Azure Functions][azure-functionsdoc]<br/>[Azure Logic Apps][nested-logic-appdoc]<br/>AzureML<br/>Azure 佇列<br/>Azure Resource Manager<br/>[Azure SQL Database][sql-serverdoc]<br/><br/><a name="b"></a>Basecamp 2<br/>Basecamp 3<br/>批次<br/>Benchmark Email<br/>Bing 搜尋<br/>Bitbucket<br/>Bitly<br/>BizTalk Server<br/>Blogger<br/>Box<br/>Buffer<br/><br/><a name="c"></a>Calendly<br/>Campfire<br/>Capsule CRM<br/>Chatter<br/>Cognito Forms<br/>辨識服務電腦視覺 API<br/>辨識服務臉部 API<br/>辨識服務 LUIS<br/>辨識服務文字分析<br/>Common Data Service<br/>內容轉換<br/>Control-Terminate<br/>[自訂 API / Web 應用程式][api/web-appdoc]<br/><br/><a name="d"></a>Data Operations<br/>[DB2][db2doc]<br/>Disqus<br/>DocuSign<br/>Do Until<br/>Dropbox<br/>[Dynamics 365 CRM Online][Dynamics-365doc]<br/>Dynamics 365 for Financials<br/>Dynamics 365 for Operations<br/>Dynamics NAV<br/><br/><a name="e"></a>Easy Redmine<br/>EDIFACT<br/>[事件中樞][event-hubs-doc]<br/>Eventbrite<br/><br/><a name="f"></a>Facebook<br/>[檔案系統][filesystemdoc]<br/>[一般檔案][flatfiledoc]<br/>FreshBooks<br/>Freshdesk!<br/>FreshService<br/>[FTP][ftpdoc]<br/><br/><a name="g"></a>GitHub<br/>Gmail<br/>Google 行事曆<br/>Google 聯絡人<br/>Google 雲端硬碟<br/>Google 試算表<br/>Google 工作表<br/>GoToMeeting<br/>GoToTraining<br/>GoToWebinar<br/><br/><a name="h"></a>Harvest<br/>HelloSign<br/>HipChat<br/>[HTTP][httpdoc]<br/>[HTTP + Swagger][http-swaggerdoc]<br/>[HTTP Webhook][webhookdoc]<br/><br/><a name="i"></a>[Informix][informixdoc]<br/>Infusionsoft<br/>Inoreader<br/>Insightly<br/>Instagram<br/>Instapaper<br/>整合帳戶<br/>Intercom | <a name="j"></a>JotForm<br/>JIRA<br/><br/><a name="l"></a>LeanKit<br/>LiveChat<br/><br/><a name="m"></a>MailChimp<br/>Mandrill<br/>中型<br/>Microsoft Forms<br/>Microsoft Teams<br/>Microsoft Translator<br/>[MQ][mqdoc]<br/>MSN 天氣<br/>Muhimbi PDF<br/>MySQL<br/><br/><a name="n"></a>Nexmo<br/><br/><a name="o"></a>[Office 365 Outlook][office365-outlookdoc]<br/>Office 365 使用者<br/>Office 365 影片<br/>OneDrive<br/>OneDrive for Business<br/>OneNote (Business)<br/>[Oracle Database][oracle-db-doc]<br/>Outlook Customer Manager<br/>Outlook 工作<br/>Outlook.com<br/><br/><a name="p"></a>PagerDuty<br/>Parserr<br/>Paylocity<br/>Pinterest<br/>Pipedrive<br/>Pivotal Tracker<br/>Planner<br/>PostgreSQL<br/>Power BI<br/>Project Online<br/><br/><a name="r"></a>Redmine<br/>[要求 / 回應][http-requestdoc]<br/>RSS<br/><br/><a name="s"></a>[Salesforce][salesforcedoc]<br/>[SAP 應用程式伺服器][sapconnector]<br/>[SAP 訊息伺服器][sapconnector]<br/>[排程][recurrencedoc]<br/>Scope<br/>SendGrid<br/>將訊息傳送至 Batch<br/>[服務匯流排][service-busdoc]<br/>SFTP<br/>[SharePoint Online][sharepointdoc]<br/>[SharePoint Server][sharepointserver]<br/>Slack<br/>Smartsheet<br/>SMTP<br/>SparkPost<br/>[SQL Server][sql-serverdoc]<br/>等量磁碟區<br/>SurveyMonkey<br/>切換大小寫<br/><br/><a name="t"></a>Teamwork 專案<br/>Teradata<br/>Todoist<br/>Toodledo<br/>[轉換 XML][xmltransformdoc]<br/>Trello<br/>Twilio<br/>[Twitter][twitterdoc]<br/>Typeform<br/><br/><a name="u"></a>UserVoice<br/><br/><a name="v"></a>變數<br/>Vimeo<br/>Visual Studio Team Services<br/><br/><a name="w"></a>WebMerge<br/>WordPress<br/>Wunderlist<br/><br/><a name="x"></a>[X12][x12doc]<br/>[XML 驗證][xmlvalidatedoc]<br/><br/><a name="y"></a>Yammer<br/>YouTube<br/><br/><a name="z"></a>Zendesk |

> [!TIP]
> 若要在註冊 Azure 帳戶之前先開始使用 Azure Logic Apps，請移至[試用 Logic Apps](https://tryappservice.azure.com/?appservice=logic)。 您可以立即建立短期的入門邏輯應用程式。 不需要信用卡；沒有承諾。

## <a name="connectors-as-triggers-and-actions"></a>連接器做為觸發程序和動作

**觸發程序**可啟動或執行邏輯應用程式的執行個體。 有些連接器會提供觸發程序，以在發生特定事件時通知您的應用程式。 例如，FTP 連接器具有 `OnUpdatedFile` 觸發程序，可在檔案更新時通知邏輯應用程式。 

Logic Apps 包含下列幾種觸發程序：  

* 輪詢觸發程序：這些觸發程序會以指定的頻率輪詢您的服務，以檢查是否有新資料。 

    有新資料可用時，邏輯應用程式的新執行個體會以該資料做為輸入而執行。 

* 推送觸發程序：這些觸發程序會接聽端點上的資料或發生的事件，然後觸發邏輯應用程式的新執行個體。

* 週期性觸發程序︰此觸發程序會依照指定的排程具現化邏輯應用程式的執行個體。

連接器也會提供您可使用於工作流程中的**動作**。 比方說，邏輯應用程式可以查閱資料，稍後在邏輯應用程式中使用此資料。 更特別的是，您可以從 SQL Database 查閱客戶資料，然後使用此客戶資料來建立工作流程。 

> [!TIP]
> [連接器概觀](connectors-overview.md)提供有關觸發程序和動作的詳細資訊。 


## <a name="message-manipulation-actions"></a>訊息操作動作

邏輯應用程式包含內建動作，可變更或操作承載資料。 內建的 **Data Operations** 連接器包含下列動作： 

| | |
|---|---|
| **撰寫** | 建置或產生值或物件以便稍後使用，或在建置工作流程時使用。 例如，您可以使用來自多個步驟的值來撰寫 JSON 物件，或計算稍後要在邏輯應用程式執行中參考的常數。 |
| **建立 CSV 資料表**<br/>**建立 HTML 資料表** | 將陣列結果集變成 CSV 或 HTML 資料表。 例如，新增 CRM「列出記錄」動作，以及為今天新增的記錄新增篩選條件。 然後，透過電子郵件以 HTML 資料表形式傳送結果。 |
| **篩選陣列** (查詢) | 將結果集篩選成您感興趣的項目。 例如，使用 `#Azure` 搜尋所有推文，然後「篩選」傳回的推文，只傳回 `Tweeted_by_followers > 50` 的結果。 |
| **Join** | 由某個分隔符號聯結陣列。 例如，「偵測關鍵片語」作業會傳回關鍵片語的陣列。 您可以用 `,` 或類似符號加以「聯結」。 因此，您會取得 `"Some, Phrase"`，而不是 `["Some", "Phrase"]`。 |
| **剖析 JSON** | 剖析及存取設計工具中 JSON 物件的值。 例如，如果您的 Azure Function 傳回 JSON 承載，您可加以剖析，以便稍後在其他步驟中存取 JSON 屬性。 此動作也會驗證 JSON 是否符合在執行階段指定的結構描述。 | 
| **選取** | 選取陣列的某些屬性，以便進一步處理。 如果您從 SQL 執行「列出記錄」，而它傳回 15 個資料行，則只要選取幾個資料行以便進一步處理。 輸出是一個僅包含所選屬性的陣列。 |

## <a name="custom-connectors-and-azure-certification"></a>自訂連接器和 Azure 憑證 

若要呼叫執行自訂程式碼或不可做為連接器的 API，您可以建立以 REST 為基礎的 API Apps 做為自訂連接器，以[擴充 Logic Apps 平台](../logic-apps/logic-apps-create-api-app.md)。 

如果您想要讓您自訂的 API Apps 公開且能夠在 Azure 中使用，請將您的提名提交至 [Microsoft Azure 認證程式](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/)。

## <a name="get-help"></a>取得說明

若要提出問題、回答問題以及查看其他 Azure Logic Apps 使用者的做法，請移至 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

若要改善 Azure Logic Apps 和連接器，請在 [Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)上票選或提交想法。

我們是否遺漏連接器主題，或任何您認為重要的詳細資訊？ 若是如此，請將其新增到我們現有的主題，或撰寫自己的主題來協助我們。 我們的文件是開放原始碼並存放於 GitHub。 從我們的 [GitHub 存放庫](https://github.com/Microsoft/azure-docs)著手。 

## <a name="next-steps"></a>後續步驟
* [建立第一個邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)
* [建立邏輯應用程式的自訂 API](../logic-apps/logic-apps-create-api-app.md)
* [監視邏輯應用程式](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "整合邏輯應用程式與 App Service API Apps"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "使用 Azure Blob 儲存體連接器管理 Blob 容器中的檔案"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "整合邏輯應用程式與 Azure Functions"
[db2doc]: ./connectors-create-api-db2.md "連線至雲端或內部部署中的 IBM DB2。更新資料列、取得資料表等等"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "連線至 Dynamics CRM Online，以便使用 CRM Online 資料"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "連線到 Azure 事件中樞。在邏輯應用程式與事件中樞之間接收和傳送事件"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "連線至內部部署檔案系統"
[ftpdoc]: ./connectors-create-api-ftp.md "連線至 FTP / FTPS 伺服器以便執行 FTP 工作，像是上傳、取得、刪除檔案等等"
[httpdoc]: ./connectors-native-http.md "使用 HTTP 連接器進行 HTTP 呼叫"
[http-requestdoc]: ./connectors-native-reqres.md "將 HTTP 要求和回應的動作新增至邏輯應用程式"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "使用 HTTP + Swagger 連接器進行 HTTP 呼叫"
[informixdoc]: ./connectors-create-api-informix.md "連線至雲端或內部部署中的 Informix。讀取資料列、列出資料表等等"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "整合邏輯應用程式與巢狀工作流程"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "連線至 Office 365 帳戶。傳送和接收電子郵件、管理行事曆和連絡人等等"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "連線至 Oracle 資料庫來新增、插入、刪除資料列等等"
[mqdoc]: ./connectors-create-api-mq.md "連線到 MQ 內部部署或 Azure，以及傳送及接收訊息"
[recurrencedoc]:  ./connectors-native-recurrence.md "觸發邏輯應用程式的重複執行動作"
[salesforcedoc]: ./connectors-create-api-salesforce.md "連線至 Salesforce 帳戶。管理帳戶、潛在客戶、機會等等"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "連線至內部部署 SAP 系統"
[service-busdoc]: ./connectors-create-api-servicebus.md "從「服務匯流排佇列和主題」傳送訊息，並接收來自「服務匯流排佇列和訂用帳戶」的訊息"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "連線至 SharePoint Online。管理文件、列出項目等等"
[sharepointserver]: ./connectors-create-api-sharepointserver.md "連線至內部部署伺服器上的 SharePoint。管理文件、列出項目等等"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "連線至 Azure SQL Database 或 SQL Server。建立、更新、取得和刪除 SQL Database 資料表中的項目。"
[twitterdoc]: ./connectors-create-api-twitter.md "連線至 Twitter。取得時間軸、張貼推文等等"
[webhookdoc]: ./connectors-native-webhook.md "將 Webhook 動作和觸發程序新增至邏輯應用程式"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "深入了解企業整合 AS2。"
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "深入了解企業整合 X12"
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "深入了解企業整合一般檔案。"
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "深入了解企業整合一般檔案。"
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "深入了解企業整合 XML 驗證。"
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "深入了解企業整合轉換。"
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "深入了解企業整合 AS2 解碼"
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "深入了解企業整合 AS2 編碼"
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "深入了解企業整合 X12 解碼"
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "深入了解企業整合 X12 編碼"
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "深入了解企業整合 EDIFACT 解碼"
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "深入了解企業整合 EDIFACT 編碼"
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "查閱整合帳戶中的結構描述、對應、合作夥伴和其他資訊"


[boxDoc]: ./connectors-create-api-box.md "連線至 Box。上傳、取得、刪除、列出您的檔案等等"
[delaydoc]: ./connectors-native-delay.md "執行延遲的動作"
[dropboxdoc]: ./connectors-create-api-dropbox.md "連線至 Dropbox。上傳、取得、刪除、列出您的檔案等等"
[facebookdoc]: ./connectors-create-api-facebook.md "連線至 Facebook。張貼在動態時報上、取得頁面摘要等等"
[githubdoc]: ./connectors-create-api-github.md "連線至 GitHub 並追蹤問題"
[google-drivedoc]: ./connectors-create-api-googledrive.md "連接至 GoogleDrive 以便使用資料"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "連線至 Google 試算表以便修改工作表"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "連線至 Google Tasks 以便管理工作"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "連接到 Google Calendar 並可管理行事曆。"
[http-responsedoc]: ./connectors-native-reqres.md "將 HTTP 要求和回應的動作新增至邏輯應用程式"
[instagramdoc]: ./connectors-create-api-instagram.md "連線至 Instagram。觸發事件或對其採取行動"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "連線至您的 MailChimp 帳戶。管理及自動處理郵件"
[mandrilldoc]: ./connectors-create-api-mandrill.md "連線至 Mandrill 進行通訊"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "連線至 Microsoft Translator。翻譯文字、偵測語言等等" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "取得影片資訊、影片清單和頻道，以及 Office 365 影片的播放 URL"
[onedrivedoc]: ./connectors-create-api-onedrive.md "連線至您的個人 Microsoft OneDrive。上傳、刪除、列出檔案等等"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "連線至公司 Microsoft OneDrive。上傳、刪除、列出您的檔案等等"
[outlook.comdoc]: ./connectors-create-api-outlook.md "連線至 Outlook 信箱。管理電子郵件、行事曆、連絡人等等"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "連線至 Microsoft Project Online。管理專案、工作、資源等等"
[querydoc]: ./connectors-native-query.md "透過查詢動作選取和篩選陣列"
[rssdoc]: ./connectors-create-api-rss.md "發佈和擷取摘要項目，在新項目發佈到 RSS 時觸發作業。"
[sendgriddoc]: ./connectors-create-api-sendgrid.md "連線至 SendGrid。傳送電子郵件及管理收件者清單"
[sftpdoc]: ./connectors-create-api-sftp.md "連線至 SFTP 帳戶。上傳、取得、刪除檔案等等"
[slackdoc]: ./connectors-create-api-slack.md "連線至 Slack，並將訊息張貼至 Slack 通道"
[smtpdoc]: ./connectors-create-api-smtp.md "連線至 SMTP 伺服器，以及傳送帶有附件的電子郵件"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "連線至 SparkPost 進行通訊"
[trellodoc]: ./connectors-create-api-trello.md "連線至 Trello。管理您的專案並組織任何項目與任何人"
[twiliodoc]: ./connectors-create-api-twilio.md "連線至 Twilio。傳送和取得訊息、取得可用的號碼、管理撥入的電話號碼等等"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "連線至 Wunderlist。管理您的工作和待辦事項清單、讓您的生活保持同步等等"
[yammerdoc]: ./connectors-create-api-yammer.md "連線至 Yammer。張貼訊息、取得新訊息等等"
[youtubedoc]: ./connectors-create-api-youtube.md "連線至 YouTube。管理您的影片和頻道"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/api.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[Delayicon]: ./media/apis-list/delay.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png
