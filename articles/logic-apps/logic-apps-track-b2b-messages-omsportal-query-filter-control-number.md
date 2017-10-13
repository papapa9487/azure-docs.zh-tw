---
title: "在 Operations Management Suite 中查詢 B2B 訊息 - Azure Logic Apps  | Microsoft Docs"
description: "建立查詢以在 Operations Management Suite 中追蹤 AS2、X12 和 EDIFACT 訊息"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 2748d3d3daf7c13dca05f663a4a088598e1b3605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>在 Microsoft Operations Management Suite (OMS) 中查詢 AS2、X12 和 EDIFACT 訊息

若要尋找您在 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中使用 [Azure Log Analytics](../log-analytics/log-analytics-overview.md) 追蹤的 AS2、X12 或 EDIFACT 訊息，則可以建立查詢，以根據特定準則來篩選動作。 例如，您可以找到根據特定交換控制編號的訊息。

## <a name="requirements"></a>需求

* 已設定診斷記錄的邏輯應用程式。 了解[如何建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)和[如何設定該邏輯應用程式的記錄](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

* 已設定監視和記錄的整合帳戶。 了解[如何建立整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)和[如何設定該帳戶的監視和記錄](../logic-apps/logic-apps-monitor-b2b-message.md)。

* 如果您還沒有這麼做，請[在 OMS 中將診斷資料發佈至 Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)，並[在 OMS 中設定訊息追蹤](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

> [!NOTE]
> 在您符合先前的需求之後，[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中應該有工作區。 您應該在 OMS 中使用相同的 OMS 工作區來追蹤 B2B 通訊。 
>  
> 如果您沒有 OMS 工作區，請了解[如何建立 OMS 工作區](../log-analytics/log-analytics-get-started.md)。

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>在 Operations Management Suite 入口網站中建立具有篩選的訊息查詢

此範例示範如何找到根據其交換控制編號的訊息。

> [!TIP] 
> 如果您知道 OMS 工作區名稱，請移至工作區首頁 (`https://{your-workspace-name}.portal.mms.microsoft.com`)，然後在步驟 4 啟動。 否則，請從步驟 1 開始。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選擇 [更多服務]。 搜尋 "log analytics"，然後選擇 [Log Analytics]，如下所示：

   ![尋找 Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. 在 [Log Analytics] 下，尋找並選取 OMS 工作區。

   ![選取 OMS 工作區](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. 在 [管理] 下，選擇 [OMS 入口網站]。

   ![選擇 OMS 入口網站](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. 在 OMS 首頁上，選擇 [記錄搜尋]。

   ![在 OMS 首頁上，選擇 [記錄搜尋]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -或-

   ![在 OMS 功能表上，選擇 [記錄搜尋]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. 在搜尋方塊中，輸入您想要尋找的欄位，然後按 **Enter**。 當您開始鍵入時，OMS 會顯示您可以使用的可能相符項目和作業。 深入了解[如何在 Log Analytics 中尋找資料](../log-analytics/log-analytics-log-searches.md)。

   此範例會搜尋具有 **Type=AzureDiagnostics** 的事件。

   ![開始鍵入查詢字串](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. 在左列中，選擇您想要檢視的時間範圍。 若要將篩選新增至查詢，請選擇 [+新增]。

   ![將篩選新增至查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. 在 [新增篩選] 下，輸入篩選名稱，以找到您想要的篩選。 選取篩選，然後選擇 [+新增]。

   為了尋找交換控制編號，此範例會搜尋 "interchange" 這個字，並選取 **event_record_messageProperties_interchangeControlNumber_s** 作為篩選。

   ![選取篩選](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. 在左列中，選取您想要使用的篩選值，然後選擇 [套用]。

   此範例會選取我們想要之訊息的交換控制編號。

   ![選取篩選值](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. 現在，回到您要建置的查詢。 已使用您所選取的篩選事件和值來更新您的查詢。 現在也會篩選您的先前結果。

    ![回到具有已篩選結果的查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>儲存查詢供日後使用

1. 從 [記錄搜尋] 頁面上的查詢，選擇 [儲存]。 提供查詢名稱，並選取分類，然後選擇 [儲存]。

   ![提供查詢名稱和分類](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. 若要檢視您的查詢，請選擇 [我的最愛]。

   ![選擇 [我的最愛]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. 在 [儲存的搜尋] 下，選取您的查詢，以檢視結果。 若要更新查詢，以找到不同的結果，請編輯查詢。

   ![選取查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>在 Operations Management Suite 入口網站中尋找並執行預存查詢

1. 開啟 OMS 工作區首頁 (`https://{your-workspace-name}.portal.mms.microsoft.com`)，然後選擇 [記錄搜尋]。

   ![在 OMS 首頁上，選擇 [記錄搜尋]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -或-

   ![在 OMS 功能表上，選擇 [記錄搜尋]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. 在 [記錄搜尋] 首頁上，選擇 [我的最愛]。

   ![選擇 [我的最愛]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. 在 [儲存的搜尋] 下，選取您的查詢，以檢視結果。 若要更新查詢，以找到不同的結果，請編輯查詢。

   ![選取查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>後續步驟

* [AS2 追蹤結構描述](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 追蹤結構描述](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自訂追蹤結構描述](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)