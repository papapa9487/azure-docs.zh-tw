---
title: "Azure 計費企業版 API - PriceSheet| Microsoft Docs"
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
ms.openlocfilehash: 2e7d6e883abe4cee13bc5f684baf2a1ea9c6c397
ms.contentlocale: zh-tw
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---price-sheet"></a>適用於企業客戶的報告 API - PriceSheet

價位表 API 可針對指定註冊和計費週期的每個計量提供適用的費率。

##<a name="request"></a>要求
需要新增的常見標頭屬性在[這裡](billing-enterprise-api.md)詳述。 如果未指定計費週期，則會傳回目前計費週期的資料。

|方法 | 要求 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|

> [!Note]
> 若要使用 API 的預覽版本，請將上述 URL 中的 v2 取代為 v1。
>

## <a name="response"></a>Response

    
        [
            {
                "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                "billingPeriodId": "201704",
                "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
                "meterName": "A1 VM",
                "unitOfMeasure": "100 Hours",
                "includedQuantity": 0,
                "partNumber": "N7H-00015",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            {
                "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                "billingPeriodId": "201404",
                "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
                "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                "unitOfMeasure": "100 GB",
                "includedQuantity": 0,
                "partNumber": "N9H-00402",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            ...
        ]
    

> [!Note]
>如果您使用的是預覽版 API，就無法使用 meterId 欄位。
>

**回應屬性定義**

|屬性名稱| 類型| 說明
|-|-|-|
|id| string| 表示特定 PriceSheet 項目的唯一識別碼 (依計費週期的計量)|
|billingPeriodId| string| 表示特定計費週期的唯一識別碼|
|meterId| 字串| 計量的識別碼。 它可以對應至使用量 meterId。|
|meterName| 字串| 計量名稱|
|unitOfMeasure| 字串| 測量服務的度量單位|
|includedQuantity| decimal| 包含的數量 |
|partNumber| string| 與計量相關聯的組件編號|
|unitPrice| decimal| 計量的單價|
|currencyCode| 字串| unitPrice 的貨幣代碼|
<br/>
## <a name="see-also"></a>另請參閱

* [計費週期 API](billing-enterprise-api-billing-periods.md)

* [使用量詳細資料 API](billing-enterprise-api-usage-detail.md)

* [餘額與摘要 API](billing-enterprise-api-balance-summary.md)

* [Marketplace 市集費用 API](billing-enterprise-api-marketplace-storecharge.md)

