---
title: "連線到內部部署檔案系統 - Azure Logic Apps | Microsoft Docs"
description: "透過內部部署資料閘道和檔案系統連接器，從邏輯應用程式工作流程連線到內部部署檔案系統"
keywords: "檔案系統, 內部部署"
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 7738b3346af49cb8aa811eb17003d1b72b1bbe46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>使用檔案系統連接器，從邏輯應用程式連線到內部部署檔案系統

若要管理資料並安全地存取內部部署資源，您的邏輯應用程式可以使用內部部署資料閘道。 本文說明如何透過以下基本範例案例來連線到內部部署檔案系統：將已上傳至 Dropbox 的檔案複製到檔案共用，然後傳送電子郵件。

## <a name="prerequisites"></a>必要條件

* 下載最新的[內部部署資料閘道](https://www.microsoft.com/download/details.aspx?id=53127)。

* 安裝並設定最新的內部部署資料閘道 (1.15.6150.1 版或更新版本)。 如需了解這些步驟，請參閱[連線到內部部署資料來源](http://aka.ms/logicapps-gateway)。 您必須先在內部部署電腦上安裝閘道，才能繼續執行這些步驟。

* [如何建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)的基本知識

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>新增用來連線到檔案系統的觸發程序和動作

1. 建立空白邏輯應用程式。 將此觸發程序新增為第一個步驟：**Dropbox - 建立檔案時**。 

2. 在觸發程序底下，選擇 [+ 下一步] > [新增動作]。 

3. 在搜尋方塊中，輸入「檔案系統」作為篩選條件。 看到「檔案系統」連接器的所有動作時，請選擇 [檔案系統 - 建立檔案] 動作。 

   ![搜尋檔案連接器](media/logic-apps-using-file-connector/search-file-connector.png)

4. 如果您尚未與檔案系統建立連線，系統就會提示您建立連線。 

5. 選取 [透過內部部署資料閘道連線]。 連線屬性出現時，請依照下表所指定的方式，設定您的連線。

   ![設定連線](media/logic-apps-using-file-connector/create-file.png)

   | 設定 | 說明 |
   | ------- | ----------- |
   | **根資料夾** | 指定您檔案系統的根資料夾。 您可以指定內部部署資料閘道安裝所在電腦的本機資料夾，或者資料夾可以是電腦可存取的網路共用。 <p>**秘訣：**根資料夾是主要的父資料夾，會作為所有檔案相關動作的相對路徑。 | 
   | **驗證類型** | 您檔案系統所使用的驗證類型 | 
   | **使用者名稱** | 為先前安裝的閘道提供您的使用者名稱 {*網域*\\*使用者名稱*}。 | 
   | **密碼** | 為先前安裝的閘道提供您的密碼。 | 
   | **閘道** | 選取先前安裝的閘道。 | 
   ||| 

6. 提供所有連線詳細資料之後，選擇 [建立]。 

   Logic Apps 會設定並測試連線，以確定連線運作正常。 
   如果已正確設定連線，就會針對您先前選取的動作顯示選項。 
   檔案系統連接器現在已可供使用。

7. 設定 [建立檔案] 動作，以將檔案從 Dropbox 複製到內部部署檔案共用的根資料夾。

   ![建立檔案動作](media/logic-apps-using-file-connector/create-file-filled.png)

8. 在這個複製檔案的動作之後，新增一個會傳送電子郵件的 Outlook 動作，以便讓相關使用者知道有新的檔案。 輸入電子郵件的收件者、標題和本文。 

   在**動態內容**清單中，您可以選擇來自檔案連接器的資料輸出，以便將更多詳細資料新增到電子郵件中。

   ![傳送電子郵件動作](media/logic-apps-using-file-connector/send-email.png)

9. 儲存您的邏輯應用程式。 將檔案上傳至 Dropbox，以測試應用程式。 檔案應該會複製到內部部署檔案共用，而且您應該會收到該作業的電子郵件。

恭喜，您現在已經有可運作的邏輯應用程式，可連線到內部部署檔案系統的。 

請嘗試探索連接器所提供的其他功能，例如︰

- 建立檔案
- 列出資料夾中的檔案
- 附加檔案
- 刪除檔案
- 取得檔案內容
- 使用路徑來取得檔案內容
- 取得檔案中繼資料
- 使用路徑取得檔案中繼資料
- 列出根資料夾中的檔案
- 更新檔案

## <a name="view-the-swagger"></a>檢視 Swagger

請參閱 [Swagger 詳細資料](/connectors/fileconnector/)。 

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 若要協助改善 Azure Logic Apps 和連接器，請在 [Azure Logic Apps User Voice 網站](http://aka.ms/logicapps-wish) \(英文\) 上票選或提交想法。

## <a name="next-steps"></a>後續步驟

* [連線至內部部署資料](../logic-apps/logic-apps-gateway-connection.md) 
* [監視邏輯應用程式](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [適用於 B2B 案例的企業整合](../logic-apps/logic-apps-enterprise-integration-overview.md)
