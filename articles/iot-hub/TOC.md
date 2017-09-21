# 概觀
## [Azure 和 IoT](iot-hub-what-is-azure-iot.md)
## [何謂 Azure IoT 中樞？](iot-hub-what-is-iot-hub.md)
## [裝置管理概觀](iot-hub-device-management-overview.md)

# [開始使用](iot-hub-get-started.md)

## 設定您的裝置
### [模擬電腦上的裝置](iot-hub-get-started-simulated.md)
#### [.NET](iot-hub-csharp-csharp-getstarted.md)
#### [Java](iot-hub-java-java-getstarted.md)
#### [Node.js](iot-hub-node-node-getstarted.md)
#### [Python](iot-hub-python-getstarted.md)

### [使用線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)

### [使用實體裝置](iot-hub-get-started-physical.md)
#### [Raspberry Pi with Python](iot-hub-raspberry-pi-kit-python-get-started.md)
#### [Raspberry Pi with Node.js](iot-hub-raspberry-pi-kit-node-get-started.md)
#### [Raspberry Pi with C](iot-hub-raspberry-pi-kit-c-get-started.md)

#### [MXChip IoT DevKit with Arduino](iot-hub-arduino-iot-devkit-az3166-get-started.md)

#### [Intel Edison with Node.js](iot-hub-intel-edison-kit-node-get-started.md)
#### [Intel Edison with C](iot-hub-intel-edison-kit-c-get-started.md)

#### [Adafruit Feather HUZZAH ESP8266 with Arduino IDE](iot-hub-arduino-huzzah-esp8266-get-started.md)
#### [Sparkfun ESP8266 Thing Dev with Arduino IDE](iot-hub-sparkfun-esp8266-thing-dev-get-started.md)
#### [Adafruit Feather M0 with Arduino IDE](iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md)

#### 使用 IoT 閘道入門套件
##### [將 Intel NUC 設定為閘道](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
##### [將閘道連線至 IoT 中樞](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
##### [使用閘道進行資料轉換](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)
##### [疑難排解](iot-hub-gateway-kit-c-troubleshooting.md)

## 擴充的 IoT 案例
### [透過 iothub-explorer 管理雲端裝置傳訊](iot-hub-explorer-cloud-device-messaging.md)
### [將 IoT 中樞IoT 中樞儲存至 Azure 資料儲存體](iot-hub-store-data-in-azure-table-storage.md)
### [Power BI 中的資料視覺效果](iot-hub-live-data-visualization-in-power-bi.md)
### [Web Apps 的資料視覺效果](iot-hub-live-data-visualization-in-web-apps.md)
### [使用 Azure Machine Learning 進行天氣預測](iot-hub-weather-forecast-machine-learning.md)
### [透過 iothub-explorer進行裝置管理](iot-hub-device-management-iothub-explorer.md)
### [搭配 Logic Apps 進行遠端監視和通知](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

# 作法
## 規劃
### [比較 IoT 中樞與事件中樞](iot-hub-compare-event-hubs.md)
### [調整您的解決方案規模](iot-hub-scaling.md)
### [高可用性和災害復原](iot-hub-ha-dr.md)
### [支援其他通訊協定](iot-hub-protocol-gateway.md)
## [開發](iot-hub-how-to.md)
### [開發人員指南](iot-hub-devguide.md)
#### [裝置對雲端的功能指南](iot-hub-devguide-d2c-guidance.md)
#### [雲端對裝置的功能指南](iot-hub-devguide-c2d-guidance.md)
#### [傳送及接收訊息](iot-hub-devguide-messaging.md)
##### [將裝置到雲端訊息傳送至 IoT 中樞](iot-hub-devguide-messages-d2c.md)
##### [從內建端點讀取裝置對雲端訊息](iot-hub-devguide-messages-read-builtin.md)
##### [使用適用於裝置對雲端訊息的自訂端點和路由規則](iot-hub-devguide-messages-read-custom.md)
##### [從 IoT 中樞傳送雲端到裝置訊息](iot-hub-devguide-messages-c2d.md)
##### [建立和閱讀 IoT 中樞訊息](iot-hub-devguide-messages-construct.md)
##### [選擇通訊協定](iot-hub-devguide-protocols.md)
#### [從裝置上傳檔案](iot-hub-devguide-file-upload.md)
#### [管理裝置身分識別](iot-hub-devguide-identity-registry.md)
#### [控制 IoT 中樞的存取權](iot-hub-devguide-security.md)
#### [了解攣生裝置](iot-hub-devguide-device-twins.md)
#### [叫用裝置上的直接方法](iot-hub-devguide-direct-methods.md)
#### [排程多個裝置上的作業](iot-hub-devguide-jobs.md)
#### [IoT 中樞端點](iot-hub-devguide-endpoints.md)
#### [查詢語言](iot-hub-devguide-query-language.md)
#### [配額和節流](iot-hub-devguide-quotas-throttling.md)
#### [計費範例](iot-hub-devguide-pricing.md)
#### [裝置與服務 SDK](iot-hub-devguide-sdks.md)
#### [MQTT 支援](iot-hub-mqtt-support.md)
#### [詞彙](iot-hub-devguide-glossary.md)
### [使用適用於 C 的 IoT 裝置 SDK](iot-hub-device-sdk-c-intro.md)
#### [使用 IoTHubClient](iot-hub-device-sdk-c-iothubclient.md)
#### [使用序列化程式](iot-hub-device-sdk-c-serializer.md)
### 處理裝置到雲端的訊息
#### [.NET](iot-hub-csharp-csharp-process-d2c.md)
#### [Java](iot-hub-java-java-process-d2c.md)
### 傳送雲端到裝置訊息
#### [.NET](iot-hub-csharp-csharp-c2d.md)
#### [Java](iot-hub-java-java-c2d.md)
#### [Node.js](iot-hub-node-node-c2d.md)
### [從裝置上傳檔案]
#### [.NET](iot-hub-csharp-csharp-file-upload.md)
#### [Java](iot-hub-java-java-file-upload.md)
### 開始使用攣生裝置
#### [Node.js 後端/Node.js 裝置](iot-hub-node-node-twin-getstarted.md)
#### [.NET 後端/Node.js 裝置](iot-hub-csharp-node-twin-getstarted.md)
#### [.NET 後端/.NET 裝置](iot-hub-csharp-csharp-twin-getstarted.md)
#### [Java 後端/Java 裝置](iot-hub-java-java-twin-getstarted.md)
### 使用直接方法
#### [Node.js 後端/Node.js 裝置](iot-hub-node-node-direct-methods.md)
#### [.NET 後端/Node.js 裝置](iot-hub-csharp-node-direct-methods.md)
#### [.NET 後端/.NET 裝置](iot-hub-csharp-csharp-direct-methods.md)
#### [Java 後端/Java 裝置](iot-hub-java-java-direct-methods.md)
### 開始使用裝置管理
#### [Node.js 後端/Node.js 裝置](iot-hub-node-node-device-management-get-started.md)
#### [.NET 後端/Node.js 裝置](iot-hub-csharp-node-device-management-get-started.md)
#### [.NET 後端/.NET 裝置](iot-hub-csharp-csharp-device-management-get-started.md)
#### [Java 後端/Java 裝置](iot-hub-java-java-device-management-getstarted.md)
### 使用攣生實體的方式
#### [Node.js 後端/Node.js 裝置](iot-hub-node-node-twin-how-to-configure.md)
#### [.NET 後端/Node.js 裝置](iot-hub-csharp-node-twin-how-to-configure.md)
#### [.NET 後端/.NET 裝置](iot-hub-csharp-csharp-twin-how-to-configure.md)
#### [Java 後端/Java 裝置](iot-hub-java-java-twin-how-to-configure.md)
### 使用裝置作業更新裝置韌體
#### [Node 後端/Node 裝置](iot-hub-node-node-firmware-update.md)
#### [.NET 後端/Node.js 裝置](iot-hub-csharp-node-firmware-update.md)
### 排程及廣播作業
#### [Node.js 後端/Node.js 裝置](iot-hub-node-node-schedule-jobs.md)
#### [.NET 後端/Node.js 裝置](iot-hub-csharp-node-schedule-jobs.md)
#### [Java](iot-hub-java-java-schedule-jobs.md)
## 管理
### 建立 IoT 中樞 
#### [使用入口網站](iot-hub-create-through-portal.md)
#### [使用 PowerShell](iot-hub-create-using-powershell.md)
#### [使用 CLI 2.0](iot-hub-create-using-cli.md)
#### [使用 CLI](iot-hub-create-using-cli-nodejs.md)
#### [使用 REST API](iot-hub-rm-rest.md)
#### [使用 PowerShell 範本](iot-hub-rm-template-powershell.md)
#### [使用 .NET 範本](iot-hub-rm-template.md)
### 設定檔案上傳
#### [使用入口網站](iot-hub-configure-file-upload.md)
#### [使用 PowerShell](iot-hub-configure-file-upload-powershell.md)
#### [使用 CLI 2.0](iot-hub-configure-file-upload-cli.md)
### [大量管理 IoT 裝置](iot-hub-bulk-identity-mgmt.md)
### [用量度量](iot-hub-metrics.md)
### [作業監視](iot-hub-operations-monitoring.md)
### [設定 IP 篩選](iot-hub-ip-filtering.md)
## 安全
### [從頭建立安全性](iot-hub-security-ground-up.md)
### [安全性最佳做法](iot-hub-security-best-practices.md)
### [安全性架構](iot-hub-security-architecture.md)
### [保護您的 IoT 部署](iot-hub-security-deployment.md)
## Azure IoT Edge
### [概觀](iot-hub-iot-edge-overview.md)
### 開始使用
#### [Linux](iot-hub-linux-iot-edge-get-started.md)
#### [Windows](iot-hub-windows-iot-edge-get-started.md)
### 模擬裝置
#### [Linux](iot-hub-linux-iot-edge-simulated-device.md)
#### [Windows](iot-hub-windows-iot-edge-simulated-device.md)
### [使用真實裝置](iot-hub-iot-edge-physical-device.md)
### 建立模組
#### [Java](iot-hub-iot-edge-create-module-java.md)
#### [.NET Framework](https://github.com/Azure-Samples/iot-edge-samples#how-to-run-the-net-module-sample-windows-10)
#### [.NET Standard](iot-hub-iot-edge-create-module-dotnet-core.md)
#### [Node.js](iot-hub-iot-edge-create-module-js.md)
### 建置
#### [.NET Framework](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample)
#### [.NET Core 模組](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_module_sample)
#### [.NET Core 的受管閘道](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_managed_gateway)
#### [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample)
#### [Node.js](https://github.com/Azure/iot-edge/tree/master/samples/nodejs_simple_sample)
#### [動態新增模組](https://github.com/Azure/iot-edge/tree/master/samples/dynamically_add_module_sample)
#### [跨處理序的 proxy 模組](https://github.com/Azure/iot-edge/tree/master/samples/proxy_sample)
#### [原生模組主機](https://github.com/Azure/iot-edge/tree/master/samples/native_module_host_sample)

# 參考
## [程式碼範例](https://azure.microsoft.com/en-us/resources/samples/?service=iot-hub)
## [Azure CLI](/cli/azure/iot)
## [.NET (服務)](/dotnet/api/microsoft.azure.devices)
## [.NET (裝置)](/dotnet/api/microsoft.azure.devices.client)
## [Java (服務)](/java/api/com.microsoft.azure.sdk.iot.service)
## [Java (裝置)](/java/api/com.microsoft.azure.sdk.iot.device)
## [Node.js SDK](http://azure.github.io/azure-iot-sdk-node/)
## [C 裝置 SDK](https://azure.github.io/azure-iot-sdk-c/index.html)
## [Azure IoT Edge](http://azure.github.io/iot-edge/)
## [REST (資源提供者)](https://docs.microsoft.com/rest/api/iothub/iothubresource)
## [REST (裝置身分識別)](https://docs.microsoft.com/rest/api/iothub/deviceapi)
## [REST (裝置對應項)](https://docs.microsoft.com/rest/api/iothub/devicetwinapi)
## [REST (裝置通訊)](https://docs.microsoft.com/rest/api/iothub/httpruntime)
## [REST (作業)](https://docs.microsoft.com/rest/api/iothub/jobapi)

# 相關參考
## [Azure IoT 套件](https://azure.microsoft.com/documentation/suites/iot-suite/)
## [Azure 事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)
## [串流分析](https://azure.microsoft.com/documentation/services/stream-analytics/)
## [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

# 資源
## [Azure IoT 認證裝置目錄](https://catalog.azureiotsuite.com/)
## [Azure IoT 開發人員中心](https://azure.microsoft.com/develop/iot/)
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=internet-of-things)
## [DeviceExplorer 工具](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)
## [iothub-diagnostics 工具](https://github.com/Azure/iothub-diagnostics)
## [iothub-explorer 工具](https://github.com/Azure/iothub-explorer)
## [學習路徑](https://azure.microsoft.com/documentation/learning-paths/iot-hub/)
## [MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azureiothub)
## [價格](https://azure.microsoft.com/pricing/details/iot-hub/)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [服務更新](https://azure.microsoft.com/updates/?product=iot-hub)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-iot-hub)
## [技術案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=iot-hub)
