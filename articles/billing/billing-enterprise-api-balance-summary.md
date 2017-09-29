---
title: "Azure 計費企業版 API - 餘額與摘要| Microsoft Docs"
description: "了解 Azure 計費使用情況和 RateCard API，其可用來提供 Azure 資源耗用量和趨勢的見解。"
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
ms.openlocfilehash: f6b149f0e656d2263705048aa5b644f5bb4a5712
ms.contentlocale: zh-tw
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary"></a>適用於企業客戶的報告 API - 餘額與摘要

餘額與摘要 API 可提供餘額、新購買、Azure Marketplace 服務費用、調整及超額部分費用的每月摘要資訊。


##<a name="request"></a>要求 
需要新增的常見標頭屬性在[這裡](billing-enterprise-api.md)詳述。 如果未指定計費週期，則會傳回目前計費週期的資料。

|方法 | 要求 URI|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|

> [!Note]
> 若要使用 API 的預覽版本，請將上述 URL 中的 v2 取代為 v1。
>

## <a name="response"></a>回應

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
            "billingPeriodId": 201507,
            "currencyCode": "USD",
            "beginningBalance": 0,
            "endingBalance": 1.1,
            "newPurchases": 1,
            "adjustments": 1.1,
            "utilized": 1.1,
            "serviceOverage": 1,
            "chargesBilledSeparately": 1,
            "totalOverage": 1,
            "totalUsage": 1.1,
            "azureMarketplaceServiceCharges": 1,
            "newPurchasesDetails": [
                {
                "name": "",
                "value": 1
                }
            ],
            "adjustmentDetails": [
                {
                "name": "Promo Credit",
                "value": 1.1
                },
                {
                "name": "SIE Credit",
                "value": 1.0
                }
            ]
        }


**回應屬性定義**

|屬性名稱| 類型| 說明
|-|-|-|
|id|字串|特定計費週期和註冊的唯一識別碼|
|billingPeriodId|string |計費週期識別碼|
|currencyCode|字串 |貨幣代碼|
|beginningBalance|decimal| 計費週期的開始餘額|
|endingBalance|decimal| 計費週期的結算餘額 (此值在開放期間內會每天更新)|
|newPurchases|decimal| 新購買總數|
|adjustments|decimal| 總調整量|
|utilized|decimal| 總承諾用量|
|serviceOverage|decimal| Azure 服務的超額部分|
|chargesBilledSeparately|decimal| 另外計費的費用|
|totalOverage|decimal| serviceOverage + chargesBilledSeparately|
|totalUsage|decimal| Azure 服務承諾用量 + 總超額部分|
|azureMarketplaceServiceCharges|decimal| Azure Marketplace 的總費用|
|newPurchasesDetails|名稱值組的 JSON 字串陣列|新購買的清單|
|adjustmentDetails|名稱值組的 JSON 字串陣列|調整的清單 (促銷信用額度、SIE 信用額度等) |


<br/>
## <a name="see-also"></a>另請參閱

* [計費週期 API](billing-enterprise-api-billing-periods.md)

* [使用量詳細資料 API](billing-enterprise-api-usage-detail.md) 

* [Marketplace 市集費用 API](billing-enterprise-api-marketplace-storecharge.md) 

* [價位表 API](billing-enterprise-api-pricesheet.md)
