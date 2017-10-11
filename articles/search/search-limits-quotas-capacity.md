---
title: "Azure 搜尋中的服務限制 | Microsoft Docs"
description: "容量計劃中使用的服務限制，以及 Azure 搜尋服務要求和回應的最大限制。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/07/2017
ms.author: heidist
ms.openlocfilehash: 60e63401e3915e62e1ec5ac03cd548c291580b24
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="service-limits-in-azure-search"></a>Azure 搜尋中的服務限制
儲存體與工作負載的最大限制，以及索引、文件和其他物件的數量上限，皆取決於您是否在**免費**、**基本**，還是**標準**定價層中[佈建 Azure 搜尋服務](search-create-service-portal.md)。

* **免費** 的是 Azure 訂用帳戶隨附的多租用戶共用服務。 此為針對現有訂用帳戶提供的免費選項，無須額外費用，可讓您試驗服務後再註冊專用資源。
* **基本**會針對生產環境工作負載提供較小規模的專用計算資源。
* **標準** 是在專用的機器上執行，在各層級具有更多的儲存和處理容量。 標準共有四個等級︰S1、S2、S3 及 S3 高密度 (S3 HD)。

> [!NOTE]
> 服務會佈建在特定層。 如果您需要跨層以取得更多容量，您必須佈建新服務 (未提供就地升級)。 如需詳細資訊，請參閱[選擇 SKU 或階層](search-sku-tier.md)。 若要深入了解如何在已佈建的服務內調整容量，請參閱[調整適用於查詢和編製索引工作負載的資源等級](search-capacity-planning.md)。
>

## <a name="per-subscription-limits"></a>每一訂用帳戶限制
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="per-service-limits"></a>每一服務限制
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## <a name="per-index-limits"></a>每一索引限制
索引限制與索引子限制之間有一對一的對應關係。 若限制 200 個索引，則在相同服務中，索引子的最大限制也是為 200 個。

| 資源 | 免費 | 基本 | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| 索引︰每個索引的欄位上限 |1000 |100 <sup>1</sup> |1000 |1000 |1000 |1000 |
| 索引：每個索引的評分設定檔上限 |100 |100 |100 |100 |100 |100 |
| 索引：每個設定檔的函式上限 |8 |8 |8 |8 |8 |8 |
| 索引子︰每次叫用的索引編製負載上限 |10,000 份文件 |僅限制文件上限 |僅限制文件上限 |僅限制文件上限 |僅限制文件上限 |N/A <sup>2</sup> |
| 索引子︰執行時間上限 | 1-3 分鐘 <sup>3</sup> |24 小時 |24 小時 |24 小時 |24 小時 |N/A <sup>2</sup> |
| Blob 索引子︰Blob 大小上限，MB |16 |16 |128 |256 |256 |N/A <sup>2</sup> |
| Blob 索引子︰從 Blob 擷取的內容字元數上限 |32,000 |64,000 |4 百萬 |4 百萬 |4 百萬 |N/A <sup>2</sup> |

<sup>1</sup> 基本層是唯一具有較低限制 (每個索引 100 個欄位) 的 SKU。

<sup>2</sup> S3 HD 目前不支援索引子。 如果您對此功能有迫切的需求，請連絡 Azure 支援。

<sup>3</sup> 免費層的索引子執行時間上限，針對 Blob 來源為 3 分鐘，針對其他所有資料來源為 1 分鐘。

## <a name="document-size-limits"></a>文件大小限制
| 資源 | 免費 | 基本 | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| 每個索引 API 的文件大小 |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |

指呼叫「索引 API」時的文件大小上限。 文件大小是索引 API 要求主體大小的實際限制。 由於您可以一次將多份文件整批傳遞給「索引 API」，因此大小限制實際上取決於批次中的文件數量。 針對具有單一文件的批次，文件大小上限為 16 MB 的 JSON。

為了降低文件大小，請記得從要求中排除不可查詢的資料。 影像和其他二進位資料無法執行查詢，而且不應該儲存於索引中。 若要將不可搜尋的資料整合到搜尋結果，請定義不可搜尋的欄位，將 URL 參考儲存於資源中。

## <a name="workload-limits-queries-per-second"></a>工作負載限制 (每秒查詢次數)
| 資源 | 免費 | 基本 | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| QPS |N/A  |~3/每個複本 |~15/每個複本 |~60/每個複本 |>60/每個複本 |>60/每個複本 |

每秒查詢次數 (QPS) 是根據啟發學習法所得的近似值，這是使用模擬及實際的客戶工作負載來導出的估計值。 確實的 QPS 輸送量會視您的資料和查詢本質而有所差異。

雖然上面提供粗略的估計值，但是實際的查詢率難以判斷，尤其是在輸送量會依可用頻寬和系統資源競爭而有所不同的「免費」共用服務中。 在「免費層」中，運算資源和儲存體資源是由多個訂用帳戶共用，因此您解決方案的 QPS 將一律依有多少其他工作負載在同時執行而有所不同。

在標準層級中，由於可控制較多的參數，所以能更準確地估計 QPS。 如需有關如何計算您工作負載之 QPS 的指引，請參閱 [管理您的搜尋解決方案](search-manage.md) 。

## <a name="api-request-limits"></a>API 要求限制
* 每個要求最多 16 MB <sup>1</sup>
* 最長 8 KB 的 URL 長度
* 每個索引上傳、合併、或刪除批次最多包含 1000 個文件
* $orderby 子句中最多 32 個欄位
* 最大搜尋詞彙的大小是 utf-8 編碼文字的 32,766 個位元組 (32 KB 減 2 個位元組)

<sup>1</sup> 在「Azure 搜尋服務」中，要求主體的上限是 16 MB，這會針對不受理論上限制約束之個別欄位或集合的內容強加實際限制 (如需有關欄位組合和限制的詳細資訊，請參閱[支援的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx))。

## <a name="api-response-limits"></a>API 回應限制
* 每一頁搜尋結果最多傳回 1000 個文件
* 每個建議 API 要求最多傳回 100 個建議

## <a name="api-key-limits"></a>API 金鑰限制
API 金鑰可用於服務驗證。 有兩種類型。 系統管理金鑰是在要求標頭中指定，並會授與完整的服務讀寫存取。 查詢金鑰為唯讀並在 URL 上指定，且通常會發佈到用戶端應用程式。

* 每個服務最多 2 個系統管理金鑰
* 每個服務最多 50 個查詢金鑰
