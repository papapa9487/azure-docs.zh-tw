---
title: "在您的 Logic Apps 中新增 Azure Blob 儲存體連接器 | Microsoft Docs"
description: "在邏輯應用程式中開始使用及設定 Azure Blob 儲存體連接器"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: mandia; ladocs
ms.openlocfilehash: bc7908868828bd1628633cf9e57f8c44f8000827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>在邏輯應用程式中使用 Azure Blob 儲存體連接器
您可以使用 Azure Blob 儲存體連接器來上傳、更新、取得及刪除您儲存體帳戶中的 Blob，所有這些操作都在一個邏輯應用程式內完成。  

您可以利用 Azure Blob 儲存體來：

* 上傳新專案或取得最近更新的檔案以建置工作流程。
* 使用動作來取得檔案中繼資料、刪除檔案、複製檔案和進行其他作業。 例如，當 Azure 網站中的工具更新時 (觸發程序)，便更新 Blob 儲存體中的檔案 (動作)。 

本主題說明如何在邏輯應用程式中使用 Blob 儲存體連接器。

若要深入瞭解 Logic Apps，請參閱[什麼是邏輯應用程式](../logic-apps/logic-apps-what-are-logic-apps.md)以及[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

若要深入瞭解 Logic Apps，請參閱[什麼是邏輯應用程式](../logic-apps/logic-apps-what-are-logic-apps.md)以及[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="connect-to-azure-blob-storage"></a>連線至 Azure Blob 儲存體
您必須先建立與服務的「連線」，才能透過邏輯應用程式存取任何服務。 連線可讓邏輯應用程式與另一個服務連線。 例如，若要連線至儲存體帳戶，您得先建立 Blob 儲存體連線。 若要建立連線，請輸入平常用來存取所連線服務的認證。 因此，請在 Azure 儲存體中，輸入儲存體帳戶的認證以建立連線。 

#### <a name="create-the-connection"></a>建立連線
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>使用觸發程序
此連接器並沒有任何觸發程序。 請使用其他觸發程序來啟動邏輯應用程式，例如循環觸發程序、HTTP Webhook 觸發程序、其他連接器適用的觸發程序等等。 [建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md) 可提供範例。

## <a name="use-an-action"></a>使用動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。

1. 選取加號。 您會看到幾個選擇︰[新增動作]、[新增條件] 或其中一個 [其他] 選項。
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. 選擇 [新增動作] 。
3. 在文字方塊中，輸入「blob」以取得所有可用動作的清單。
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. 在我們的範例中，選擇 [AzureBlob - 使用路徑取得檔案中繼資料]。 如果連線已存在，請選取 [...](顯示選擇器) 按鈕來選取檔案。
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    如果系統提示您輸入連線資訊，請輸入詳細資料以建立連線。 本主題的[建立連線](connectors-create-api-azureblobstorage.md#create-the-connection)一節會說明這些屬性。 
   
   > [!NOTE]
   > 在此範例中，我們會取得檔案的中繼資料。 若要查看中繼資料，請新增另一個動作，以使用另一個連接器建立新檔案。 例如，新增 OneDrive 動作，以根據中繼資料建立新的「測試」檔案。 


5. **儲存**您的變更 (工具列的左上角)。 邏輯應用程式將會儲存，而且可能會自動啟用。

> [!TIP]
> [儲存體總管](http://storageexplorer.com/)是很適合用來管理多個儲存體帳戶的工具。

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/azureblobconnector/)的所有限制。 

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。 請到我們的 [API 清單](apis-list.md)探索 Logic Apps 中其他可用的連接器。

