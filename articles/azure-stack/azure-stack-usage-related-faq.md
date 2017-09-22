---
title: "使用情況 API 相關常見問題集 | Microsoft Docs"
description: "Azure Stack 計量清單、與 Azure 使用情況 API 的比較、使用時間與回報時間、錯誤碼。"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: alfredop
ms.translationtype: HT
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 6dde832fe6b4679bd36575ca6aa3c274fed84f0c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="azure-stack-usage-api-faqs"></a>Azure Stack 使用情況 API 常見問題集
此文章提供 Azure Stack 使用情況 API 的一些常見問題集解答。

## <a name="what-meter-ids-can-i-see"></a>我可以看到哪個計量識別碼？
目前，系統是針對網路、儲存體與計算資源提供者回報使用情況。

| **資源提供者** | **Meter ID** | **計量名稱** | **單位** | **其他資訊** |
| --- | --- | --- | --- | --- | 
| **網路** |F271A8A388C44D93956A063E1D2FA80B |靜態 IP 位址使用情況 |IP 位址|已使用的 IP 位址計數 | 
| |9E2739BA86744796B465F64674B822BA |動態 IP 位址使用情況 |IP 位址|已使用的 IP 位址計數 | 
| **儲存體** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*小時 |資料表所取用的總容量 |
| | B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*小時 |分頁 Blob 所取用的總容量 |
| | B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*小時 |佇列所取用的總容量 |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*小時 |區塊 Blob 所取用的總容量 |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |要求計數 (以萬為單位) |資料表服務要求 (以萬為單位) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |輸入資料 (GB) |資料表服務資料輸入 (GB) |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |輸出 (GB) |資料表服務資料輸出 (GB) |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |要求計數 (以萬為單位) |Blob 服務要求 (以萬為單位) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |輸入資料 (GB) |Blob 服務資料輸入 (GB) |
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |輸出 (GB) |Blob 服務資料輸出 (GB) |
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |要求計數 (以萬為單位) |佇列服務要求 (以萬為單位) |
| | E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |輸入資料 (GB) |佇列服務資料輸入 (GB) | 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |輸出 (GB) |佇列服務資料輸出 (GB) |
| **計算** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |基礎 VM 大小小時 |虛擬核心分鐘數 | VM 所執行的虛擬核心數目時間 (分鐘) |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM 大小小時 |虛擬核心分鐘數 | VM 所執行的虛擬核心數目時間 (分鐘) |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |VM 大小小時 |VM 小時 |同時擷取基本與 VM。 不針對虛擬核心調整 |
| **Key Vault** | EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Key Vault 交易 | 要求計數 (以萬為單位)| 由 Key Vault 所接收的 REST API 要求數目 |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Azure Stack 使用情況 API 與 [Azure 使用情況 API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (目前為公開預覽版) 相較有何異同？
* 租用戶使用量 API 與 Azure API 相同，但有一點除外：Azure Stack 目前不支援 *showDetails* 旗標。
* 提供者使用量 API 只適用於 Azure Stack。
* 目前，Azure Stack 不提供 Azure 中提供的 [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx)。

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>「使用時間」與「回報時間」之間有何差異？
使用情況資料報告有兩個主要時間值：

* **回報時間**。 使用情況事件進入使用情況系統的時間
* **使用時間**。 取用 Azure Stack 資源的時間

針對特定使用情況事件，您可能會發現「使用時間」與「回報時間」的值有差異。 延遲在任何環境中可能長達數小時。

目前，您只能依「回報時間」查詢。

## <a name="what-do-these-usage-api-error-codes-mean"></a>這些使用情況 API 錯誤碼的意義為何？
| **HTTP 狀態碼** | **錯誤碼** | **描述** |
| --- | --- | --- |
| 400/不正確的要求 |*NoApiVersion* |未提供 *api-version* 查詢參數。 |
| 400/不正確的要求 |*InvalidProperty* |缺少屬性或屬性具有無效的值。 回應主體錯誤碼中的訊息指出缺少的屬性。 |
| 400/不正確的要求 |*RequestEndTimeIsInFuture* |*ReportedEndTime* 的值是未來時間。 此引數不允許未來時間值。 |
| 400/不正確的要求 |*SubscriberIdIsNotDirectTenant* |提供者 API 呼叫所使用的訂用帳戶識別碼不是呼叫者的有效租用戶。 |
| 400/不正確的要求 |*SubscriptionIdMissingInRequest* |缺少呼叫者的訂用帳戶識別碼。 |
| 400/不正確的要求 |*InvalidAggregationGranularity* |所要求的彙總細微性無效。 有效的值為每日與每小時。 |
| 503 |*ServiceUnavailable* |發生可重試的錯誤，因為服務忙碌中或呼叫已由系統進行節流處理。 |

## <a name="next-steps"></a>後續步驟
[Azure Stack 中的客戶帳務與退款](azure-stack-billing-and-chargeback.md)

[提供者資源使用情況 API](azure-stack-provider-resource-api.md)

[租用戶資源使用情況 API](azure-stack-tenant-resource-usage-api.md)


