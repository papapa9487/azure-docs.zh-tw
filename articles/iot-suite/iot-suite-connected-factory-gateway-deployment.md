---
title: "部署連線處理站閘道 - Azure | Microsoft Docs"
description: "如何在 Windows 或 Linux 上部署閘道以啟用連線處理站預先設定解決方案的連線。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.openlocfilehash: e99a7bc34ac5ed060100e5f5032513bf4b18b2eb
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>在 Windows 或 Linux 上部署連線處理站預先設定解決方案的閘道

針對連線處理站的預先設定解決方案，部署閘道所需的軟體有兩個元件：

* OPC Proxy 會建立與 IoT 中樞的連線。 然後 OPC Proxy 會等待在連線處理站解決方案入口網站中執行之整合式 OPC 瀏覽器的命令和控制訊息。
* *OPC 發行者*會連線到現有的內部部署 OPC UA 伺服器，並將它們的遙測訊息轉送到 IoT 中樞。

這兩個元件都是開放程式碼，且以 GitHub 上的來源與 Docker 容器的方式提供：

| GitHub | DockerHub |
| ------ | --------- |
| [OPC 發行者][lnk-publisher-github] | [OPC 發行者][lnk-publisher-docker] |
| [OPC Proxy][lnk-proxy-github] | [OPC Proxy][lnk-proxy-docker] |

這兩個元件都不需要公開 IP 位址或閘道防火牆中的開放連接埠。 OPC Proxy 和 OPC 發行者僅使用連出連接埠 443、5671 和 8883。

本文中的步驟示範如何在 [Windows](#windows-deployment) 或 [Linux](#linux-deployment) 上使用 Docker 部署閘道。 閘道會啟用連線處理站預先設定解決方案的連線。

> [!NOTE]
> 在 Docker 容器中執行的閘道軟體是 [Azure IoT Edge]。

## <a name="windows-deployment"></a>Windows 部署

> [!NOTE]
> 如果您還沒有閘道裝置，Microsoft 建議您從我們的合作夥伴購買商業閘道。 請瀏覽 [Azure IoT 裝置目錄]尋找與連線處理站解決方案相容的閘道裝置清單。 依照裝置隨附的指示來設定閘道。 或者，使用下列指示手動設定其中一個現有的閘道。

### <a name="install-docker"></a>安裝 Docker

在 Windows 閘道裝置上安裝 [Docker for Windows]。 在 Windows Docker 安裝期間，選取主機電腦上要與 Docker 共用的磁碟機。 下列螢幕擷取畫面顯示共用 Windows 系統上的 D 磁碟機︰

![安裝 Docker][img-install-docker]

然後在共用磁碟機的根目錄中建立一個稱為 **docker** 的資料夾。
您也可以在安裝 Docker 之後從 [Settings (設定)] 功能表執行這個步驟。

### <a name="configure-the-gateway"></a>設定閘道

1. 您需要 Azure IoT 套件連線處理站部署的 **iothubowner** 連接字串才能完成閘道部署。 在 [Azure 入口網站]中，瀏覽至您在部署連線處理站時所建立之資源群組中的 IoT 中樞。 按一下 [共用存取原則] 以存取 **iothubowner** 連接字串︰

    ![找出 IoT 中樞連接字串][img-hub-connection]

    複製 [連接字串 – 主要金鑰] 的值。

1. 從命令提示字元使用下列命令，執行「一次」兩個閘道模組來設定 IoT 中樞的閘道︰

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** 是用來提供給 OPC UA 發行者的名稱，格式為 **publisher.&lt;您的完整網域名稱&gt;**。 例如，如果您的處理站網路名稱為 **myfactorynetwork.com**，則 **ApplicationName** 值為 **publisher.myfactorynetwork.com**。
    * **&lt;IoTHubOwnerConnectionString&gt;** 是您在上一個步驟中複製的 **iothubowner** 連接字串。 您只會在此步驟中使用此連接字串，在下列步驟中就不再需要它：

    您之後會使用對應的 D:\\docker 資料夾 (`-v` 引數) 來保存閘道模組所使用的兩個 X.509 憑證。

### <a name="run-the-gateway"></a>執行閘道

1. 使用下列命令重新啟動閘道︰

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. 為了安全起見，保存在 D:\\docker 資料夾中的兩個 X.509 憑證都會包含私密金鑰。 將此資料夾的存取權限制為用於執行 Docker 容器的認證 (通常是 **Administrators**)。 以滑鼠右鍵按一下 D:\\docker 資料夾，依序選擇 [內容]、[安全性]，然後選擇 [編輯]。 賦予 **Administrators** 完全控制，並移除其他人︰

    ![授與 Docker 共用的權限][img-docker-share]

1. 驗證網路連線。 在命令提示字元中，輸入命令 `ping publisher.<your fully qualified domain name>` 以 Ping 閘道。 如果無法連線到目的地，請在閘道的 hosts 檔案中新增閘道的 IP 位址和名稱。 hosts 檔案位於 **Windows\\System32\\drivers\\etc** 資料夾中。

1. 接下來，試著使用閘道上執行的本機 OPC UA 用戶端連線到發行者。 OPC UA 端點 URL 是 `opc.tcp://publisher.<your fully qualified domain name>:62222`。 如果您沒有 OPC UA 用戶端，則可以下載並使用[開放原始碼的 OPC UA 用戶端]。

1. 當您已成功完成這些本機測試時，請瀏覽至連線處理站解決方案入口網站中的 [Connect your own OPC UA Server (連線您自己的 OPC UA 伺服器)] 頁面。 輸入發行者端點 URL (`tcp://publisher.<your fully qualified domain name>:62222`) 並按一下 [Connect (連線)]。 您會收到憑證警告，然後按一下 [Proceed (繼續)]。 接下來您會收到發行者不信任 UA Web 用戶端的錯誤。 若要解決這個錯誤，請在閘道上從 **D:\\docker\\Rejected Certificates\\certs** 資料夾將 **UA 網頁用戶端**憑證複製到 **D:\\docker\\UA Applications\\certs** 資料夾。 您不需要重新啟動閘道。 重複此步驟。 您現在可以從雲端連線到閘道，而且您也準備好在解決方案中新增 OPC UA 伺服器。

### <a name="add-your-opc-ua-servers"></a>新增 OPC UA 伺服器

1. 瀏覽至連線處理站解決方案入口網站中的 [Connect your own OPC UA Server (連線您自己的 OPC UA 伺服器)] 頁面。 依照上一節中相同的步驟在連線處理站入口網站和 OPC UA 伺服器之間建立信任。 此步驟會在連線處理站入口網站和 OPC UA 伺服器之間建立相互信任的憑證，並建立連線。

1. 瀏覽 OPC UA 伺服器的 OPC UA 節點樹狀結構、以滑鼠右鍵按一下 OPC 節點，然後選取 [publish (發行)]。 若要以此方式發行，OPC UA 伺服器和發行者必須在相同的網路上。 換句話說，如果發行者的完整網域名稱是 **publisher.mydomain.com**，則 OPC UA 伺服器的完整網域名稱就必須是 (例如) **myopcuaserver.mydomain.com**。如果您的設定不同，則可以在 **D:\\docker** 資料夾的 publishesnodes.json 檔案中手動新增節點。 第一個成功發佈的 OPC 節點上會自動產生 publishesnodes.json 檔案。

1. 現在會從閘道裝置傳送遙測。 您可以在連線處理站入口網站位於 [New Factory (新處理站)] 之下的 [Factory Locations (處理站位置)] 中檢視遙測。

## <a name="linux-deployment"></a>Linux 部署

> [!NOTE]
> 如果您還沒有閘道裝置，Microsoft 建議您從我們的合作夥伴購買商業閘道。 請瀏覽 [Azure IoT 裝置目錄]尋找與連線處理站解決方案相容的閘道裝置清單。 依照裝置隨附的指示來設定閘道。 或者，使用下列指示手動設定其中一個現有的閘道。

在 Linux 閘道裝置上[安裝 Docker]。

### <a name="configure-the-gateway"></a>設定閘道

1. 您需要 Azure IoT 套件連線處理站部署的 **iothubowner** 連接字串才能完成閘道部署。 在 [Azure 入口網站]中，瀏覽至您在部署連線處理站時所建立之資源群組中的 IoT 中樞。 按一下 [共用存取原則] 以存取 **iothubowner** 連接字串︰

    ![找出 IoT 中樞連接字串][img-hub-connection]

    複製 [連接字串 – 主要金鑰] 的值。

1. 從殼層使用下列命令，執行「一次」兩個閘道模組來設定 IoT 中樞的閘道︰

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** 是閘道以 **publisher.&lt;完整網域名稱&gt;** 的格式建立的 OPC UA 應用程式名稱。 例如，**publisher.microsoft.com**。
    * **&lt;IoTHubOwnerConnectionString&gt;** 是您在上一個步驟中複製的 **iothubowner** 連接字串。 您只會在此步驟中使用此連接字串，在下列步驟中就不再需要它：

    您之後會使用 **/shared** 資料夾 (`-v` 引數) 來保存閘道模組所使用的兩個 X.509 憑證。

### <a name="run-the-gateway"></a>執行閘道

1. 使用下列命令重新啟動閘道︰

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 --rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. 為了安全起見，保存在 **/shared** 資料夾中的兩個 X.509 憑證都會包含私密金鑰。 將此資料夾的存取權限制為用於執行 Docker 容器的認證。 若要設定權限只供 **root** 使用，請對資料夾使用 `chmod` 殼層命令。

1. 驗證網路連線。 在 Shell 中，輸入命令 `ping publisher.<your fully qualified domain name>` 以 Ping 閘道。 如果無法連線到目的地，請在閘道的 hosts 檔案中新增閘道的 IP 位址和名稱。 hosts 檔案位於 **/etc** 資料夾中。

1. 接下來，試著使用閘道上執行的本機 OPC UA 用戶端連線到發行者。 OPC UA 端點 URL 是 `opc.tcp://publisher.<your fully qualified domain name>:62222`。 如果您沒有 OPC UA 用戶端，則可以下載並使用[開放原始碼的 OPC UA 用戶端]。

1. 當您已成功完成這些本機測試時，請瀏覽至連線處理站解決方案入口網站中的 [Connect your own OPC UA Server (連線您自己的 OPC UA 伺服器)] 頁面。 輸入發行者端點 URL (`tcp://publisher.<your fully qualified domain name>:62222`) 並按一下 [Connect (連線)]。 您會收到憑證警告，然後按一下 [Proceed (繼續)]。 接下來您會收到發行者不信任 UA Web 用戶端的錯誤。 若要解決這個錯誤，請在閘道上從 **/shared/Rejected Certificates/certs** 資料夾將 **UA 網頁用戶端**憑證複製到 **/shared/UA Applications/certs** 資料夾。 您不需要重新啟動閘道。 重複此步驟。 您現在可以從雲端連線到閘道，而且您也準備好在解決方案中新增 OPC UA 伺服器。

### <a name="add-your-opc-ua-servers"></a>新增 OPC UA 伺服器

1. 瀏覽至連線處理站解決方案入口網站中的 [Connect your own OPC UA Server (連線您自己的 OPC UA 伺服器)] 頁面。 依照上一節中相同的步驟在連線處理站入口網站和 OPC UA 伺服器之間建立信任。 此步驟會在連線處理站入口網站和 OPC UA 伺服器之間建立相互信任的憑證，並建立連線。

1. 瀏覽 OPC UA 伺服器的 OPC UA 節點樹狀結構、以滑鼠右鍵按一下 OPC 節點，然後選取 [publish (發行)]。 若要以此方式發行，OPC UA 伺服器和發行者必須在相同的網路上。 換句話說，如果發行者的完整網域名稱是 **publisher.mydomain.com**，則 OPC UA 伺服器的完整網域名稱就必須是 (例如) **myopcuaserver.mydomain.com**。如果您的設定不同，則可以在 **/shared** 資料夾的 publishesnodes.json 檔案中手動新增節點。 第一個成功發佈的 OPC 節點上會自動產生 publishesnodes.json。

1. 現在會從閘道裝置傳送遙測。 您可以在連線處理站入口網站位於 [New Factory (新處理站)] 之下的 [Factory Locations (處理站位置)] 中檢視遙測。

## <a name="next-steps"></a>後續步驟

若要深入了解連線處理站預先設定的解決方案架構，請參閱[連線處理站預先設定的解決方案逐步解說][lnk-walkthrough]。

了解 [OPC 發行者參考實作](iot-suite-connected-factory-publisher.md)。

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker for Windows]: https://www.docker.com/docker-windows
[Azure IoT 裝置目錄]: https://catalog.azureiotsuite.com/?q=opc
[Azure 入口網站]: http://portal.azure.com/
[開放原始碼的 OPC UA 用戶端]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[安裝 Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT Edge]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy