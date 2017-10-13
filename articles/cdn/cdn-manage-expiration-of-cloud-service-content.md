---
title: "在 Azure CDN 中管理 Web 內容的到期 | Microsoft Docs"
description: "了解如何在 Azure CDN 中管理 Azure Web Apps/雲端服務、ASP.NET 或 IIS 內容的到期。"
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c207d780857a61d4b1fc0f39e6185cae67abc955
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>在 Azure CDN 中管理 Azure Web Apps/雲端服務、ASP.NET 或 IIS 內容的到期
> [!div class="op_single_selector"]
> * [Azure Web Apps/雲端服務、ASP.NET 或 IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure 儲存體 Blob 服務](cdn-manage-expiration-of-blob-content.md)
> 
> 

來自任何可公開存取的原始 Web 伺服器的檔案均可在 Azure CDN 中加以快取，直到其存留時間 (TTL) 結束。  TTL 是由來自原始伺服器之 HTTP 回應中的 [*Cache-Control* 標頭](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)所決定。  本文說明如何設定Azure Web Apps、Azure 雲端服務、ASP.NET 應用程式和 Internet Information Services 網站的 `Cache-Control` 標頭，上述所有項目的設定方式均類似。

> [!TIP]
> 您可以選擇不對檔案設定 TTL。  在此情況下，Azure CDN 會自動套用預設為期 7 天的 TTL。
> 
> 如需 Azure CDN 如何運作以加快檔案和其他資源存取速度的詳細資訊，請參閱 [Azure CDN 概觀](cdn-overview.md)。
> 
> 

## <a name="setting-cache-control-headers-in-configuration"></a>在組態中設定 Cache-Control 標頭
針對影像和樣式表之類的靜態內容，您可以藉由修改 Web 應用程式的 **applicationHost.config** 或 **web.config** 檔案來控制更新頻率。  組態檔中的 **system.webServer\staticContent\clientCache** 項目會設定內容的 `Cache-Control` 標頭。 針對 **web.config**，除非在子資料夾層級中加以覆寫，否則組態設定會影響資料夾和所有子資料夾中的所有項目。  例如，您可以設定根目錄的預設存留時間具有快取 3 天的所有靜態內容，但是有一個子資料夾具有快取設定為 6 小時的更多變數內容。  針對 **applicationHost.config**，網站上的所有應用程式都會受到影響，但可在應用程式的 **web.config** 檔案中加以覆寫。

下列 XML 說明設定 **clientCache** 以指定最長使用期限 (3 天) 的範例：  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

指定 **UseMaxAge**，會根據 **CacheControlMaxAge** 屬性中所指定的值，將 `Cache-Control: max-age=<nnn>` 標頭加入回應。 **cacheControlMaxAge** 屬性的 timespan 格式為 <days>.<hours>:<min>:<sec>。 如需 **clientCache** 節點的詳細資訊，請參閱[用戶端快取<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。  

## <a name="setting-cache-control-headers-in-code"></a>在程式碼中設定 Cache-Control 標頭
針對 ASP.NET 應用程式，設定 **HttpResponse.Cache** 屬性即可透過程式設計方式設定 CDN 快取行為。 如需 **HttpResponse.Cache** 屬性的詳細資訊，請參閱 [HttpResponse.Cache 屬性](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)和 [HttpCachePolicy 類別](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

如果您想要在 ASP.NET 中以程式設計方式快取應用程式內容，請務必將 HttpCacheability 設為「Public」 ，以將內容標示為可進行快取。 也請確定已設定快取驗證程式。 快取驗證程式可以是透過呼叫 SetLastModified 所設定的上次修改時間戳記，或透過呼叫 SetETag 所設定的 etag 值。 您也可以選擇性地透過呼叫 SetExpires 來指定快取到期時間，或可以依賴本文件稍早所述的預設快取啟發學習法。  

例如，若要快取一個小時的內容，請加入下列內容：  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>後續步驟
* [深入了解 **clientCache** 項目](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [閱讀 **HttpResponse.Cache** 屬性的文件](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [閱讀 **HttpCachePolicy 類別**的文件](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

