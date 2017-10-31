---
title: "使用 Azure IoT 套件連線處理站 OPC 發行者 | Microsoft Docs"
description: "如何建置和部署連線處理站 OPC 發行者的參考實作。"
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
ms.date: 10/16/2017
ms.author: dobett
ms.openlocfilehash: fd823194f6e51600b9d4ca1daa053db837871fef
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>適用於 Azure IoT Edge 的 OPC 發行者

本文說明如何使用 OPC 發行者的參考實作。 參考實作會示範如何使用 Azure IoT Edge 執行以下工作：

- 連線至現有的 OPC UA 伺服器。
- 使用 JSON 承載，以 OPC UA *Pub/Sub* 格式從伺服器將 JSON 編碼的遙測資料發佈至 Azure IoT 中樞。 可以使用 Azure IoT Edge 支援的任何傳輸通訊協定。

此參考應用程式包括：

- 用於連線至您網路上現有 OPC UA 伺服器的 OPC UA 用戶端。
- 接聽 62222 連接埠的 OPC UA 伺服器 (可用於管理發佈內容)。

此應用程式使用 .NET Core 實作，並可在 .NET Core 支援的平台上執行。

發行者建立端點連線時，會實作重試邏輯。 發行者預計端點會在指定的保持連線要求數量範圍內回應。 此重試邏輯可讓發行者偵測 OPC UA 伺服器是否發生電源中斷等狀況。

針對 OPC UA 伺服器各個相異的發佈間隔，其會建立個別獨立的訂用帳戶，並在定用帳戶上更新該發佈間隔的所有節點。

發行者支援將資料批次傳送至 IoT 中樞，以減少網路負載。 當達到設定的封裝大小時，才會將該批次傳送至 IoT 中樞。

此應用程式採用 OPC Foundation 的 OPC UA 參考堆疊，因此適用授權限制。 如需 OPC UA 文件和授權條款，請造訪 http://opcfoundation.github.io/UA-.NETStandardLibrary/。

可以在[適用於 Azure IoT Edge 的 OPC 發行者](https://github.com/Azure/iot-edge-opc-publisher) GitHub 存放庫，找到 OPC 發行者的原始程式碼。

## <a name="prerequisites"></a>必要條件

若要建置應用程式，需要作業系統適用的 [.NET Core SDK 1.1.](https://docs.microsoft.com/dotnet/core/sdk) 。

## <a name="build-the-application"></a>建置應用程式

### <a name="as-native-windows-application"></a>若為原生 Windows 應用程式

以 Visual Studio 2017 開啟 `OpcPublisher.sln` 專案，然後點擊 F7 以建置解決方案。

### <a name="as-docker-container"></a>若為 Docker 容器

若要將應用程式建置為 Windows Docker 容器，請使用 `Dockerfile.Windows` 組態檔。

若要將應用程式建置為 Linux Docker 容器，請使用 `Dockerfile` 組態檔。

至開發用電腦的存放庫根目錄，在主控台視窗中輸入下列命令：

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

`docker build` 的 `-f` 選項為選擇性，預設為使用 `Dockerfile` 組態檔。

Docker 亦可從 git 存放庫直接建置。 您可使用下列命令來建置 Linux Docker 容器：

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>設定要發行的 OPC UA 節點

若要設定哪些 OPC UA 節點應將其值發佈至 Azure IoT 中樞，請建立 JSON 格式的組態檔。 此組態檔的預設名稱為 `publishednodes.json`。 當應用程式使用 **PublishNode** 或 **UnpublishNode** 等 OPC UA 伺服器方法時，會更新並儲存此組態檔。

組態檔語法如下：

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>執行應用程式

### <a name="command-line-options"></a>命令列選項

若要查看應用程式的完整使用方式，請使用 `--help` 命令列選項。 下列範例顯示命令的結構：

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname` 為要使用的 OPC UA 應用程式名稱。 這是必要參數。 應用程式名稱亦用於在 IoT 中樞裝置登錄中註冊發行者。

`IoT Hubconnectionstring` 為 IoT 中樞擁有者的連接字串。 這是選擇性參數。

支援下列選項：

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

您可使用下列環境變數來控制應用程式：
- `_HUB_CS`：設定 IoT 中樞擁有者連接字串
- `_GW_LOGP`：設定所用記錄檔的檔名
- `_TPC_SP`：設定受信任站台憑證的儲存路徑
- `_GW_PNFP`：設定發行組態檔的檔名

命令列引數會凌駕於環境變數設定。

一般而言，僅會在初次啟動應用程式時指定 IoT 中樞擁有者連接字串。 連接字串會加密，並儲存在平台的憑證存放區。

在後續呼叫中，系統會從平台的憑證存放區讀取和重複使用連接字串。 若您在每次啟動時皆指定連接字串，則每次都會移除並重新建立 IoT 中樞裝置登錄中的裝置。

### <a name="native-on-windows"></a>原生於 Windows

若要在 Windows 上以原生方式執行應用程式，請使用 Visual Studio 2017 開啟 `OpcPublisher.sln` 專案，再建置解決方案並發佈。 可以透過下列方式，在已發佈至其中的**目標目錄**內啟動應用程式：

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>使用自行建置的容器

若要在自行建置容器中執行應用程式，請建置並啟動您的專屬容器：

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>使用來自 hub.docker.com 的容器

DockerHub 提供了預先建置好的容器。 執行下列命令即可啟動容器：

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>使用容器時的重要事項

#### <a name="access-to-the-publisher-opc-ua-server"></a>存取發行者 OPC UA 伺服器

依預設，發行者 OPC UA 伺服器會接聽 62222 連接埠。 若要公開容器內的該輸入連接埠，您必須使用 `docker run` 選項 `-p`：

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>啟用容器間名稱解析

若要啟用從容器內部至其他容器的名稱解析，您必須：

- 建立使用者定義的 Docker 橋接網路。
- 使用 `--network` 選項，將容器連線至網路。
- 使用 `--name` 選項，指派容器名稱。

下列範例為各個設定選項：

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

現可使用 `publisher` 名稱，讓網路上的其他容器連線至此容器。

#### <a name="assign-a-hostname"></a>指派主機名稱

發行者會使用其運作所在電腦的主機名稱，來產生憑證和端點。 若您未使用 `-h` 選項指定，則 Docker 會隨機選擇一個主機名稱。 以下範例是將容器的內部主機名稱設為 `publisher`：

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>使用繫結掛接 (共用檔案系統)

在某些情況下，您會想要從主機上的某些位置讀取組態資訊，或是寫入記錄檔，而不想使用容器檔案系統。 若要設定此行為，請在繫結掛接模式下使用 `docker run` 的 `-v` 選項。 例如：

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>儲存 X509 憑證

儲存 X509 憑證不適用於繫結掛接，因為擁有者至存放區的路徑權限需為 `rw`。 必須改為在磁碟區模式下，使用 `docker run` 的 `-v` 選項。

## <a name="debug-the-application"></a>偵錯應用程式

### <a name="native-on-windows"></a>原生於 Windows

使用 Visual Studio 2017 開啟 `OpcPublisher.sln`專案，然後點擊 F5 開始偵錯應用程式。

### <a name="in-a-docker-container"></a>在 Docker 容器中

Visual Studio 2017 支援使用 `docker-compose` 在 Docker 容器中偵錯應用程式。 不過，此方法無法傳遞命令列參數。

VS2017 支援的另一種替代偵錯選項是透過 `ssh` 進行偵錯。 可使用存放庫根目錄中的 Docker 建置組態檔 `Dockerfile.ssh`，建立已啟用 SSH 的容器：

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

現可啟動容器來偵錯發行者：

```cmd/sh
docker run -it publisherssh
```

在容器中必須手動啟動 **ssh** 精靈：

```cmd/sh
service ssh start
```

此時，您可以建立使用者為 `root`，密碼為 `Passw0rd` 的 **ssh** 工作階段。

若要準備在容器中偵錯應用程式，請完成下列額外步驟：

1. 在主機端建立 `launch.json` 檔案：

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. 建置您的專案，並將其發佈至您選擇的目錄。

1. 使用 `WinSCP` 之類的工具，將已發佈的檔案複製至容器中的 `/root/publisher` 目錄。 若選擇使用不同的目錄，請更新 `launch.json` 檔案中的 `cdw` 屬性。

現可在 Visual Studio 的命令視窗中，使用下列命令開始偵錯：

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>後續步驟

建議採取的後續步驟，是瞭解如何[在 Windows 或 Linux 上部署連線處理站預先設定解決方案的閘道](iot-suite-connected-factory-gateway-deployment.md)。