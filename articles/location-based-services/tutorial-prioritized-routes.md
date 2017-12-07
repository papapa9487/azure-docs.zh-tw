---
title: "使用 Azure LBS 的多個路線 | Microsoft Docs"
description: "使用 Azure LBS 尋找不同行進模式的路線"
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
ms.openlocfilehash: 3631bab8e5cb505689e92d2862c6863bcd56404d
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>使用 Azure LBS 尋找不同行進模式的路線

本教學課程說明如何使用 Azure LBS 帳戶和路線規劃服務 SDK 來尋找景點路線 (依行進模式設定優先順序)。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 設定路線規劃服務查詢
> * 轉譯依行進模式設定優先順序的路線

## <a name="prerequisites"></a>必要條件

在繼續之前，請務必[建立 Azure LBS 帳戶](./tutorial-search-location.md#createaccount)，並[取得帳戶的訂用帳戶金鑰](./tutorial-search-location.md#getkey)。 您也可以如[使用 Azure LBS 來搜尋附近景點](./tutorial-search-location.md)教學課程所述，觀察該如何使用地圖控制項和搜尋服務 API，以及如[使用 Azure LBS 來尋找景點的路線](./tutorial-route-location.md)教學課程所述，了解路線規劃服務 API 的基本使用方式。


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>設定路線規劃服務查詢

請使用下列步驟，建立內嵌位置服務地圖控制項 API 的靜態 HTML 網頁。 

1. 在您的本機機器上建立新檔案，並將其命名為 **MapTruckRoute.html**。 
2. 在檔案中新增下列 HTML 元件：

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
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
    請注意，HTML 標頭會內嵌 CSS 的資源位置和 Azure LBS 程式庫的 JavaScript 檔案。 也請注意，新增至 HTML 主體的 script 區段，其中包含內嵌的 JavaScript 程式碼，以便存取 Azure 地圖控制項 API。
3. 在 HTML 檔案的 script 區塊中新增下列 JavaScript 程式碼。 將預留位置 <insert-key> 替換為位置服務帳戶的主索引鍵。

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    **atlas.Map** 是 Azure 地圖控制項 API 的元件，可供控制視覺化互動式網路地圖。

4. 在 script 區塊中新增下列 JavaScript 程式碼，以將交通流量顯示新增到地圖：

    ```HTML
            // Add Traffic Flow to the Map
            map.setTraffic({
                flow: "relative"
            });
    ```
    此程式碼將交通流量設定為 `relative`，這是相對於自由流量的道路速度。 您也可以將其設定為道路的 `absolute` 速度，或設定為 `relative-delay`，以顯示不同於自由流量的相對速度。 

5. 新增下列 JavaScript 程式碼，以建立路線的起點和終點圖釘：

    ```HTML
            // Create the GeoJSON objects which represent the start and end point of the route
            var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
            var startPin = new atlas.data.Feature(startPoint, {
                title: "Fabrikam, Inc.",
                icon: "pin-round-blue"
            });

            var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
            var destinationPin = new atlas.data.Feature(destinationPoint, {
                title: "Microsoft",
                icon: "pin-blue"
            });
    ```
    此程式碼會建立兩個 [GeoJSON 物件](https://en.wikipedia.org/wiki/GeoJSON)，代表路線的起點和終點。 

6. 新增下列 JavaScript 程式碼，將 *linestrings* 的圖層新增至地圖控制項，進而根據運輸模式 (例如「汽車」和「卡車」) 顯示路線。

    ```HTML
            // Place route layers on the map
            var carRouteLayerName = "car-route";
            map.addLinestrings([], {
                name: carRouteLayerName,
                color: "#B76DAB",
                width: 5,
                cap: "round",
                join: "round",
                before: "labels"
            });
    
            var truckRouteLayerName = "truck-route";
            map.addLinestrings([], {
                name: truckRouteLayerName,
                color: "#2272B9",
                width: 9,
                cap: "round",
                join: "round",
                before: carRouteLayerName
            });
    ```

7. 新增下列 JavaScript 程式碼，以在地圖中新增起點和終點：

    ```HTML
            // Fit the map window to the bounding box defined by the start and destination points
            var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 100
            });

            // Add pins to the map for the start and end point of the route
            map.addPins([startPin, destinationPin], {
                name: "route-pins",
                textFont: "SegoeUi-Regular",
                textOffset: [0, -20]
            });
    ``` 
    **map.setCameraBounds** API 會根據起點和終點座標來調整地圖視窗。 **map.addPins** API 會在地圖控制項中以視覺化元件的形式新增景點。

8. 在您的機器上儲存 **MapTruckRoute.html** 檔案。 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>轉譯依行進模式設定優先順序的路線

本節說明如何使用 Azure LBS 的路線規劃服務 API，根據運輸模式，尋找從給定起點到目的地的多條路線。 路線規劃服務會提供 API 來規劃兩個位置之間最快速、最短或最環保的路線，並將即時路況考慮進去。 它也可讓使用者使用 Azure 廣泛的歷史路況資料庫，並預測任何日期和時間的路線時間，以規劃日後的路線。 

1. 開啟上一節建立的 **MapTruckRoute.html** 檔案，並在 script 區塊中新增下列 JavaScript 程式碼，以使用路線規劃服務取得卡車的路線。

    ```HTML
            // Perform a request to the route service and draw the resulting truck route on the map
            var xhttpTruck = new XMLHttpRequest();
            xhttpTruck.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200) {
                    var response = JSON.parse(this.responseText);

                    var route = response.routes[0];
                    var routeCoordinates = [];
                    for (var leg of route.legs) {
                        var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                        routeCoordinates = routeCoordinates.concat(legCoordinates);
                    }

                    var routeLinestring = new atlas.data.LineString(routeCoordinates);
                    map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                        name: truckRouteLayerName
                    });
                }
            };

            var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
            truckRouteUrl += "&api-version=1.0";
            truckRouteUrl += "&subscription-key=" + subscriptionKey;
            truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
            truckRouteUrl += "&travelMode=truck";
            truckRouteUrl += "&vehicleWidth=2";
            truckRouteUrl += "&vehicleHeight=2";
            truckRouteUrl += "&vehicleLength=5";
            truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

            xhttpTruck.open("GET", truckRouteUrl, true);
            xhttpTruck.send();
    ```
    此程式碼片段會建立 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，並新增事件處理常式以剖析傳入的回應。 若是成功的回應，它會建立所傳回路線的座標陣列，並將它新增至地圖的 `truckRouteLayerName` 圖層。 
    
    此程式碼片段也會將查詢傳送至路線規劃服務，針對您帳戶的訂用帳戶金鑰，取得指定起點和終點的路線。 下列選用參數用來表示重型卡車的路線：- `travelMode=truck` 參數會將行進模式指定為「卡車」。 其他支援的行進模式為「計程車」、「巴士」、「箱形車」、「摩托車」 和預設的「汽車」。 
        - `vehicleWidth`、`vehicleHeight` 和 `vehicleLength` 參數可指定車輛的尺寸 (以公尺為單位)，而且只會針對行進模式「卡車」進行考量。 
        - `vehicleLoadType` 會將貨物歸類為危險，並限制在某些道路上運送。 這目前也只會針對「卡車」模式進行考量。 

2. 新增下列 JavaScript 程式碼，以使用路線規劃服務取得汽車的路線：

    ```HTML
            // Perform a request to the route service and draw the resulting car route on the map
            var xhttpCar = new XMLHttpRequest();
            xhttpCar.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200) {
                    var response = JSON.parse(this.responseText);

                    var route = response.routes[0];
                    var routeCoordinates = [];
                    for (var leg of route.legs) {
                        var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                        routeCoordinates = routeCoordinates.concat(legCoordinates);
                    }

                    var routeLinestring = new atlas.data.LineString(routeCoordinates);
                    map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                        name: carRouteLayerName
                    });
                }
            };

            var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
            carRouteUrl += "&api-version=1.0";
            carRouteUrl += "&subscription-key=" + subscriptionKey;
            carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

            xhttpCar.open("GET", carRouteUrl, true);
            xhttpCar.send();
    ```
    此程式碼片段會建立另一個 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，並新增事件處理常式以剖析傳入的回應。 若是成功的回應，它會建立所傳回路線的座標陣列，並將它新增至地圖的 `carRouteLayerName` 圖層。 
    
    此程式碼片段也會將查詢傳送至路線規劃服務，針對您帳戶的訂用帳戶金鑰，取得指定起點和終點的路線。 由於未使用任何其他參數，所以會傳回預設行進模式「汽車」的路線。 

3. 在本機儲存 **MapTruckRoute.html** 檔案，然後在您選擇的網頁瀏覽器中開啟它，並觀察結果。 如果您成功連線到位置服務的 API，您應該會看到類似下面的地圖。 

    ![使用 Azure 路線規劃服務設定優先順序的路線](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    請注意，卡車路線為藍色，而汽車路線則為紫色。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定路線規劃服務查詢
> * 轉譯依行進模式設定優先順序的路線

繼續閱讀**概念**和**作法**文章，深入了解 Azure LBS SDK。 
