---
title: "以 Azure API 管理進行進階要求節流"
description: "了解如何使用 Azure API 管理來建立及套用彈性配額和速率限制原則。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 356f98aec072a1295915ae0701a3e3cd793aba07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>以 Azure API 管理進行進階要求節流
能夠節流傳入要求是 Azure API 管理的重要角色。 藉由控制要求的速率或傳輸的要求/資料總量，API 管理讓 API 提供者能夠保護其 API 不被濫用，並建立不同 API 產品層級的價值。

## <a name="product-based-throttling"></a>依產品節流
到目前為止，速率節流功能侷限於特定產品訂閱的限定範圍 (基本上是索引鍵)，是在 API 管理發行者入口網站中定義。 這可用來讓 API 提供者將限制套用至註冊使用其 API 的開發人員，不過，舉例來說，它無法協助節流 API 的個別使用者。 想讓開發人員的應用程式的單一使用者取用整個配額，並讓開發人員的其他客戶無法使用應用程式，是有可能的。 同樣的，數個產生大量要求的客戶可能會限制偶爾使用者的存取權。

## <a name="custom-key-based-throttling"></a>依自訂索引鍵節流
新的 [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) 和 [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) 原則提供明顯更有彈性的流量控制解決方案。 這些新原則可讓您定義運算式，以識別將用來追蹤流量使用的索引鍵。 其運作的方式用範例來說明最簡單。 

## <a name="ip-address-throttling"></a>IP 位址節流
下列原則會限制單一用戶端 IP 位址每一分鐘只有 10 個呼叫，等於每個月總數為 1,000,000 個呼叫和 10,000 KB 頻寬。 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

如果網際網路上的所有用戶端皆使用唯一 IP 位址，這是可能是限制使用者使用量的有效方式。 不過，很有可能多個使用者共用單一公用 IP 位址，因為他們透過 NAT 裝置存取網際網路。 儘管如此，對允許未驗證存取的 API 來說， `IpAddress` 可能是最佳選項。

## <a name="user-identity-throttling"></a>使用者身分識別節流
如果使用者經過驗證，則可以根據該名使用者的唯一身分識別產生節流索引鍵。

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

在此範例中，我們要擷取授權的標頭，將它轉換成 `JWT` 物件，並使用權杖的主體來識別使用者，並使用它做速率限制索引鍵。 如果使用者身分識別是儲存在 `JWT` 中做為其中一個宣告，則該值可用於它的位置。

## <a name="combined-policies"></a>結合的原則
雖然新的節流原則比現有節流原則提供更多的控制，但仍會有結合兩種功能的值。 依產品訂用帳戶金鑰 ([依訂用帳戶限制呼叫率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate)和[依訂用帳戶設定使用量配額](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) 進行節流是一種根據使用量層級收費、讓 API 創造獲利的絕佳方式。 更精細的依使用者控制節流則和其互補，並防止一位使用者的行為降低另一位使用者的體驗。 

## <a name="client-driven-throttling"></a>用戶端導向節流
若使用 [原則運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)定義節流索引鍵，則是由 API 提供者選擇如何設定節流的範圍。 不過，開發人員可能想要控制他們對自己的客戶的速率限制。 API 提供者可以藉由導入自訂標頭來做到這一點，以允許開發人員的用戶端應用程式與 API 通訊索引鍵。

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

這可讓開發人員的用戶端應用程式選擇要如何建立速率限制索引鍵。 加上一些巧思，用戶端開發人員可以透過配置索引鍵組給使用者和輪流使用索引鍵，建立自己的速率層。

## <a name="summary"></a>Summary
Azure API 管理提供速率和配額節流，不但能保護您的 API 服務，並為您的 API 服務增加價值。 新的節流原則與自訂範圍規則，可讓您更精細的控制這些原則，進而讓您的客戶建置更好的應用程式。 本文中的範例示範如何使用這些新原則，分別使用用戶端 IP 位址、使用者身分識別及用戶端產生值來製造速率限制索引鍵。 不過，訊息中還有許多其他部份可以利用，例如使用者代理程式、URL 路徑片段、訊息大小。

## <a name="next-steps"></a>後續步驟
敬請不吝賜教在 Disqus 的本主題系列中提供意見。 我們很想知道其他在您的案例中是合理選擇的可能索引鍵值。

## <a name="watch-a-video-overview-of-these-policies"></a>觀看這些原則的影片概觀
如需有關本文中所涵蓋的 [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) 和 [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) 原則的詳細資訊，請觀看以下影片。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Advanced-Request-Throttling-with-Azure-API-Management/player]
> 
> 

