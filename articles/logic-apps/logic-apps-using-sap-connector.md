---
title: "連線到 Azure Logic Apps 中的內部部署 SAP 系統 | Microsoft Docs"
description: "透過內部部署資料閘道，連線到邏輯應用程式工作流程中的內部部署 SAP 系統"
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3fea93f558d5a4ef62550fd1f6486903cb812930
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>使用 SAP 連接器，從邏輯應用程式連線到內部部署 SAP 系統 

內部部署資料閘道可讓您管理資料，並且安全地存取內部部署的資源。 本主題示範如何將邏輯應用程式連線到內部部署 SAP 系統。 在此範例中，邏輯應用程式會透過 HTTP 要求 IDOC，並傳回回應。    

> [!NOTE]
> 目前限制： 
> - 如果回應所需的所有步驟未在[要求逾時限制](./logic-apps-limits-and-config.md)內完成，則邏輯應用程式會逾時。 在此情況下，可能會封鎖要求。 
> - 檔案選擇器不會顯示所有可用的欄位。 在此案例中，您可以手動新增路徑。

## <a name="prerequisites"></a>必要條件

- 安裝及設定最新的[內部部署資料閘道](https://www.microsoft.com/download/details.aspx?id=53127)版本 1.15.6150.1 或更新版本。 [如何連接至邏輯應用程式中的內部部署資料閘道](http://aka.ms/logicapps-gateway)中有列出步驟。 必須先在內部部署機器上安裝閘道，然後才能繼續。

- 在您已安裝資料閘道的同一部電腦上，下載並安裝最新的 SAP 用戶端程式庫。 使用下列任一 SAP 版本： 
    - SAP 伺服器
        - 任何支援 .NET 連接器 (NCo) 3.0 的 SAP 伺服器
 
    - SAP 用戶端
        - SAP.NET 連接器 (NCo) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>新增用來連線到 SAP 系統的觸發程序和動作

SAP 連接器具有動作，但沒有觸發程序。 因此，我們需要在工作流程的開頭使用另一個觸發程序。 

1. 新增要求/回應觸發程序，然後選取 [新增步驟]。

2. 選取 [新增動作]，然後在搜尋欄位輸入 `SAP` 以選取 SAP 連接器：    

     ![選取 SAP 應用程式伺服器或 SAP 訊息伺服器](media/logic-apps-using-sap-connector/sap-action.png)

3. 選取 [**SAP 應用程式伺服器**](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)或 [**SAP 訊息伺服器**](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)，這取決於您的 SAP 設定。 如果您目前沒有連線，系統會提示您建立連線。

   1. 選取 [透過內部部署資料閘道連接]，並輸入 SAP 系統的詳細資料︰   

       ![將連接字串新增到 SAP](media/logic-apps-using-sap-connector/picture2.png)  

   2. 在 [閘道] 下方，選取現有閘道，或者，若要安裝新的閘道，請選取 [安裝閘道]。

        ![安裝新的閘道](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. 輸入所有詳細資料之後，選取 [建立]。 
   Logic Apps 會設定並測試連線，以確定連線運作正常。

4. 輸入 SAP 連線的名稱。

5. 不同的 SAP 選項現已推出。 若要尋找 IDOC 類別，請從清單中進行選取。 或者，以手動方式輸入路徑，並在 **body** 欄位中選取 HTTP 回應：

     ![SAP 動作](media/logic-apps-using-sap-connector/picture3.png)

6. 新增用於建立 [HTTP 回應] 的動作。 回應訊息應該都來自 SAP 輸出。

7. 儲存您的邏輯應用程式。 透過 HTTP 觸發程序 URL 傳送 IDOC 加以測試。 傳送 IDOC 之後，請等待邏輯應用程式的回應：   

     > [!TIP]
     > 請查看如何[監視 Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

現在，SAP 連接器新增至邏輯應用程式，開始探索其他功能︰

- BAPI
- RFC

## <a name="get-help"></a>取得說明

若要提出問題、回答問題以及了解其他 Azure Logic Apps 使用者的做法，請造訪 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

若要改善 Azure Logic Apps 和連接器，請在 [Azure Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)上票選或提交想法。

## <a name="next-steps"></a>後續步驟

- 了解如何驗證、轉換及[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)中其他與 BizTalk 類似的功能。 
- 從邏輯應用程式[連線到內部部署資料](../logic-apps/logic-apps-gateway-connection.md)
