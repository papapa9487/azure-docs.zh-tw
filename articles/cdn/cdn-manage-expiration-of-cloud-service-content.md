---
title: "管理 Azure 內容傳遞網路中 Web 內容的期限 | Microsoft Docs"
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
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>管理 Azure 內容傳遞網路中 Web 內容的期限
 在 Azure CDN 中
> [!div class="op_single_selector"]
> * [Azure Web Apps/雲端服務、ASP.NET 或 IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob 儲存體](cdn-manage-expiration-of-blob-content.md)
> 

來自任何可公開存取的原始 Web 伺服器的檔案均可在 Azure 內容傳遞網路 (CDN) 中加以快取，直到其存留時間 (TTL) 結束。 TTL 是由來自原始伺服器之 HTTP 回應中的 [`Cache-Control` 標頭](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)所決定。 本文說明如何設定 Microsoft Azure App Service 之 Web Apps 功能、Azure Cloud Services、ASP.NET 應用程式、Internet Information Services 網站的 `Cache-Control` 標頭，上述所有項目的設定方式均類似。

> [!TIP]
> 您可以選擇不替檔案設定 TTL。 在此情況下，Azure CDN 會自動套用預設為期 7 天的 TTL。
> 
> 針對 Azure CDN 如何加快對檔案和其他資源的存取速度，如需詳細資訊請參閱 [Azure 內容傳遞網路概觀](cdn-overview.md)。
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>在設定檔中設定 Cache-Control 標頭
針對影像和樣式表之類的靜態內容，您可以藉由修改 Web 應用程式的 **applicationHost.config** 或 **web.config** 檔案來控制更新頻率。 組態檔中的 **system.webServer\staticContent\clientCache** 元素會設定內容的 `Cache-Control` 標頭。 針對 **web.config**，除非在子資料夾層級被覆寫，否則組態設定會影響資料夾和所有子資料夾中的所有項目。 例如，您可以將根資料夾的預設 TTL 設定為快取 3 天的所有靜態內容，並以變數內容將子資料夾設定為僅快取 6 個小時的內容。 雖然 **applicationHost.config** 檔案的設定會影響網站上的所有應用程式，但此設定會被應用程式中任何現有 **web.config** 檔案的設定覆寫。

下列 XML 說明如何設定 **clientCache** 以指定最長使用期限 3 天：  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

指定 **UseMaxAge**，系會根據 **CacheControlMaxAge** 屬性中指定的值，將 `Cache-Control: max-age=<nnn>` 標頭加入回應。 **cacheControlMaxAge** 屬性的時間範圍格式為 `<days>.<hours>:<min>:<sec>`。 如需 **clientCache** 節點的詳細資訊，請參閱[用戶端快取<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。  

## <a name="setting-cache-control-headers-in-code"></a>在程式碼中設定 Cache-Control 標頭
針對 ASP.NET 應用程式，設定 **HttpResponse.Cache** 屬性即可透過程式設計方式控制 CDN 快取行為。 如需 **HttpResponse.Cache** 屬性的詳細資訊，請參閱 [HttpResponse.Cache 屬性](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)和 [HttpCachePolicy 類別](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

若要以程式設計方式快取 ASP.NET 中的應用程式內容，遵循下列步驟：
   1. 確認內容已標示為可快取(將 `HttpCacheability` 設定為 *Public*)。 
   2. 呼叫下列方法之一來設定快取驗證程式：
      - 呼叫 `SetLastModified` 可設定 LastModified 時間戳記。
      - 呼叫 `SetETag` 可設定 `ETag` 值。
   3. (選擇性) 藉由呼叫 `SetExpires` 來指定快取到期時間。 否則，預設快取會套用本文件先前所述的啟發學習法。

例如，若要快取一個小時的內容，請加入下列 C# 程式碼：  

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

