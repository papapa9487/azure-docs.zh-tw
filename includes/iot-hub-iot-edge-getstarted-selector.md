> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

本文提供 [Hello World 範例程式碼][lnk-helloworld-sample]的詳細逐步解說，來說明 [Azure IoT Edge][lnk-iot-edge] 架構的基本元件。 此範例使用 Azure IoT Edge，建置每五秒即將 "hello world" 訊息記錄到檔案的簡單閘道。

本逐步解說涵蓋下列項目：

* **Hello World 範例架構**︰描述 [Azure IoT Edge 架構概念][lnk-edge-concepts]套用到 Hello World 範例的方式，以及元件如何彼此搭配運作。
* **如何建置範例**︰建置範例所需的步驟。
* **如何執行範例**︰執行範例所需的步驟。 
* **典型輸出**：執行範例時所預期的輸出範例。
* **程式碼片段**︰程式碼片段集合，顯示 Hello World 範例如何實作重要 IoT Edge 閘道元件。


## <a name="hello-world-sample-architecture"></a>Hello World 範例架構
Hello World 範例說明上節中所述的概念。 Hello World 範例會實作管線由兩個 IoT Edge 模組所構成的 IoT Edge 閘道︰

* 「Hello World」  模組每五秒會建立一則訊息，並將其傳遞到 Logger 模組。
* 「Logger」  模組會將所收到的訊息寫入檔案。

![使用 Azure IoT Edge 所建置之 Hello World 範例的架構][4]

如上節所述，Hello World 模組不會每五秒將訊息直接傳遞到 Logger 模組。 而是每五秒將訊息發佈到訊息代理程式。

Logger 模組會接收來自訊息代理程式的訊息並對其採取動作，以將訊息的內容寫入檔案。

Logger 模組只會取用來自訊息代理程式的訊息，永遠不會對訊息代理程式發佈新訊息。

![訊息代理程式如何在 Azure IoT Edge 的模組之間路由傳送訊息][5]

上圖顯示 Hello World 範例的架構，以及實作[儲存機制][lnk-iot-edge]中範例不同部分之來源檔案的相對路徑。 自行探索程式碼，或使用本文中的程式碼片段作為指南。

<!-- Images -->
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md