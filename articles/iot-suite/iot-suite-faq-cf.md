---
title: "Azure IoT 套件連線處理站常見問題集 | Microsoft Docs"
description: "IoT 套件連線處理站的常見問題集"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: corywink
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 35cf824210a14410d7ea2aedddde0040309901f9
ms.contentlocale: zh-tw
ms.lasthandoff: 08/25/2017

---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>IoT 套件連線處理站預先設定之解決方案的常見問題集

另請參閱，IoT 套件的一般[常見問題集](iot-suite-faq.md)。

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>哪裡可以找到預先設定之解決方案的原始程式碼？

原始程式碼儲存在下列 GitHub 儲存機制中︰

* [連線處理站預先設定的解決方案](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>什麼是 OPC UA？

2008 年推出的 OPC 統一架構 (UA) 是各平台通用的服務導向互通性標準。 OPC UA 由各種工業系統和裝置使用，例如產業 PC、PLC 和感應器。 OPC UA 會將 OPC Classic 規格的功能整合成一個內建安全性的可延伸架構。 它是 OPC Foundation 所推動的一套標準。 [OPC Foundation](http://opcfoundation.org/) 是擁有 440 名以上成員的非營利組織。 組織的目標是透過以下方式，使用 OPC 規格來促進多廠商、多平台、安全且可靠的互通性：

* 基礎結構
* 規格
* Technology
* 處理序

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Microsoft 為什麼選擇 OPC UA 作為連線工廠的預先設定解決方案？

Microsoft 選擇 OPC UA 的原因是它是一種開放式、非專屬、與平台無關、業界認同且已核准的標準。 它是 Industrie 4.0 (RAMI4.0) 參考架構解決方案的必要項目，以確保一組廣泛製造程序與設備之間的互通性。 Microsoft 看到客戶建置 Industrie 4.0 解決方案的需求。 OPC UA 支援可協助降低客戶達到其目標的障礙物，並對他們提供即時商務價值。

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>如何將公用 IP 位址新增至模擬虛擬機器？

您有兩個選項可新增 IP 位址：

* 使用[儲存機制](https://github.com/Azure/azure-iot-connected-factory)中的 PowerShell 指令碼 `Simulation/Factory/Add-SimulationPublicIp.ps1`。 傳遞您的部署名稱做為參數。 對於本機部署，請使用 `<your username>ConnFactoryLocal`。 指令碼會列印出虛擬機器的 IP 位址。

* 在 Azure 入口網站中，找出您部署的資源群組。 除了本機部署以外，您指定作為解決方案或部署的名稱就是資源群組的名稱。 對於使用組建指令碼的本機部署，資源群組的名稱是 `<your username>ConnFactoryLocal`。 現在請將新的**公用 IP 位址**資源新增至資源群組。

> [!NOTE]
> 無論哪種情況，請確定遵循 [Ubuntu 網站](https://wiki.ubuntu.com/Security/Upgrades)的指示安裝最新的修補程式。 只要您的虛擬機器可透過公用 IP 位址存取，請確保安裝保持最新狀態。

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>如何移除模擬虛擬機器的公用 IP 位址？

您有兩個選項可移除 IP 位址：

* 使用[儲存機制](https://github.com/Azure/azure-iot-connected-factory)的 PowerShell 指令碼 Simulation/Factory/Remove-SimulationPublicIp.ps1。 傳遞您的部署名稱做為參數。 對於本機部署，請使用 `<your username>ConnFactoryLocal`。 指令碼會列印出虛擬機器的 IP 位址。

* 在 Azure 入口網站中，找出您部署的資源群組。 除了本機部署以外，您指定作為解決方案或部署的名稱就是資源群組的名稱。 對於使用組建指令碼的本機部署，資源群組的名稱是 `<your username>ConnFactoryLocal`。 現在，將新的**公用 IP 位址**資源從資源群組中移除。

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>如何登入模擬虛擬機器？

只有在您已使用[儲存機制](https://github.com/Azure/azure-iot-connected-factory)中的 PowerShell 指令碼 `build.ps1` 部署您的解決方案時，才可支援登入模擬虛擬機器。

如果您已從 www.azureiotsuite.com 部署解決方案，則無法登入虛擬機器。 您無法登入是因為會隨機產生密碼，而且您無法重設密碼。

1. 新增虛擬機器的公用 IP 位址。 請參閱[如何將公用 IP 位址新增至模擬虛擬機器？](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. 使用虛擬機器的 IP 位址，為您的虛擬機器建立 SSH 工作階段。
1. 要使用的使用者名稱是：`docker`。
1. 要使用的密碼取決於您用來部署的版本而定：
    * 對於在 2017 年 6 月 1 日之前使用 build.ps1 指令碼部署的解決方案，密碼是：`Passw0rd`。
    * 對於在 2017 年 6 月 1 日之後使用 build.ps1 指令碼部署的解決方案，您可以在 `<name of your deployment>.config.user` 檔案中找到密碼。 密碼儲存在 **VmAdminPassword** 設定。 除非您使用 `build.ps1` 指令碼參數 `-VmAdminPassword` 指定密碼，否則密碼會在部署時隨機產生

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>如何在模擬虛擬機器中停止和啟動所有 docker 處理序？

1. 登入模擬虛擬機器。 請參閱[如何登入模擬虛擬機器？](#how-do-i-sign-in-to-the-simulation-vm)
1. 若要檢查哪些容器正在作用中，請執行：`docker ps`。
1. 若要停止所有模擬容器，請執行：`./stopsimulation`。
1. 若要啟動所有模擬容器：
    * 匯出具有名稱 **IOTHUB_CONNECTIONSTRING** 的殼層變數。 使用 `<name of your deployment>.config.user` 檔案中的 **IotHubOwnerConnectionString** 設定值。 例如：

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * 執行 `./startsimulation`。

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>如何更新虛擬機器中的模擬？

如果您已對模擬進行任何變更，您可以使用 `updatedimulation` 命令，使用[儲存機制](https://github.com/Azure/azure-iot-connected-factory)中的 PowerShell 指令碼 `build.ps1`。 此指令碼可建置所有模擬元件，停止虛擬機器中的模擬，並上傳、安裝，並啟動這些元件。

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>如何找出我的解決方案所使用之 IoT 中樞的連接字串？

如果您已透過[儲存機制](https://github.com/Azure/azure-iot-connected-factory)中的 `build.ps1` 指令碼部署解決方案，連接字串是 `<name of your deployment>.config.user` 檔案中的 **IotHubOwnerConnectionString** 值。

您也可以使用 Azure 入口網站尋找連接字串。 在您部署之資源群組中的 IoT 中樞資源中，找出連接字串設定。

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>連接的處理站模擬使用何種 IoT 中樞裝置？

模擬會自行註冊下列裝置：

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

使用 [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) 或 [iothub-explorer](https://github.com/azure/iothub-explorer) 工具可檢查哪些裝置已向解決方案正在使用的 IoT 中樞註冊。 若要使用這些工具，您需要您部署中的 IoT 中樞的連接字串。

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>如何從模擬元件取得記錄資料？

模擬中的所有元件會將資訊記錄到記錄檔中。 這些檔案可在虛擬機器中的資料夾 `home/docker/Logs` 中找到。 若要擷取記錄，您可以使用[儲存機制](https://github.com/Azure/azure-iot-connected-factory)中的 PowerShell 指令碼 `Simulation/Factory/Get-SimulationLogs.ps1`。

此指令碼需要登入虛擬機器。 您可能需要提供認證才可登入。 請參閱[如何登入模擬虛擬機器？](#how-do-i-sign-in-to-the-simulation-vm)以尋找認證。

指令碼會對虛擬機器新增/移除公用 IP 位址，如果沒有，請將它移除。 指令碼會將所有記錄檔放置在封存中，並下載封存至您的部署工作站。

或者透過 SSH 登入虛擬機器，並檢查執行階段的記錄檔。

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>如何檢查模擬是否傳送資料至雲端？

使用 [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) 或 [iothub-explorer](https://github.com/azure/iothub-explorer) 工具，您可以檢查從特定裝置傳送至 IoT 中樞的資料。 若要使用這些工具，您需要了解部署中 IoT 中樞的連接字串。 請參閱[如何找出我的解決方案所使用之 IoT 中樞的連接字串？](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

檢查由其中一個發行工具裝置傳送的資料：

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

如果您看不到傳送至 IoT 中樞的資料，則表示模擬有問題。 您應該採取的第一個分析步驟是分析模擬元件的記錄檔。 請參閱[如何從模擬元件取得記錄資料？](#how-can-i-get-log-data-from-the-simulation-components) 接著，嘗試停止並啟動模擬，如果仍然沒有任何傳送的資料，請徹底更新模擬。 請參閱[如何更新虛擬機器中的模擬？](#how-do-i-update-the-simulation-in-the-vm)

### <a name="next-steps"></a>後續步驟

您也可以瀏覽一些其他功能和預先設定的 IoT 套件解決方案的功能︰

* [預測性維護預先設定的解決方案概觀](iot-suite-predictive-overview.md)
* [連線處理站預先設定的解決方案概觀](iot-suite-connected-factory-overview.md)
* [從頭建立 IoT 安全性](securing-iot-ground-up.md)
