---
title: "將 CDN 新增至 Azure App Service | Microsoft Docs"
description: "將內容傳遞網路 (CDN) 新增至 Azure App Service，以從您在世界各地的客戶附近的伺服器快取和傳遞靜態檔案。"
services: app-service\web
author: syntaxc4
ms.author: cfowler
ms.date: 05/31/2017
ms.topic: tutorial
ms.service: app-service-web
manager: erikre
ms.workload: web
ms.custom: mvc
ms.openlocfilehash: 257b75d01f3904661c1a188a2d53ffcb74f48f06
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="add-a-content-delivery-network-cdn-to-an-azure-app-service"></a>將內容傳遞網路 (CDN) 新增至 Azure App Service

[Azure 內容傳遞網路 (CDN)](../cdn/cdn-overview.md) 會在策略性放置的位置上快取靜態 Web 內容，以提供最大輸送量來將內容傳遞給使用者。 CDN 也可降低您的 Web 應用程式的伺服器負載。 本教學課程說明如何將 Azure CDN 新增至 [Azure App Service 中的 Web 應用程式](app-service-web-overview.md)。 

以下是您將使用的範例靜態 HTML 網站首頁︰

![範例應用程式首頁](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

您將學到什麼：

> [!div class="checklist"]
> * 建立 CDN 端點。
> * 重新整理快取的資產。
> * 使用查詢字串來控制快取的版本。
> * 使用 CDN 端點的自訂網域。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

- [安裝 Git](https://git-scm.com/)
- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>建立 Web 應用程式

若要建立您將使用的 Web 應用程式，請遵循[靜態 HTML 快速入門](app-service-web-get-started-html.md)的**瀏覽至應用程式**步驟。

### <a name="have-a-custom-domain-ready"></a>備妥自訂網域

若要完成本教學課程的自訂網域步驟，您需要擁有自訂網域並且具有網域提供者 (例如 GoDaddy) 的 DNS 登錄存取權。 例如，若要為 `contoso.com` 和 `www.contoso.com` 新增 DNS 項目，您必須有權設定 `contoso.com` 根網域的 DNS 設定。

如果您還沒有網域名稱，請考慮遵循 [App Service 網域教學課程](custom-dns-web-site-buydomains-web-app.md)的作法，使用 Azure 入口網站來購買網域。 

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

開啟瀏覽器並瀏覽至 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-cdn-profile-and-endpoint"></a>建立 CDN 設定檔和端點

在左側導覽中，選取 [應用程式服務]，然後選取您在[靜態 HTML 快速入門](app-service-web-get-started-html.md)中建立的應用程式。

![在入口網站中選取 App Service 應用程式](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

在 [App Service] 頁面的 [設定] 區段中，選取 [網路] > [設定您應用程式的 Azure CDN]。

![在入口網站中選取 CDN](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

在 [Azure 內容傳遞網路] 頁面中，提供表格中所指定的 [新端點] 設定。

![在入口網站中建立設定檔和端點](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| 設定 | 建議的值 | 說明 |
| ------- | --------------- | ----------- |
| **CDN 設定檔** | myCDNProfile | 選取 [新建] 以建立 CDN 設定檔。 CDN 設定檔是定價層相同的 CDN 端點集合。 |
| **定價層** | 標準 Akamai | [定價層](../cdn/cdn-overview.md#azure-cdn-features)指定提供者和可用的功能。 在本教學課程中，我們會使用標準 Akamai。 |
| **CDN 端點名稱** | azureedge.net 網域中任何唯一的名稱 | 您可在網域 *\<endpointname>.azureedge.net* 存取快取的資源。

選取 [ **建立**]。

Azure 會建立設定檔和端點。 新端點會出現在相同頁面上的 [端點] 清單中，而且其佈建後的狀態為 [執行中]。

![清單中的新端點](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>測試 CDN 端點

如果您選取了 Verizon 定價層，通常需要大約 90 分鐘的時間進行端點傳播。 若為 Akamai，傳播需要幾分鐘的時間

範例應用程式有 `index.html` 檔案以及包含其他靜態資產的 css、img 和 js 資料夾。 在 CDN 端點上，上述所有檔案的內容路徑都相同。 例如，下列 URL 可存取 css 資料夾中的 bootstrap.css 檔案︰

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

在瀏覽器中瀏覽至下列 URL：

```
http://<endpointname>.azureedge.net/index.html
```

![CDN 提供的範例應用程式首頁](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 您會看到與您稍早在 Azure Web 應用程式中執行的相同分頁。 Azure CDN 已擷取原始 Web 應用程式的資產，並從 CDN 端點提供這些資產

若要確保此頁面已在 CDN 中快取，請重新整理此頁面。 CDN 有時需要相同資產的兩個要求，才能快取所要求的內容。

如需建立 Azure CDN 設定檔和端點的詳細資訊，請參閱[開始使用 Azure CDN](../cdn/cdn-create-new-endpoint.md)。

## <a name="purge-the-cdn"></a>清除 CDN

CDN 會根據存留時間 (TTL) 組態，從原始 web 應用程式定期重新整理其資源。 預設 TTL 為 7 天。

有時候，您可能需要在 TTL 到期日之前重新整理CDN 更新的內容部署至 web 應用程式時，-例如，。 若要觸發重新整理，您可以手動清除 CDN 資源。 

在本節的教學課程中，您會將變更部署到 Web 應用程式並清除 CDN，進而觸發 CDN 重新整理其快取。

### <a name="deploy-a-change-to-the-web-app"></a>將變更部署到 Web 應用程式

開啟 `index.html` 檔案並將 "- V2" 新增至 H1 標題，如下列範例所示︰ 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

認可您的變更並將它部署至 Web 應用程式。

```bash
git commit -am "version 2"
git push azure master
```

完成部署後，瀏覽至 Web 應用程式 URL，您會看到變更。

```
http://<appname>.azurewebsites.net/index.html
```

![Web 應用程式標題中的 V2](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

瀏覽至首頁的 CDN 端點 URL，因為 CDN 中快取的版本尚未到期，所以您不會看到變更。 

```
http://<endpointname>.azureedge.net/index.html
```

![CDN 標題中沒有 V2](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>在入口網站中清除 CDN

若要觸發 CDN 更新其快取的版本，請清除 CDN。

在入口網站的左側導覽中，選取 [資源群組]，然後選取您為 Web 應用程式 (myResourceGroup) 建立的資源群組。

![選取資源群組](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

在資源清單中，選取您的 CDN 端點。

![選取端點](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

在 [端點] 頁面的頂端，按一下 [清除]。

![選取清除](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

輸入您想要清除的內容路徑。 您可以傳遞完整檔案路徑來清除個別檔案，也可以傳遞路徑區段來清除並重新整理資料夾中的所有內容。 因為您變更了 `index.html`，請確定這是其中一個路徑。

選取頁面底部的 [清除]。

![清除頁面](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>確認 CDN 已更新

等到清除要求完成處理，通常需要幾分鐘的時間。 若要查看目前的狀態，請選取頁面頂端的鈴鐺圖示。 

![清除通知](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

瀏覽至 `index.html` 的 CDN 端點 URL，您現在會看到已新增至首頁標題的 V2。 這會顯示已重新整理的 CDN 快取。

```
http://<endpointname>.azureedge.net/index.html
```

![CDN 標題中的 V2](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

如需詳細資訊，請參閱[清除 Azure CDN 端點](../cdn/cdn-purge-endpoint.md)。 

## <a name="use-query-strings-to-version-content"></a>使用查詢字串來控制內容版本

Azure CDN 提供下列快取行為選項︰

* 忽略查詢字串
* 略過查詢字串的快取
* 快取每個唯一的 URL 

上述第一個選項是預設值，這表示不管 URL 中的查詢字串為何，資產都只有一個快取的版本。 

在本節的教學課程中，您可將快取行為變更為快取每個唯一 URL。

### <a name="change-the-cache-behavior"></a>變更快取行為

在 Azure 入口網站的 [CDN 端點] 頁面中，選取 [快取]。

從 [查詢字串快取行為] 下拉式清單中，選取 [快取每個唯一 URL]。

選取 [ **儲存**]。

![選取查詢字串快取行為](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>確認唯一的 URL 會分開快取

在瀏覽器中，瀏覽至 CDN 端點首頁，但包含查詢字串︰ 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

CDN 會傳回目前的 Web 應用程式內容，其標題中包含 "V2"。 

若要確保此頁面已在 CDN 中快取，請重新整理此頁面。 

開啟 `index.html` 並將 "V2" 變更為 "V3"，然後部署變更。 

```bash
git commit -am "version 3"
git push azure master
```

在瀏覽器中，移至含有新查詢字串 (例如`q=2`) 的 CDN 端點 URL。 CDN 會取得目前的 `index.html` 檔案並顯示 "V3"。  但是，如果您瀏覽至含有 `q=1` 查詢字串的 CDN 端點，您會看到 "V2"。

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![CDN 標題中的 V3，查詢字串 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![CDN 標題中的 V2，查詢字串 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

此輸出會顯示每個查詢字串是以不同方式處理：

* 以前使用 q=1，因此會傳回快取內容 (V2)。
* q=2 是新的，因此會擷取及傳回最新的 Web 應用程式內容 (V3)。

如需詳細資訊，請參閱[使用查詢字串控制 Azure CDN 快取行為](../cdn/cdn-query-string.md)。

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>將自訂網域對應至 CDN 端點

您會建立 CNAME 記錄，以將自訂網域對應至 CDN 端點。 CNAME 記錄是將來源網域對應至目的地網域的 DNS 功能。 例如，您可能會將 `cdn.contoso.com` 或 `static.contoso.com` 對應到 `contoso.azureedge.net`。

如果您沒有自訂網域，請考慮遵循 [App Service 網域教學課程](custom-dns-web-site-buydomains-web-app.md)的作法，使用 Azure 入口網站來購買網域。 

### <a name="find-the-hostname-to-use-with-the-cname"></a>尋找要搭配 CNAME 使用的主機名稱

在 Azure 入口網站的 [端點] 頁面中，確定已選取左側導覽中的 [概觀]，然後選取頁面頂端的 [+ 自訂網域] 按鈕。

![選取新增自訂網域](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

在 [新增自訂網域] 頁面中，您會看到要用於建立 CNAME 記錄的端點主機名稱。 主機名稱衍生自 CDN 端點 URL：**&lt;EndpointName>.azureedge.net**。 

![新增網域頁面](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>透過您的網域註冊機構設定 CNAME

瀏覽至您網域註冊機構的網站，並找出用於建立 DNS 記錄的區段。 您可能會在 **Domain Name**、**DNS** 或 **Name Server Management** 等區段中發現此頁面。

尋找管理 CNAME 的區段。 您可能需要移至進階設定頁面，並尋找 CNAME、Alias 或 Subdomains 單字。

建立 CNAME 記錄，將您選擇的子網域 (例如 **static** 或 **cdn**) 對應到入口網站中稍早顯示的 [端點主機名稱]。 

### <a name="enter-the-custom-domain-in-azure"></a>在 Azure 中輸入自訂網域

返回 [新增自訂網域]  頁面，在對話方塊中輸入您的自訂網域 (包括子網域)。 例如，輸入 `cdn.contoso.com`。   
   
Azure 會確認您所輸入的網域名稱存在 CNAME 記錄。 如果 CNAME 正確，您的自訂網域就會驗證。

可能需要時間讓 CNAME 記錄傳播到網際網路上的名稱伺服器。 如果您的網域不會立即驗證，請稍候幾分鐘，然後再試一次。

### <a name="test-the-custom-domain"></a>測試自訂網域

在瀏覽器中，使用自訂網域 (例如 `cdn.contoso.com/index.html`) 瀏覽至 `index.html` 檔案，確認結果與您直接移至 `<endpointname>azureedge.net/index.html` 時相同。

![使用自訂網域 URL 的範例應用程式首頁](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

如需詳細資訊，請參閱[將 Azure CDN 內容對應至自訂網域](../cdn/cdn-map-content-to-custom-domain.md)。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

您已了解如何︰

> [!div class="checklist"]
> * 建立 CDN 端點。
> * 重新整理快取的資產。
> * 使用查詢字串來控制快取的版本。
> * 使用 CDN 端點的自訂網域。

在下列文章中了解如何將 CDN 效能最佳化：

> [!div class="nextstepaction"]
> [在 Azure CDN 中壓縮檔案以改善效能](../cdn/cdn-improve-performance.md)

> [!div class="nextstepaction"]
> [在 Azure CDN 端點上預先載入資產](../cdn/cdn-preload-endpoint.md)
