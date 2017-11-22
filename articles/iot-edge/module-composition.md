---
title: "Azure IoT Edge 模組撰寫 | Microsoft Docs"
description: "了解 Azure IoT Edge 模組有什麼內容以及如何重複使用"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6f9ca3d9b0f41210a3f43a8ae505f0a90b130b34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>了解如何使用、設定以及重複使用 IoT Edge 模組 - 預覽

Azure IoT Edge 可讓您在將多個 IoT Edge 模組部署至 IoT Edge 裝置之前進行撰寫。 本文說明在部署之前撰寫多個 IoT Edge 模組的最重要概念。 

## <a name="the-deployment-manifest"></a>部署資訊清單
部署資訊清單是 JSON 文件，描述：

* 必須部署哪些 IoT Edge 模組，以及其建立和管理選項；
* Edge 中樞的設定，說明訊息應該如何在模組之間以及模組與 IoT 中樞之間流動；
* (選擇性) 在模組對應項預期屬性中設定的值，以設定個別模組應用程式。

在 Azure IoT Edge 教學課程中，您會藉由完成 Azure IoT Edge 入口網站中的精靈，來建置部署資訊清單。 您也可以程式設計方式使用 REST 或 IoT 中樞服務 SDK，套用部署資訊清單。 請參閱[部署與監視][lnk-deploy]以取得 IoT Edge 部署的詳細資訊。

概括而言，部署資訊清單會設定在 IoT Edge 裝置上部署之 IoT Edge 模組預期屬性。 這兩個模組永遠存在：Edge 代理程式和 Edge 中樞。

資訊清單會遵循此結構：

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
                }
            },
            "{module1}": {  // optional
                "properties.desired": {
                    // desired properties of module with id {module1}
                }
            },
            "{module2}": {  // optional
                ...
            },
            ...
        }
    }

部署資訊清單的範例會在本章節的結尾報告。

> [!IMPORTANT]
> 所有 IoT Edge 裝置都需要使用部署資訊清單來設定。 新安裝的 IoT Edge 執行階段會報告錯誤碼，直到使用有效的資訊清單進行設定。 

### <a name="specify-the-modules"></a>指定模組
Edge 代理程式之模組對應項預期屬性包含：預期模組、其設定和管理選項，以及 Edge 代理程式的設定參數。

概括而言，本節的資訊清單包含 IoT Edge 執行階段模組 (Edge 代理程式和 Edge 中樞) 之模組映像和管理選項的參考，以及使用者指定的模組。

請參閱 [Edge 代理程式預期屬性][lnk-edgeagent-desired]以取得本節資訊清單的詳細說明。

> [!NOTE]
> 僅包含 IoT Edge 執行階段 (代理程式和中樞) 的部署資訊清單才是有效的。


### <a name="specify-the-routes"></a>指定路由
Edge 中樞提供方法以宣告方式在模組之間及模組與 IoT 中樞之間路由訊息。

路由具有下列語法：

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

來源可以是下列任何項目：

| 來源 | 說明 |
| ------ | ----------- |
| `/*` | 來自任何裝置或模組的所有裝置到雲端訊息 |
| `/messages/*` | 由裝置或模組透過部分或無輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/*` | 由模組透過部分或無輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/{moduleId}/*` | 由 {moduleId} 使用無輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/{moduleId}/outputs/*` | 由 {moduleId} 使用部分輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/{moduleId}/outputs/{output}` | 由 {moduleId} 使用 {output} 傳送的任何裝置到雲端訊息 |

這個條件可以是 IoT 中樞路由規則之 [IoT 中樞查詢語言][lnk-iothub-query]支援的任何條件。

接收可以是下列其中一種：

| 接收 | 說明 |
| ---- | ----------- |
| `$upstream` | 將訊息傳送到 IoT 中樞 |
| `BrokeredEndpoint(/modules/{moduleId}/inputs/{input})` | 將訊息傳送到模組 `{moduleId}` 的輸入 `{input}` |

務必記住，Edge 中樞提供至少一次保證，這表示訊息會儲存在本機，以免路由無法將訊息傳遞到其接收，例如，Edge 中樞無法連線到 IoT 中樞，或目標模組未連線。

Edge 中樞會儲存訊息最多到 Edge 中樞預期屬性的 `storeAndForwardConfiguration.timeToLiveSecs` 屬性指定的時間。

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>指定模組對應項的預期屬性

部署資訊清單可以指定 Edge 代理程式區段中指定之每個使用者模組之模組對應項的預期屬性。

在部署資訊清單中指定預期屬性時，它們會覆寫目前在模組對應項中任何預期屬性。

如果您未在部署資訊清單中指定模組對應項的預期屬性，IoT 中樞就不會修改模組對應項，您將無法以程式設計方式設定預期屬性。

### <a name="deployment-manifest-example"></a>部署資訊清單範例

這是部署資訊清單 JSON 文件的範例。

    {
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "runtime": {
                    "type": "docker",
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "loggingOptions": ""
                    }
                },
                "systemModules": {
                    "edgeAgent": {
                        "type": "docker",
                        "settings": {
                        "image": "microsoft/azureiotedge-agent:1.0-preview",
                        "createOptions": ""
                        }
                    },
                    "edgeHub": {
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-hub:1.0-preview",
                        "createOptions": ""
                        }
                    }
                },
                "modules": {
                    "tempSensor": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                        "createOptions": "{}"
                        }
                    },
                    "filtermodule": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "myacr.azurecr.io/filtermodule:latest",
                        "createOptions": "{}"
                        }
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "routes": {
                    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
                },
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 10
                }
            }
        }
    }
    }

## <a name="reference-edge-agent-module-twin"></a>參考：Edge 代理程式模組對應項

Edge 代理程式的模組對應項稱為 `$edgeAgent`，並且會協調裝置與 IoT 中樞上執行之 Edge 代理程式之間的通訊。
在單一裝置或規模部署時，在特定裝置上套用部署資訊清單時，會設定預期屬性。 請參閱[部署與監視][lnk-deploy]以取得如何在 IoT Edge 裝置上部署模組的詳細資訊。

### <a name="edge-agent-twin-desired-properties"></a>Edge 代理程式對應項預期屬性

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| schemaVersion | 必須為「1.0」 | 是 |
| runtime.type | 必須為「docker」 | 是 |
| runtime.settings.minDockerVersion | 此部署資訊清單需要設定為最小 Docker 版本 | 是 |
| runtime.settings.loggingOptions | stringified JSON，包含 Edge 代理程式容器的記錄選項。 [Docker 記錄選項][lnk-docker-logging-options] | 否 |
| systemModules.edgeAgent.type | 必須為「docker」 | 是 |
| systemModules.edgeAgent.settings.image | Edge 代理程式映像的 URI。 目前，Edge 代理程式無法自行更新。 | 是 |
| systemModules.edgeAgent.settings.createOptions | stringified JSON，包含 Edge 代理程式容器的建立選項。 [Docker 建立選項][lnk-docker-create-options] | 否 |
| systemModules.edgeAgent.configuration.id | 部署此模組之部署的識別碼。 | 使用部署來套用此資訊清單時，這是由 IoT 中樞進行設定。 非部署資訊清單的一部分。 |
| systemModules.edgeHub.type | 必須為「docker」 | 是 |
| systemModules.edgeHub.status | 必須為「執行中」 | 是 |
| systemModules.edgeHub.restartPolicy | 必須為「永遠」 | 是 |
| systemModules.edgeHub.settings.image | Edge 中樞映像的 URI。 | 是 |
| systemModules.edgeHub.settings.createOptions | stringified JSON，包含 Edge 中樞容器的建立選項。 [Docker 建立選項][lnk-docker-create-options] | 否 |
| systemModules.edgeHub.configuration.id | 部署此模組之部署的識別碼。 | 使用部署來套用此資訊清單時，這是由 IoT 中樞進行設定。 非部署資訊清單的一部分。 |
| modules.{moduleId}.version | 使用者定義的字串，表示此模組的版本。 | 是 |
| modules.{moduleId}.type | 必須為「docker」 | 是 |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | 是 |
| modules.{moduleId}.settings.image | 模組映像的 URI。 | 是 |
| modules.{moduleId}.settings.createOptions | stringified JSON，包含模組容器的建立選項。 [Docker 建立選項][lnk-docker-create-options] | 否 |
| modules.{moduleId}.configuration.id | 部署此模組之部署的識別碼。 | 使用部署來套用此資訊清單時，這是由 IoT 中樞進行設定。 非部署資訊清單的一部分。 |

### <a name="edge-agent-twin-reported-properties"></a>Edge 代理程式對應項報告屬性

Edge 代理程式報告屬性包含三個主要部分資訊：

1. 上次出現預期屬性之應用程式的狀態；
2. 目前在裝置上執行之模組的狀態；由 Edge 代理程式報告；以及
3. 目前在裝置上執行之預期屬性的複本。

萬一最新的預期屬性未由執行階段成功套用，讓裝置仍在執行先前的部署資訊清單，這個最後一項資訊就很有用。

> [!NOTE]
> 可以使用 [IoT 中樞查詢語言][lnk-iothub-query]查詢，以調查大規模部署的狀態時，Edge 代理程式的報告屬性相當有用。 請參閱[部署][lnk-deploy]以取得如何使用這項功能的詳細資訊。

下表不包含從預期屬性複製的資訊。

| 屬性 | 說明 |
| -------- | ----------- |
| lastDesiredVersion | 此 int 代表 Edge 代理程式處理之預期屬性的最後版本。 |
| lastDesiredStatus.code | 這是狀態代碼，代表 Edge 代理程式看到的最後預期屬性。 允許的值：`200` 成功、`400` 無效的設定、`412` 無效的結構描述版本、`417` 預期屬性是空的、`500` 失敗 |
| lastDesiredStatus.description | 狀態的文字描述 |
| deviceHealth | 如果所有模組的執行階段狀態為 `running`、`stopped` 時，則為 `healthy`，否則為 `unhealthy` |
| configurationHealth.{deploymentId}.health | 如果部署 {deploymentId} 設定之所有模組的執行階段狀態為 `running` 或 `stopped` 時，則為 `healthy`，否則為 `unhealthy` |
| runtime.platform.OS | 報告裝置上執行的作業系統 |
| runtime.platform.architecture | 報告裝置上的 CPU 架構 |
| systemModules.edgeAgent.runtimeStatus | Edge 代理程式的報告狀態：{"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Edge 代理程式之報告狀態的文字描述。 |
| systemModules.edgeHub.runtimeStatus | Edge 中樞的目前狀態：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | 狀況不良時，Edge 中樞目前狀態的文字描述。 |
| systemModules.edgeHub.exitCode | 如果結束，Edge 中樞容器報告的結束代碼 |
| systemModules.edgeHub.startTimeUtc | Edge 中樞的上次啟動時間 |
| systemModules.edgeHub.lastExitTimeUtc | Edge 中樞的上次結束時間 |
| systemModules.edgeHub.lastRestartTimeUtc | Edge 中樞的上次重新啟動時間 |
| systemModules.edgeHub.restartCount | 此模組重新啟動的次數，作為重新啟動原則的一部分。 |
| modules.{moduleId}.runtimeStatus | 模組的目前狀態：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | 狀況不良時，模組目前狀態的文字描述。 |
| modules.{moduleId}.exitCode | 如果結束，模組容器報告的結束代碼 |
| modules.{moduleId}.startTimeUtc | 模組的上次啟動時間 |
| modules.{moduleId}.lastExitTimeUtc | 模組的上次結束時間 |
| modules.{moduleId}.lastRestartTimeUtc | 模組的上次重新啟動時間 |
| modules.{moduleId}.restartCount | 此模組重新啟動的次數，作為重新啟動原則的一部分。 |

## <a name="reference-edge-hub-module-twin"></a>參考：Edge 中樞模組對應項

Edge 中樞的模組對應項稱為 `$edgeHub`，並且會協調裝置與 IoT 中樞上執行之 Edge 中樞之間的通訊。
在單一裝置或規模部署時，在特定裝置上套用部署資訊清單時，會設定預期屬性。 請參閱[部署][lnk-deploy]以取得如何在 IoT Edge 裝置上部署模組的詳細資訊。

### <a name="edge-hub-twin-desired-properties"></a>Edge 中樞對應項預期屬性

| 屬性 | 說明 | 部署資訊清單中的必要項目 |
| -------- | ----------- | -------- |
| schemaVersion | 必須為「1.0」 | 是 |
| routes.{routeName} | 字串，表示 Edge 中樞路由。 | `routes` 元素可以存在但為空白。 |
| storeAndForwardConfiguration.timeToLiveSecs | Edge 中樞在與路由端點中斷連線時保留訊息的時間 (以秒為單位)，例如，與 IoT 中樞或本機模組中斷連線 | 是 |

### <a name="edge-hub-twin-reported-properties"></a>Edge 中樞對應項報告屬性

| 屬性 | 說明 |
| -------- | ----------- |
| lastDesiredVersion | 此 int 代表 Edge 中樞處理之預期屬性的最後版本。 |
| lastDesiredStatus.code | 這是狀態代碼，代表 Edge 中樞看到的最後預期屬性。 允許的值：`200` 成功、`400` 無效的設定、`500` 失敗 |
| lastDesiredStatus.description | 狀態的文字描述 |
| clients.{device or module identity}.status | 此裝置或模組的連線狀態。 可能的值 {"connected" \| "disconnected"}. 只有模組身分識別可以處於中斷連線狀態。 連線到 Edge 中樞的下游裝置只會在連線時顯示。 |
| clients.{device or module identity}.lastConnectTime | 上一次裝置或模組連線時 |
| clients.{device or module identity}.lastDisconnectTime | 上一次裝置或模組中斷連線時 |

## <a name="next-steps"></a>後續步驟

您現在知道如何使用 IoT Edge 模組，[了解開發 IoT Edge 模組的需求和工具][lnk-module-dev]。

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
