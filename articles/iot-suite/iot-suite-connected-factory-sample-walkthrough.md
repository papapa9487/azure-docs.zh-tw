---
title: "連線處理站 Azure IoT 套件解決方案逐步解說 | Microsoft Docs"
description: "說明 Azure IoT 預先設定的解決方案連線處理站及其架構。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: dobett
ms.openlocfilehash: 517e908a744734139ed0aeee314a4f3b9eda86cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>連線處理站預先設定的解決方案逐步解說

IoT 套件連線處理站[預先設定的解決方案][lnk-preconfigured-solutions]是端對端企業解決方案的實作︰

* 連線至模擬處理站生產線中執行 OPC UA 的模擬工業裝置，和實際的 OPC UA 伺服器裝置。 如需 OPC UA 的詳細資訊，請參閱[連線的處理站常見問題集](iot-suite-faq-cf.md)。
* 顯示作業 KPI 和這些裝置與生產線的 OEE。
* 示範如何將以雲端為基礎的應用程式用來與 OPC UA 伺服器系統進行互動。
* 可讓您連線到自己的 OPC UA 伺服器裝置。
* 可讓您瀏覽並修改 OPC UA 伺服器資料。
* 與 Azure Time Series Insights (TSI) 服務進行整合，可提供 OPC UA 伺服器資料的自訂檢視。

您可以將此解決方案做為自己實作的起點，並加以[自訂][lnk-customize]以符合自己特有的商務需求。

本文將逐步介紹連線處理站解決方案的一些重要元素，讓您了解它的運作方式。 這項知識能協助您︰

* 在解決方案中進行疑難排解。
* 規劃如何自訂解決方案以滿足您的特定需求。
* 設計使用 Azure 服務的 IoT 解決方案。

如需詳細資訊，請參閱[連線的處理站常見問題集](iot-suite-faq-cf.md)。

## <a name="logical-architecture"></a>邏輯架構

下圖概述預先設定解決方案的邏輯元件：

![連線處理站的邏輯架構][connected-factory-logical]

## <a name="communication-patterns"></a>通訊模式

此解決方案會使用 [OPC UA Pub/Sub 規格](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/)，以 JSON 格式將 OPC UA 遙測資料傳送到 IoT 中樞。 此解決方案會就此目的使用 [OPC 發行者](https://github.com/Azure/iot-edge-opc-publisher) IoT Edge 模組。

此解決方案也有整合至 Web 應用程式的 OPC UA 用戶端，該應用程式可以建立與內部部署 OPC UA 伺服器的連線。 用戶端會使用 [reverse-proxy](https://wikipedia.org/wiki/Reverse_proxy) 並接受 IoT 中樞的協助進行連線，而不需要內部部署防火牆中的開啟連接埠。 此通訊模式稱為[服務輔助通訊](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)。 此解決方案會就此目的使用 [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy/) IoT Edge 模組。


## <a name="simulation"></a>模擬

模擬工作站與模擬製造執行系統 (MES) 會構成處理站生產線。 模擬裝置和 OPC 發行者模組是根據 OPC Foundation 所發佈的 [OPC UA .NET 標準][lnk-OPC-UA-NET-Standard]。

OPC Proxy 和 OPC 發行者會根據 [Azure IoT Edge][lnk-Azure-IoT-Gateway] 實作為模組。 每個模擬產品線都已連結指定的閘道。

所有模擬元件都是在裝載於 Azure Linux VM 的 Docker 容器中執行。 依預設，會將模擬設定為執行八個模擬生產線。

## <a name="simulated-production-line"></a>模擬生產線

生產線可製造零件。 它是由不同的工作站所組成︰組裝工作站、測試工作站和包裝工作站。

模擬所執行及更新的資料是透過 OPC UA 節點公開。 所有模擬生產線工作站都是透過 OPC UA 由 MES 進行協調。

## <a name="simulated-manufacturing-execution-system"></a>模擬製造執行系統

MES 會透過 OPC UA 來監視生產線中的每個工作站，從而偵測工作站的狀態變更。 它會呼叫 OPC UA 方法來控制工作站，並將產品從一個工作站傳遞到下一個工作站，直到完成為止。

## <a name="gateway-opc-publisher-module"></a>閘道 OPC 發行者模組

OPC 發行者模組會連線至工作站 OPC UA 伺服器，並訂閱所要發佈的 OPC 節點。 此模組會將節點資料轉換成 JSON 格式、將它加密，並傳送至 IoT 中樞作為 OPC UA Pub/Sub 訊息。

OPC 發行者模組只需要輸出的 https 連接埠 (443)，並且可以使用現有的企業基礎結構。

## <a name="gateway-opc-proxy-module"></a>閘道 OPC Proxy 模組

閘道 OPC UA Proxy 模組可建立二進位 OPC UA 命令與控制訊息的通道，且只需要輸出的 https 連接埠 (443)。 它可以使用現有的企業基礎結構，包括 Web Proxy。

它會使用 IoT 中樞裝置方法，將應用程式層中的已封包 TCP/IP 資料進行傳輸，因此可使用 SSL/TLS 來確保端點信任、資料加密及完整性。

透過 Proxy 本身轉送的 OPC UA 二進位通訊協定使用的是 UA 驗證及加密。

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

閘道 OPC 發行者模組會訂閱 OPC UA 伺服器節點，以偵測資料值中的變更。 如果在其中一個節點中偵測到資料變更，此模組就會將訊息傳送至 Azure IoT 中樞。

IoT 中樞會向 Azure TSI 提供事件來源。 根據附加至訊息的時間戳記，TSI 會將資料儲存 30 天。 這項資料包括︰

* OPC UA ApplicationUri
* OPC UA NodeId
* 節點的值
* 來源時間戳記
* OPC UA DisplayName

目前，TSI 不允許客戶自訂想要保留資料的時間。

TSI 會使用 SearchSpan (Time.From、Time.To) 針對節點資料進行查詢，並依 OPC UA ApplicationUri 或 OPC UA NodeId 或 OPC UA DisplayName 進行彙總。

若要擷取 OEE 和 KPI 量測計的資料，以及時間序列圖表，則會依事件、總和、平均值、最小值和最大值的計數將資料進行彙總。

會使用不同的程序來建立時間序列。 OEE 和 KPI 是由工作站基底資料所計算出來，並針對應用程式中的拓撲 (生產線、處理站、企業) 進行反昇。

此外，每當顯示的時間範圍就緒時，就會在應用程式中計算 OEE 和 KPI 拓撲的時間序列。 例如，每個小時都會更新 [日] 檢視。

節點資料的時間序列檢視會直接來自於使用時間範圍彙總的 TSI。

## <a name="iot-hub"></a>IoT 中樞
[IoT 中樞][lnk-IoT Hub]會接收從 OPC 發行者模組傳送至雲端的資料，並提供給 Azure TSI 服務。 

解決方案中的 IoT 中樞也可︰
- 會將儲存所有 OPC 發行者模組和所有 OPC Proxy 模組的身分識別登錄加以維護。
- 用來作為 OPC Proxy 模組雙向通訊的傳輸通道。

## <a name="azure-storage"></a>Azure 儲存體
解決方案會使用 Azure Blob 儲存體作為 VM 的磁碟儲存體，及用來儲存部署資料。

## <a name="web-app"></a>Web 應用程式
部署作為預先設定解決方案一部分的 Web 應用程式中，包含整合式的 OPC UA 用戶端、警示處理和遙測視覺效果。

## <a name="next-steps"></a>後續步驟

您可以繼續閱讀下列文章，了解如何開始使用 IoT 套件︰

* [azureiotsuite.com 網站的權限][lnk-permissions]
* [在 Windows 或 Linux 上部署連線處理站預先設定解決方案的閘道](iot-suite-connected-factory-gateway-deployment.md)

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-permissions.md
