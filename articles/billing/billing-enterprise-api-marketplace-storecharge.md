---
title: "Azure 計費企業版 API - Marketplace 費用| Microsoft Docs"
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
ms.openlocfilehash: 5539623f7ae35e14b6dafe6fdf9efe4bcaba4fd3
ms.contentlocale: zh-tw
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---marketplace-store-charge"></a>適用於企業客戶的報告 API - Marketplace 市集費用

Marketplace 市集費用 API 可針對指定的計費週期或開始和結束日期，傳回以使用量為基礎的 Marketplace 費用每日明細 (不含一次性費用)。

##<a name="request"></a>要求 
需要新增的常見標頭屬性在[這裡](billing-enterprise-api.md)詳述。 如果未指定計費週期，則會傳回目前計費週期的資料。 可以使用格式為 yyyy-MM-dd 的開始和結束日期參數來指定自訂的時間範圍，支援的最大時間範圍是 36 個月。  

|方法 | 要求 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> 若要使用 API 的預覽版本，請將上述 URL 中的 v2 取代為 v1。
>

## <a name="response"></a>Response
 
    
        [
            {
                "id": "id",
                "subscriptionGuid": "00000000-0000-0000-0000-000000000000",
                "subscriptionName": "subName",
                "meterId": "2core",
                "usageStartDate": "2015-09-17T00:00:00Z",
                "usageEndDate": "2015-09-17T23:59:59Z",
                "offerName": "Virtual LoadMaster™ (VLM) for Azure",
                "resourceGroup": "Res group",
                "instanceId": "id",
                "additionalInfo": "{\"ImageType\":null,\"ServiceType\":\"Medium\"}",
                "tags": "",
                "orderNumber": "order",
                "unitOfMeasure": "",
                "costCenter": "100",
                "accountId": 100,
                "accountName": "Account Name",
                "accountOwnerId": "account@live.com",
                "departmentId": 101,
                "departmentName": "Department 1",
                "publisherName": "Publisher 1",
                "planName": "Plan name",
                "consumedQuantity": 1.15,
                "resourceRate": 0.1,
                "extendedCost": 1.11
            },
            ...
        ]
    

**回應屬性定義**

|屬性名稱| 類型| 說明
|-|-|-|
|id|string|Marketplace 費用項目的唯一識別碼|
|subscriptionGuid|Guid|訂用帳戶 GUID|
|subscriptionName|字串|訂用帳戶名稱|
|meterId|string|所發出計量的識別碼|
|usageStartDate|DateTime|使用量記錄的開始使間|
|usageEndDate|DateTime|使用量記錄的結束使間|
|offerName|字串|優惠名稱|
|resourceGroup|字串|資源群組|
|instanceId|字串|執行個體識別碼|
|additionalInfo|字串|其他資訊 JSON 字串|
|tags|string|標籤 JSON 字串|
|orderNumber|字串|訂單編號|
|unitOfMeasure|字串|計量的度量單位|
|costCenter|字串|成本中心|
|accountId|int|帳戶識別碼|
|accountName|string |帳戶名稱|
|accountOwnerId|string|帳戶擁有者識別碼|
|departmentId|int|部門識別碼|
|departmentName|字串|部門名稱|
|publisherName|字串|發行者名稱|
|planName|字串|方案名稱|
|consumedQuantity|decimal|此時間週期內的已耗用數量|
|resourceRate|decimal|計量的單價|
|extendedCost|decimal|根據已耗用數量和延伸成本的預估費用|
<br/>
## <a name="see-also"></a>另請參閱

* [計費週期 API](billing-enterprise-api-billing-periods.md)

* [使用量詳細資料 API](billing-enterprise-api-usage-detail.md) 

* [餘額與摘要 API](billing-enterprise-api-balance-summary.md)

* [價位表 API](billing-enterprise-api-pricesheet.md)
