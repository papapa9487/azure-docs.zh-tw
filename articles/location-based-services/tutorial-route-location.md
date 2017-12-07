---
title: "使用 Azure LBS 尋找路線 | Microsoft Docs"
description: "使用 Azure LBS 尋找景點路線"
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
ms.openlocfilehash: 0f784e8ecd8fc94c12df1a819055718e06547b6b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>使用 Azure LBS 尋找景點路線

本教學課程說明如何使用 Azure LBS 帳戶和路線規劃服務 SDK，來尋找景點路線。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 取得地址座標
> * 查詢路線規劃服務，以了解如何前往景點

## <a name="prerequisites"></a>必要條件

在繼續之前，請務必[建立 Azure LBS 帳戶](./tutorial-search-location.md#createaccount)，並[取得帳戶的訂用帳戶金鑰](./tutorial-search-location.md#getkey)。 您也可以如[使用 Azure LBS 搜尋附近景點](./tutorial-search-location.md)教學課程所述，觀察該如何使用地圖控制項和搜尋服務 API。


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>取得地址座標

請使用下列步驟，建立內嵌位置服務地圖控制項 API 的靜態 HTML 網頁。 

1. 在您的本機機器上建立新檔案，並將其命名為 **MapRoute.html**。 
2. 在檔案中新增下列 HTML 元件：

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
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
    請注意，HTML 標頭會內嵌 CSS 的資源位置和 Azure LBS 程式庫的 JavaScript 檔案。 也請注意 HTML 檔案主體中的 script 區段，其中將會包含內嵌的 JavaScript 程式碼，以便存取 Azure LBS 的 API。

3. 在 HTML 檔案的 script 區塊中新增下列 JavaScript 程式碼。 將預留位置 <insert-key> 替換為位置服務帳戶的主索引鍵。

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    **atlas.Map** 是 Azure 地圖控制項 API 的元件，會提供視覺效果控制項和互動式網路地圖。

4. 在 script 區塊中新增下列 JavaScript 程式碼。 這會在地圖控制項新增 linestrings 層，以顯示路線：

    ```HTML
            // Initialize the linestring layer for routes on the map
            var routeLinesLayerName = "routes";
            map.addLinestrings([], {
                name: routeLinesLayerName,
                color: "#2272B9",
                width: 5,
                cap: "round",
                join: "round",
                before: "labels"
            });
    ```

5. 新增下列 JavaScript 程式碼以建立路線的起點和終點：

    ```HTML
            // Create the GeoJSON objects which represent the start and end point of the route
            var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
            var startPin = new atlas.data.Feature(startPoint, {
                title: "Microsoft",
                icon: "pin-round-blue"
            });

            var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
            var destinationPin = new atlas.data.Feature(destinationPoint, {
                title: "Contoso Oil & Gas",
                icon: "pin-blue"
            });
    ```
    此程式碼會建立兩個 [GeoJSON 物件](https://en.wikipedia.org/wiki/GeoJSON)，以代表路線的起點和終點。 終點是上一個教學課程[使用 Azure LBS 搜尋附近景點](./tutorial-search-location.md)中所搜尋到之其中一個*加油站*的經緯度組合。

6. 新增下列 JavaScript 程式碼，以在地圖中新增起點和終點的標示：

    ```HTML
            // Fit the map window to the bounding box defined by the start and destination points
            var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });

            // Add pins to the map for the start and end point of the route
            map.addPins([startPin, destinationPin], {
                name: "route-pins",
                textFont: "SegoeUi-Regular",
                textOffset: [0, -20]
            });
    ``` 
    **map.setCameraBounds** API 會根據起點和終點座標來調整地圖視窗。 **map.addPins** API 會在地圖控制項中以視覺化元件的形式新增景點。

7. 在您的機器上儲存 **MapRoute.html** 檔案。 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>查詢路線規劃服務，以了解如何前往景點

本節說明如何使用 Azure LBS 的路線規劃服務 API，尋找從給定起點到終點的路線。 路線規劃服務會提供 API 來規劃兩個位置之間最快速、最短或最環保的路線，並將即時路況考慮進去。 它也可讓使用者使用 Azure 廣泛的歷史路況資料庫，並預測任何日期和時間的路線時間，以規劃日後的路線。 

1. 開啟上一節建立的 **MapRoute.html** 檔案，並在 script 區塊新增下列 JavaScript 程式碼，以繪製出路線規劃服務所建議的路線。

    ```HTML
            // Perform a request to the route service and draw the resulting route on the map
            var xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200) {
                    var response = JSON.parse(xhttp.responseText);

                    var route = response.routes[0];
                    var routeCoordinates = [];
                    for (var leg of route.legs) {
                        var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                        routeCoordinates = routeCoordinates.concat(legCoordinates);
                    }

                    var routeLinestring = new atlas.data.LineString(routeCoordinates);
                    map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
                }
            };
    ```
    此程式碼片段會建立 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，並新增事件處理常式以剖析傳入的回應。 若是成功的回應，它便會以所傳回之第一條路線的每一段建構座標陣列。 然後，它會將此路線的這組座標新增到地圖的 linestrings 層。

2. 在 script 區塊中新增下列程式碼，將 XMLHttpRequest 傳送至 Azure LBS 的路線規劃服務：

    ```HTML
            var url = "https://atlas.microsoft.com/route/directions/json?";
            url += "&api-version=1.0";
            url += "&subscription-key=" + subscriptionKey;
            url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    
            xhttp.open("GET", url, true);
            xhttp.send();
    ```
    上述要求會顯示所需參數 (也就是您帳戶的訂用帳戶金鑰)，以及起點和終點的座標 (依給定順序)。 

3. 在本機儲存 **MapRoute.html** 檔案，然後在您選擇的網頁瀏覽器中開啟它，並觀察結果。 如果您成功連線到位置服務的 API，您應該會看到類似下面的地圖。 

    ![Azure 地圖控制項和路線規劃服務](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 取得地址座標
> * 查詢路線規劃服務，以了解如何前往景點

繼續進行[使用 Azure LBS 尋找不同旅行模式的路線](./tutorial-prioritized-routes.md)教學課程，以了解如何使用 Azure LBS，根據交通模式排列景點路線的先後順序。 
