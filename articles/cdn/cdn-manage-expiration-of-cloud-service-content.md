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
ms.openlocfilehash: 87d65479960cd6b5977fd7ac31cbb71afc0959e2
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>管理 Azure 內容傳遞網路中 Web 內容的期限
> [!div class="op_single_selector"]
> * [Azure 網頁內容](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob 儲存體](cdn-manage-expiration-of-blob-content.md)
> 

來自任何可公開存取的原始 Web 伺服器的檔案均可在 Azure 內容傳遞網路 (CDN) 中加以快取，直到其存留時間 (TTL) 結束。 TTL 是由來自原始伺服器之 HTTP 回應中的 `Cache-Control` 標頭所決定。 本文說明如何設定 Microsoft Azure App Service 之 Web Apps 功能、Azure Cloud Services、ASP.NET 應用程式、Internet Information Services (IIS) 網站的 `Cache-Control` 標頭，上述所有項目的設定方式均類似。 您可以使用組態檔，或以程式設計方式來設定 `Cache-Control` 標頭。

> [!TIP]
> 您可以選擇不替檔案設定 TTL。 在此情況下，Azure CDN 會自動套用預設為期 7 天的 TTL。
> 
> 針對 Azure CDN 如何加快對檔案和其他資源的存取速度，如需詳細資訊請參閱 [Azure 內容傳遞網路概觀](cdn-overview.md)。
> 

## <a name="setting-cache-control-headers-by-using-configuration-files"></a>使用組態檔設定 Cache-Control 標頭
針對影像和樣式表之類的靜態內容，您可以藉由修改 Web 應用程式的 **applicationHost.config** 或 **web.config** 組態檔來控制更新頻率。 這兩個檔案中的 `<system.webServer>/<staticContent>/<clientCache>` 元素都會設定您內容的 `Cache-Control` 標頭。

### <a name="using-applicationhostconfig-files"></a>使用 ApplicationHost.config 檔案
**ApplicationHost.config** 檔案是 IIS 組態系統的根檔案。 **ApplicationHost.config** 檔案中的組態設定會影響網站上的所有應用程式，但該組態設定會由 Web 應用程式中現有的任何 **Web.config** 檔案設定覆寫。

### <a name="using-webconfig-files"></a>使用 Web.config 檔案
您可以使用 **Web.config** 檔案，自訂整個 Web 應用程式或 Web 應用程式上特定目錄的行為。 通常來說，您的 Web 應用程式根資料夾中至少會有一個 **Web.config** 檔案。 針對特定資料夾中的每個 **web.config** 檔案，組態設定會影響資料夾中的所有項目及所有子資料夾，除非這些項目在子資料夾層級就由 **Web.config** 覆寫。 例如，您可以在 Web 應用程式中根資料夾的 **Web.config** 檔案中設定 `<clientCache>` 元素。 您也可以在子資料夾中新增具有更多變數內容的 **Web.config** 檔案 (例如 `\frequent`) 並設定其 `<clientCache>` 元素以快取子資料夾的內容達六小時。 最終結果是，除了 `\frequent` 目錄中的所有內容，整個網站會進行三天僅六小時的快取。  

下列 XML 範例會顯示如何在組態檔中設定 `<clientCache>` 元素，以指定最多三天期間：  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

若要使用 **cacheControlMaxAge** 屬性，您必須將 **cacheControlMode** 屬性的值設為 `UseMaxAge`。 此設定會產生要新增至回應的 HTTP 標頭及指示詞，`Cache-Control: max-age=<nnn>`。 **cacheControlMaxAge** 屬性的時間範圍值格式為 `<days>.<hours>:<min>:<sec>`。 此值會轉換為秒，且會當做 `Cache-Control` `max-age` 指示詞使用。 如需 `<clientCache>` 元素的詳細資訊，請參閱[用戶端快取<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。  

## <a name="setting-cache-control-headers-programmatically"></a>以程式設計方式設定 Cache-Control 標頭
針對 ASP.NET 應用程式，設定 .NET API 的 **HttpResponse.Cache** 屬性即可透過程式設計方式控制 CDN 快取行為。 如需 **HttpResponse.Cache** 屬性的資訊，請參閱 [HttpResponse.Cache 屬性](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)和 [HttpCachePolicy 類別](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

若要以程式設計方式快取 ASP.NET 中的應用程式內容，遵循下列步驟：
   1. 確認內容已標示為可快取(將 `HttpCacheability` 設定為 `Public`)。 
   2. 呼叫以下其中一個 `HttpCachePolicy` 方法來設定快取驗證程式：
      - 呼叫 `SetLastModified` 以設定 `Last-Modified` 標頭的時間戳記。
      - 呼叫 `SetETag` 以設定 `ETag` 標頭值。
   3. 您也可以選擇性地呼叫 `SetExpires`，設定 `Expires` 標頭值，以指定快取到期時間。 否則，預設快取會套用本文件先前所述的啟發學習法。

例如，若要快取一個小時的內容，請加入下列 C# 程式碼：  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>測試 Cache-Control 標頭
您可以輕鬆地驗證網頁內容的 TTL 設定。 使用瀏覽器的[開發人員工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)，測試網頁內容是否包含 `Cache-Control` 回應標頭。 您也可以使用 **wget**、[Postman](https://www.getpostman.com/) 或 [Fiddler](http://www.telerik.com/fiddler) 之類的工具來檢查回應標頭。

## <a name="next-steps"></a>後續步驟
* [深入了解 **clientCache** 項目](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [閱讀 **HttpResponse.Cache** 屬性的文件](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [閱讀 **HttpCachePolicy 類別**的文件](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

