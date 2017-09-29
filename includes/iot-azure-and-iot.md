
# <a name="azure-and-internet-of-things"></a>Azure 和物聯網

歡迎使用 Microsoft Azure 與物聯網 (IoT)。 本文將介紹您可能會使用 Azure 服務來部署之 IoT 解決方案的一般特性。 IoT 解決方案需要大量裝置與解決方案後端之間有安全、雙向的通訊。 解決方案後端可能會使用自動化的預測性分析，以從裝置到雲端的事件串流中發掘出有用見解。

[Azure IoT 中樞][lnk-iot-hub]是使用 Azure 服務的任何 IoT 解決方案的重要建置區塊。 IoT 中樞是一項完全受管理的服務，可在數百萬個 IoT 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。 

[Azure IoT 套件][lnk-iot-suite]可針對特定的 IoT 案例端對端地完整實作這個架構。 例如：

* *遠端監視*解決方案可讓您監視裝置 (例如自動販賣機) 的狀態。
* 「預測性維護」解決方案可協助您預料到裝置 (例如遠端抽水站的抽水機) 的維護需求，並避免發生意外停機。
* 「連線處理站」解決方案可協助您連線並監視您的工業裝置。

## <a name="iot-solution-architecture"></a>IoT 解決方案架構

下圖顯示典型的 IoT 解決方案架構。 此圖不會包含任何特定 Azure 服務的名稱，但會說明一般 IoT 解決方案架構中的重要元素。 在此架構中，IoT 裝置會收集其傳送到雲端閘道的資料。 雲端閘道可讓資料供其他後端服務處理。 解決方案後端會透過儀表板或報告將資料傳遞至企業營運應用程式或操作員。

![IoT 解決方案架構][img-solution-architecture]

> [!NOTE]
> 如需 IoT 架構的深入討論，請參閱 [Microsoft Azure IoT 參考架構][lnk-refarch]。

### <a name="device-connectivity"></a>裝置連線能力

在此 IoT 解決方案中，裝置會將遙測 (例如幫浦站的感應器讀數) 傳送給雲端端點，以進行儲存和處理。 在預測性維護案例中，解決方案後端可能會使用感應器資料流，來判斷特定幫浦何時需要維護。 裝置也可以透過讀取來自雲端端點的訊息，以接收和回應雲端到裝置訊息。 比方說，在預測性維護案例中，解決方案後端可能會傳送訊息給幫浦站的其他幫浦，以在維護應開始之前先重新路由流量。 此程序可確保維護工程師立即開始修正問題。

IoT 專案所面臨的其中一個最大挑戰，就是如何可靠且安全地將裝置連線至解決方案後端。 相較於其他用戶端 (例如瀏覽器和行動應用程式)，IoT 裝置有不同的特性。 IoT 裝置：

* 通常是無人操作的嵌入式系統。
* 可以部署於實體存取成本昂貴的遠端位置。
* 可能只能透過解決方案後端來存取。 沒有其他方式可與裝置互動。
* 能力和/或處理資源可能都有限。
* 網路連線能力可能不穩定、緩慢或昂貴。
* 可能需要使用專屬、自訂或業界特定的應用程式通訊協定。
* 可以使用一組大型常見的硬體和軟體平台來建立。

除了上述需求之外，任何 IoT 解決方案也必須提供調整、安全性和可靠性。 使用傳統技術 (例如 Web 容器和傳訊代理程式) 實作連線需求的結果是困難且耗時的。 Azure IoT 中樞和 Azure IoT 裝置 SDK 讓您更輕鬆地實作符合其需求的解決方案。

裝置可以直接與雲端閘道端點通訊。 如果裝置無法使用任何雲端閘道支援的通訊協定，則可以透過中繼閘道連線。 例如，[Azure IoT 通訊協定閘道][lnk-protocol-gateway]可以在裝置不能使用 IoT 中樞支援的任何通訊協定時，執行通訊協定轉譯。

### <a name="data-processing-and-analytics"></a>資料處理和分析

在雲端中，IoT 解決方案後端是大部分資料處理發生之處。 IoT 解決方案後端：

* 接收大規模來自裝置的遙測資料，並判斷如何處理與儲存該資料。 
* 可能讓您由雲端將命令傳送到特定裝置。
* 提供可讓您佈建裝置並且控制哪些裝置可以連線到您基礎結構的裝置註冊功能。
* 可讓您追蹤您的裝置狀態並監視其活動。

在預測性維護案例中，解決方案後端會儲存歷史遙測資料。 解決方案後端可以使用這項資料來識別可指出特定抽水機已達維護時間的模式。

IoT 解決方案可以包含自動意見反應迴圈。 例如，解決方案後端中的分析模組可以從遙測資料中識別出特定裝置的溫度超出一般作業水準。 接著，解決方案可以傳送命令給該裝置，指示它採取矯正動作。

### <a name="presentation-and-business-connectivity"></a>簡報及商務連線

簡報及商務連線層可讓終端使用者與 IoT 解決方案及裝置互動。 其讓使用者能夠檢視和分析從他們的裝置所收集的資料。 這些檢視可以採用儀表板或報表的格式，以顯示歷程記錄資料及/或接近即時的資料。 例如，操作員可確認特定幫浦站的狀態，並查看系統產生的任何警示。 此層也可整合 IoT 解決方案與現有企業營運應用程式，以連結企業商務程序或工作流程。 例如，預測性維護解決方案可與排程系統整合。 當解決方案識別出需要維護的幫浦時，排程系統就會預約工程師造訪幫浦台。

![IoT 解決方案儀表板][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
