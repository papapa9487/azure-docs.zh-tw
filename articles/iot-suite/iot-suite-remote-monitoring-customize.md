---
title: "自訂遠端監視解決方案 - Azure | Microsoft Docs"
description: "本文章提供有關如何存取遠端監視預先設定解決方案之原始碼的資訊。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/07/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0e15193975b3142f604c2f3c0391b21d6fb1fac1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>自訂遠端監視預先設定解決方案

本文章提供有關如何存取原始碼，並自訂遠端監視預先設定解決方案的資訊。 本文章說明：

* 包含原始碼和資源的 GitHub 存放庫，適用於組成預先設定解決方案的微服務。
* 常見的自訂情節，例如新增裝置類型。

## <a name="project-overview"></a>專案概觀

### <a name="implementations"></a>實作

遠端監視解決方案有 .NET 和 Java 實作。 這兩項實作都會提供類似的功能，且依賴相同的基礎 Azure 服務。 您可以在這裡找到最上層 GitHub 存放庫：

* [.NET 解決方案](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java 解決方案](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>微服務

如果您需要解決方案的特定功能，可以存取每個個別微服務的 GitHub 存放庫。 每個微服務都會實作不同部分的解決方案功能。 若要了解有關整體架構的詳細資訊，請參閱[遠端監視預先設定解決方案架構](iot-suite-remote-monitoring-sample-walkthrough.md)。

本表會摘要說明每種語言每個微服務的目前可用性：

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| 微服務      | 說明 | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| Web UI            | 遠端監視解決方案的 Web 應用程式。 使用 React.js 架構實作 UI。 | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| IoT 中樞管理員   | 使用 IoT 中樞處理通訊。        | [可用](https://github.com/Azure/iothub-manager-java) | [可用](https://github.com/Azure/iothub-manager-dotnet)   |
| 驗證    |  管理 Azure Active Directory 整合。  | 尚未提供 | [可用](https://github.com/Azure/pcs-auth-dotnet)   |
| 裝置模擬 | 管理模擬裝置的集區。 | 尚未提供 | [可用](https://github.com/Azure/device-simulation-dotnet)   |
| 遙測         | 讓裝置遙測可供 UI 使用。 | [可用](https://github.com/Azure/device-telemetry-java) | [可用](https://github.com/Azure/device-telemetry-dotnet)   |
| 遙測代理程式   | 分析遙測串流、儲存來自 Azure IoT 中樞的訊息，並根據定義的規則產生警示。  | [可用](https://github.com/Azure/telemetry-agent-java) | [可用](https://github.com/Azure/telemetry-agent-dotnet)   |
| UI 設定         | 從 UI 管理組態資料。 | [可用](https://github.com/azure/pcs-ui-config-java) | [可用](https://github.com/azure/pcs-ui-config-dotnet)   |
| 儲存體介面卡   |  管理與儲存體服務之間的互動。   | [可用](https://github.com/azure/pcs-storage-adapter-java) | [可用](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| 反向 proxy     | 透過唯一端點，以受管理的方式公開私人資源。 | 尚未提供 | [可用](https://github.com/Azure/reverse-proxy-dotnet)   |

Java 解決方案目前使用 .NET 驗證、模擬及反向 Proxy 微服務。 Java 版本上市時就會立即取代這些微服務。

## <a name="presentation-and-visualization"></a>簡報和虛擬化

下列章節會說明在遠端監視解決方案中自訂簡報和視覺效果層級的選項：

### <a name="change-the-logo-in-the-ui"></a>變更 UI 中的標誌

預設的部署會使用 UI 中的 Contoso 公司名稱和標誌。 若要變更這些 UI 元素以顯示您的公司名稱和標誌：

1. 使用下列命令來複製 Web UI 存放庫：

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. 若要變更公司名稱，請在文字編輯器中開啟 `src/common/lang.js` 檔案。

1. 在檔案中找到下列行：

    ```js
    CONTOSO: 'Contoso',
    ```

1. 將 `Contoso` 取代為您的公司名稱。 例如：

    ```js
    CONTOSO: 'YourCo',
    ```

1. 儲存檔案。

1. 若要更新標誌，請將新的 SVG 檔案新增至 `assets/icons` 資料夾。 現有的標誌為 `assets/icons/Contoso.svg` 檔案。

1. 在文字編輯器中開啟 `src/components/layout/leftNav/leftNav.js` 檔案。

1. 在檔案中找到下列行：

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. 將 `Contoso.svg` 取代為您的標誌檔案名稱。 例如：

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. 在檔案中找到下列行：

    ```js
    alt="ContosoIcon"
    ```

1. 將 `ContosoIcon` 取代為您的 `alt` 文字。 例如：

    ```js
    alt="YourCoIcon"
    ```

1. 儲存檔案。

1. 若要測試變更，您可以在本機電腦上執行更新的 `webui`。 若要了解如何在本機建置和執行 `webui` 解決方案，請參閱 `webui` GitHub 存放庫讀我檔案中的[在本機建置、執行及測試](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally)。

1. 若要部署您的變更，請參閱[開發人員參考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)。

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>自訂地圖

如需解決方案中地圖元件的詳細資料，請參閱 GitHub 中的[自訂地圖](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)頁面。

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>其他自訂選項

若要進一步修改遠端監視解決方案中的簡報和視覺效果層級，您可以編輯程式碼。 相關的 GitHub 存放庫為：

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure PCS 遠端監視 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>裝置連線能力和串流

下列章節會說明在遠端監視解決方案中自訂裝置連線能力和串流層級的選項。 [裝置型號](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models)會描述解決方案中的裝置類型和遙測。 您可以使用模擬和實體裝置的裝置型號。

如需實體裝置實作的範例，請參閱[將裝置連線至遠端監視預先設定解決方案](iot-suite-connecting-devices-node.md)。

如果您是使用_實體裝置_，就必須向用戶端應用程式提供裝置型號，當中包含裝置中繼資料和遙測規格。

下列各節將討論使用模擬裝置的裝置型號：

### <a name="add-a-telemetry-type"></a>新增遙測類型

Contoso 示範解決方案中的裝置類型會指定每個裝置類型所傳送的遙測。 若要指定其他遙測類型，裝置可以將遙測定義作為中繼資料傳送至解決方案。 如果您是使用此格式，儀表板就會以動態方式使用您的裝置遙測和可用的方法，您無需修改 UI。 或者，您可以修改解決方案中的裝置類型定義。

若要了解如何在_裝置模擬器_微服務中新增自訂遙測，請參閱[使用模擬裝置來測試您的解決方案](iot-suite-remote-monitoring-test.md)。

### <a name="add-a-device-type"></a>新增裝置類型

Contoso 示範解決方案會定義某些範例裝置類型。 解決方案可讓您定義自訂的裝置類型，以符合特定的應用程式需求。 例如，貴公司可能會使用工業閘道作為主要裝置來連線至解決方案。

若要建立裝置的精確表示，您需要修改裝置上執行的應用程式以符合裝置需求。

若要了解如何在_裝置模擬器_微服務中新增新的裝置類型，請參閱[使用模擬裝置來測試您的解決方案](iot-suite-remote-monitoring-test.md)。

### <a name="define-custom-methods-for-simulated-devices"></a>定義模擬裝置的自訂方法

若要深入了解如何在遠端監視解決方案中定義模擬裝置的自訂方法，請參閱 GitHub 存放庫中的[裝置型號](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models)。

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>使用實體裝置

若要實作實體裝置上的方法和作業，請參閱下列 IoT 中樞文件：

* [了解 IoT 中樞的直接方法並從中樞叫用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
* [排程多個裝置上的作業](../iot-hub/iot-hub-devguide-jobs.md)。

### <a name="other-customization-options"></a>其他自訂選項

若要進一步修改遠端監視解決方案中的裝置連線能力和串流層級，您可以編輯程式碼。 相關的 GitHub 存放庫為：

* [裝置遙測 (.NET)](https://github.com/Azure/device-telemetry-dotnet)
* [裝置遙測 (Java)](https://github.com/Azure/device-telemetry-java)
* [遙測代理程式 (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [遙測代理程式 (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="data-processing-and-analytics"></a>資料處理和分析

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

若要修改遠端監視解決方案中的資料處理和分析層級，您可以編輯程式碼。 相關的 GitHub 存放庫為：

* [遙測代理程式 (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [遙測代理程式 (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="infrastructure"></a>基礎結構

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

若要修改遠端監視解決方案中的基礎結構，您可以編輯程式碼。 相關的 GitHub 存放庫為：

* [IoTHub 管理員 (.NET)](https://github.com/Azure/iothub-manager-dotnet)
* [IoTHub 管理員 (Java)](https://github.com/Azure/iothub-manager-java)
* [儲存體介面卡 (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet)
* [儲存體介面卡 (Java)](https://github.com/Azure/pcs-storage-adapter-java)

## <a name="next-steps"></a>後續步驟

在本文中，您已了解有關可用來協助您自訂預先設定解決方案的資源。

如需關於遠端監視預先設定解決方案的詳細概念資訊，請參閱[遠端監視架構](iot-suite-remote-monitoring-sample-walkthrough.md)

如需關於自訂遠端監視解決方案的詳細資訊，請參閱：

* [開發人員參考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開發人員疑難排解指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->