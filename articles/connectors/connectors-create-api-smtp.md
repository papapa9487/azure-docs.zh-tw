---
title: "Azure Logic Apps 中的 SMTP 連接器 | Microsoft Docs"
description: "使用 Azure App Service 建立邏輯應用程式。 連接到 SMTP 以傳送電子郵件。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 1cf96bbf8bd215d7ddb3c99860a5cb4e668be3c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-smtp-connector"></a>開始使用 SMTP 連接器
連接到 SMTP 以傳送電子郵件。

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)來開始。

## <a name="connect-to-smtp"></a>連接到 SMTP
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 [連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。 例如，若要連接到 SMTP，您必須先有 SMTP「連線」。 若要建立連線，請輸入平常用來存取連線服務的認證。 因此，在 SMTP 範例中，請輸入連接名稱、SMTP 伺服器位址，以及使用者登入資訊等認證來建立 SMTP 連線。  

### <a name="create-a-connection-to-smtp"></a>建立至 SMTP 的連線
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>使用 SMTP 觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

在此範例中，由於 SMTP 沒有自己的觸發程序，因此我們將使用 **Salesforce - 當物件建立時**觸發程序。 當 Salesforce 中有新的物件建立時，觸發程序就會啟動。 在範例中，我們會設定使其每次在 Salesforce 中建立新的潛在客戶時，系統會透過 SMTP 連接器執行*傳送電子郵件*的動作，並附帶已建立新潛在客戶的通知。

1. 在邏輯應用程式設計工具的搜尋方塊中輸入 *salesforce*，然後選取 [Salesforce - 當建立物件時] 觸發程序。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. [當建立物件時]  控制項隨即顯示。
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. 選取 [物件類型] 然後從清單的物件中選取 [潛在客戶]。 在此步驟中，您會指出您要建立一個觸發程序，此觸發程序將在每次 Salesforce 中有建立新潛在客戶的情況時，通知您的邏輯應用程式。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. 觸發程序已建立。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>使用 SMTP 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

現在已新增觸發程序之後，請遵循下列步驟新增 Salesforce 中有新的潛在客戶建立時會發生的 SMTP 動作。

1. 選取 [+ 新的步驟]，來新增您想要在建立新的潛在客戶時採取的動作。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. 選取 [新增動作] 。 這會開啟搜尋方塊，您可以在其中搜尋任何想要採取的動作。  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. 輸入 *smtp* 以搜尋與 SMTP 相關的動作。  
4. 選取 [SMTP - 傳送電子郵件]，做為建立新的潛在客戶時要採取的動作。 動作控制區塊便會開啟。 如果您先前未曾在設計工具區塊中建立 SMTP 連線，您必須這麼做。  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. 在 [SMTP - 傳送電子郵件] 區塊中輸入所需的電子郵件資訊。  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. 儲存您的工作以啟動工作流程。  

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/smtpconnector/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。