---
title: "在您的 Logic Apps 中新增 Azure SQL Database 連接器 | Microsoft Docs"
description: "搭配 REST API 參數來使用 Azure SQL Database 連接器的概觀"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: a3d5cb909dbfcb00f3fbfa0165bb6cd58eb18688
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>開始使用 Azure SQL Database 連接器
使用 Azure SQL Database 連接器，為組織建立工作流程來管理資料表中的資料。 

利用 SQL Database，您可以：

* 藉由在客戶資料庫中新增新客戶或在訂單資料庫中更新訂單，以建置工作流程。
* 使用動作來取得一列資料、插入新的資料列，甚至加以刪除。 例如，當有記錄在 Dynamics CRM Online 中建立時 (觸發程序)，則在 Azure SQL Database 插入資料列 (動作)。 

本主題說明如何在邏輯應用程式中使用 SQL Database 連接器，並且也列出動作。

若要深入瞭解 Logic Apps，請參閱[什麼是邏輯應用程式](../logic-apps/logic-apps-what-are-logic-apps.md)以及[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="connect-to-azure-sql-database"></a>連線到 Azure SQL Database
您必須先建立與服務的「連線」，才能透過邏輯應用程式存取任何服務。 連線可讓邏輯應用程式與另一個服務連線。 例如，若要連線到 SQL Database，您必須先建立 SQL Database「連線」。 若要建立連線，您需要輸入平常用來存取所連線服務的認證。 因此，在 SQL Database 中，請輸入 SQL Database 認證來建立連線。 

#### <a name="create-the-connection"></a>建立連線
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>使用觸發程序
此連接器並沒有任何觸發程序。 請使用其他觸發程序來啟動邏輯應用程式，例如循環觸發程序、HTTP Webhook 觸發程序、其他連接器適用的觸發程序等等。 [建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md) 可提供範例。

## <a name="use-an-action"></a>使用動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

1. 選取加號。 您會看到幾個選擇︰[新增動作]、[新增條件] 或其中一個 [其他] 選項。
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. 選擇 [新增動作] 。
3. 在文字方塊中，輸入「sql」以取得所有可用動作的清單。
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. 在我們的範例中，選擇 [SQL Server - 取得資料列]。 如果連線已存在，則選取下拉式清單中的 [資料表名稱]，然後輸入想要傳回的 [資料列識別碼]。
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    如果系統提示您輸入連線資訊，請輸入詳細資料以建立連線。 本主題的[建立連線](connectors-create-api-sqlazure.md#create-the-connection)一節會說明這些屬性。 
   
   > [!NOTE]
   > 在此範例中，我們會從資料表傳回資料列。 若要查看此資料列中的資料，請新增另一個動作，以使用資料表中的欄位建立檔案。 例如，新增 OneDrive 動作，使用 [FirstName] 和 [LastName] 欄位在雲端儲存體帳戶中建立新檔案。 
   > 
   > 
5. **儲存**您的變更 (工具列的左上角)。 邏輯應用程式將會儲存，而且可能會自動啟用。

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/sql/)的所有限制。 

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。 請到我們的 [API 清單](apis-list.md)探索 Logic Apps 中其他可用的連接器。

