---
title: "使用 Azure LBS 搜尋 | Microsoft Docs"
description: "使用 Azure LBS 來搜尋附近景點"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 15afdead60d4c1ee3c7e3c079d43e0651b262ec8
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="search-nearby-point-of-interest-using-azure-location-based-services"></a>使用 Azure LBS 來搜尋附近景點

本教學課程說明如何使用 Azure LBS 來設定帳戶，然後使用所提供的 API 來搜尋景點。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用 Azure LBS 建立帳戶
> * 取得您帳戶的訂用帳戶金鑰
> * 使用地圖控制項 API 建立新網頁
> * 使用搜尋服務來尋找附近景點

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

# <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com)。

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-location-based-services"></a>使用 Azure LBS 建立帳戶

依照下列步驟來建立新的位置服務帳戶。

1. 按一下 [Azure 入口網站](https://portal.azure.com)左上角的 [建立資源]。
2. 在 [搜尋 Marketplace] 方塊中，輸入**位置服務**。
3. 按一下 [結果] 中的 [位置服務 (預覽)]。 按一下地圖下方顯示的 [建立] 按鈕。 
4. 在 [建立位置服務帳戶] 頁面上輸入下列值：
    - 新帳戶的 [名稱]。 
    - 您想要使用於此帳戶的 [訂用帳戶]。
    - 此帳戶的 [資源群組] 名稱。 您可以選擇 [建立新的] 或 [使用現有的] 資源群組。
    - 選取 [資源群組位置]。
    - 閱讀 [預覽條款] 並勾選核取方塊以接受條款。 
    - 最後，按一下 [建立]  按鈕。
   
    ![在入口網站中建立位置服務帳戶](./media/tutorial-search-location/create-lbs-account.png)


<a id="getkey"></a>

## <a name="get-the-subscription-key-for-your-account"></a>取得您帳戶的訂用帳戶金鑰

成功建立位置服務帳戶之後，請依照下列步驟，將它連結到其地圖搜尋 API：

1. 在入口網站中開啟您的位置服務帳戶。
2. 瀏覽至您帳戶的 [設定]，然後選取 [索引鍵]。
3. 將 [主索引鍵] 複製到剪貼簿。 將它儲存在本機，以使用於先前的步驟。 

    ![在入口網站中取得主索引鍵](./media/tutorial-search-location/lbs-get-key.png)


<a id="createmap"></a>

## <a name="create-new-web-page-using-azure-map-control-api"></a>使用 Azure 地圖控制項 API 來建立新網頁
Azure 地圖控制項 API 是很方便的用戶端程式庫，可讓您輕鬆地將 Azure LBS 整合到您的 Web 應用程式中。 它會隱藏裸機 REST 服務呼叫的複雜度，並使用可設定樣式和可自訂的元件提升生產力。 下列步驟顯示如何建立內嵌位置服務地圖控制項 API 的靜態 HTML 網頁。 

1. 在您的本機機器上建立新檔案，並將其命名為 **MapSearch.html**。 
2. 在檔案中新增下列 HTML 元件：

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>

        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body>
        <div id="map"></div>
        <script>
        // Embed Map Control JavaScript code here
        </script>

    </body>

    </html>
    ``` 
    請注意，HTML 標頭包含 Azure 地圖控制項程式庫所裝載的 CSS 和 JavaScript 資源檔案。 請注意新增至 HTML 檔案 body 的 script 區段。 此區段會包含內嵌的 JavaScript 程式碼，以便存取 Azure LBS 的 API。
 
3.  在 HTML 檔案的 script 區塊中新增下列 JavaScript 程式碼。 將預留位置 <insert-key> 替換為位置服務帳戶的主索引鍵。 

    ```HTML/JavaScript
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    此區段會針對您的訂用帳戶金鑰，初始化地圖控制項 API。 **Atlas** 是包含 Azure 地圖控制項 API 和相關視覺效果元件的命名空間。 **atlas.Map** 可供控制視覺化互動式網路地圖。 在瀏覽器中開啟 HTML 網頁，您可看到地圖的外觀。 

4. 在 script 區塊中新增下列 JavaScript 程式碼，將搜尋圖釘圖層新增到地圖控制項：

    ```HTML/JavaScript
            // Initialize the pin layer for search results to the map
            var searchLayerName = "search-results";
            map.addPins([], {
                name: searchLayerName,
                cluster: false,
                icon: "pin-round-darkblue"
            });
    ```

5. 在您的電腦上儲存此檔案。 


<a id="usesearch"></a>

## <a name="use-search-service-to-find-nearby-point-of-interest"></a>使用搜尋服務來尋找附近景點

本節說明如何使用 Azure LBS 的搜尋服務 API，在您的地圖上尋找景點。 這是針對開發人員所設計的 RESTful API，用於搜尋地址、景點及其他地理資訊。 搜尋服務會將經緯度資訊指派給指定的地址。 

1. 開啟上一節建立的 **MapSearch.html** 檔案，並在 script 區塊中新增下列 JavaScript 程式碼，以繪製出搜尋服務所建議的路線。 
    ```HTML/JavaScript
            // Perform a request to the search service and create a pin on the map for each result
            var xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function () {
                var searchPins = [];

                if (this.readyState === 4 && this.status === 200) {
                    var response = JSON.parse(this.responseText);

                    var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

                    searchPins = poiResults.map((poiResult) => {
                        var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                        return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                            name: poiResult.poi.name,
                            address: poiResult.address.freeformAddress,
                            position: poiResult.position.lat + ", " + poiResult.position.lon
                        });
                    });

                    map.addPins(searchPins, {
                        name: searchLayerName
                    });

                    var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
                    var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

                    var swLon = Math.min.apply(null, lons);
                    var swLat = Math.min.apply(null, lats);
                    var neLon = Math.max.apply(null, lons);
                    var neLat = Math.max.apply(null, lats);

                    map.setCameraBounds({
                        bounds: [swLon, swLat, neLon, neLat],
                        padding: 50
                    });
                }
            };
    ```
    此程式碼片段會建立 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，並新增事件處理常式以剖析傳入的回應。 對於成功的回應，它會收集 `searchPins` 變數中的地址、名稱、針對每個位置傳回的經緯度資訊。 最後，它會將此位置點集合新增至 `map` 控制項作為圖釘。 

2. 在 script 區塊中新增下列程式碼，將 XMLHttpRequest 傳送至 Azure LBS 的搜尋服務：

    ```HTML/JavaScript
            var url = "https://atlas.microsoft.com/search/fuzzy/json?";
            url += "&api-version=1.0";
            url += "&query=gasoline%20station";
            url += "&subscription-key=" + subscriptionKey;
            url += "&lat=47.6292";
            url += "&lon=-122.2337";
            url += "&radius=100000"

            xhttp.open("GET", url, true);
            xhttp.send();
    ``` 
    此程式碼片段會使用搜尋服務的基本搜尋 API ，稱之為 [模糊搜尋]。 它可處理輸入的大部分模糊搜尋，並處理地址或 POI 語彙基元的任意組合。 它可在指定的範圍內，針對以經緯度給定的地址，搜尋附近的**加油站**。 它會使用稍早在範例檔案中提供之帳戶的訂用帳戶金鑰，進行位置服務的呼叫。 它會傳回以所找到位置的經緯度配對表示的結果。 在瀏覽器中開啟 HTML 網頁，您可看到搜尋圖釘。 

3. 在 script 區塊中新增下列幾行，以建立搜尋服務所傳回之景點的快顯視窗：

    ```HTML/JavaScript
            // Add a popup to the map which will display some basic information about a search result on hover over a pin
            var popup = new atlas.Popup();
            map.addEventListener("mouseover", searchLayerName, (e) => {
                var popupContentElement = document.createElement("div");
                popupContentElement.style.padding = "5px";

                var popupNameElement = document.createElement("div");
                popupNameElement.innerText = e.features[0].properties.name;
                popupContentElement.appendChild(popupNameElement);

                var popupAddressElement = document.createElement("div");
                popupAddressElement.innerText = e.features[0].properties.address;
                popupContentElement.appendChild(popupAddressElement);

                var popupPositionElement = document.createElement("div");
                popupPositionElement.innerText = e.features[0].properties.position;
                popupContentElement.appendChild(popupPositionElement);

                popup.setPopupOptions({
                    position: e.features[0].geometry.coordinates,
                    content: popupContentElement
                });

                popup.open(map);
            });
    ```
    API **atlas.Popup** 可提供錨定在地圖上所需位置的資訊視窗。 此程式碼片段會設定快顯視窗的內容和位置，以及將事件接聽程式新增至 `map` 控制項，並等待「滑鼠」移到快顯視窗上。 

4. 儲存檔案，然後在您所選的網頁瀏覽器中開啟 **MapSearch.html** 檔案並觀察結果。 此時，當您將滑鼠停留在任何顯示的搜尋圖釘上時，瀏覽器中的地圖會顯示資訊快顯視窗，如下所示。 

    ![Azure 地圖控制項和搜尋服務](./media/tutorial-search-location/lbs-map-search.png)


## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 Azure LBS 建立帳戶
> * 取得您帳戶的訂用帳戶金鑰
> * 使用地圖控制項 API 建立新網頁
> * 使用搜尋服務來尋找附近景點

繼續進行[使用 Azure LBS 來尋找景點的路線](./tutorial-route-location.md)教學課程，以了解如何使用 Azure LBS 來尋找您的景點路線。 
