---
title: "Azure 計費企業版 API - 計費週期| Microsoft Docs"
description: "了解可讓企業 Azure 客戶以程式設計方式提取使用情況資料的報告 API。"
services: 
documentationcenter: 
author: cwatson-cat
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: c6880b79189e0683387a7aafbd6fa4805b3b42ef
ms.contentlocale: zh-tw
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---billing-periods"></a>適用於企業客戶的報告 API - 計費週期

計費週期 API 會傳回計費週期清單，其中包含所指定註冊的使用情況資料 (以反向時間順序排列)。 每個週期包含指向四組資料 (BalanceSummary、UsageDetails、MarketplaceCharges 和 PriceSheet) 之 API 路由的屬性。 如果該週期沒有資料，則對應的屬性為 Null。 


##<a name="request"></a>要求 
需要新增的常見標頭屬性在[這裡](billing-enterprise-api.md)詳述。 

|方法 | 要求 URI|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingperiods|

> [!Note]
> 若要使用 API 的預覽版本，請將上述 URL 中的 v2 取代為 v1。
>

## <a name="response"></a>Response
 
    
    
      [
            {
                "billingPeriodId": "201704",
                "billingStart": "2017-04-01T00:00:00Z",
                "billingEnd": "2017-04-30T11:59:59Z",
                "balanceSummary": "/v1/enrollments/100/billingperiods/201704/balancesummary",
                "usageDetails": "/v1/enrollments/100/billingperiods/201704/usagedetails",
                "marketplaceCharges": "/v1/enrollments/100/billingperiods/201704/marketplacecharges",
                "priceSheet": "/v1/enrollments/100/billingperiods/201704/pricesheet"
            },          
            ....
      ]
    

**回應屬性定義**

|屬性名稱| 類型| 說明
|-|-|-|
|billingPeriodId| string| 表示特定計費週期的唯一識別碼|
|billingStart| datetime| 表示週期開始日期的 ISO 8601 字串|
|billingEnd| datetime| 表示週期結束日期的 ISO 8601 字串|
|balanceSummary| string| 路由到此週期之餘額摘要資料的 URL 路徑|
|usageDetails| 字串| 路由到此週期之使用量詳細資料的 URL 路徑|
|marketplaceCharges| string| 路由到此週期之 Marketplace 費用資料的 URL 路徑|
|priceSheet| 字串| 路由到此週期之 PriceSheet 資料的 URL 路徑|

<br/>
## <a name="see-also"></a>另請參閱

* [餘額與摘要 API](billing-enterprise-api-balance-summary.md)

* [使用量詳細資料 API](billing-enterprise-api-usage-detail.md) 

* [Marketplace 市集費用 API](billing-enterprise-api-marketplace-storecharge.md) 

* [價位表 API](billing-enterprise-api-pricesheet.md)
