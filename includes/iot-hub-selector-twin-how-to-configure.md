> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>簡介

在[開始使用 IoT 中樞裝置對應項][lnk-twin-tutorial]中，您會學到如何使用「標籤」從解決方案後端設定裝置中繼資料，使用「報告屬性」從裝置應用程式報告裝置條件，以及使用類似 SQL 的語言查詢此資訊。

在本教學課程中，您會學習如何使用裝置對應項的「所需屬性」搭配「報告屬性」，遠端設定裝置應用程式。 更具體來說，本教學課程示範裝置對應項的報告和所需屬性如何啟用裝置應用程式的多步驟組態，並提供所有裝置的這項作業狀態的解決方案後端可見性。 您可以在[使用 IoT 中樞的裝置管理概觀][lnk-dm-overview]中，找到關於裝置組態所扮演角色的詳細資訊。

概括而言，使用裝置對應項讓解決方案後端能夠指定受管理裝置所需的設定，而不是傳送特定的命令。 這樣會讓裝置負責設定更新其組態的最佳方式 (在特定裝置條件會影響立即執行特定命令能力的 IoT 案例中很重要)，同時持續向解決方案後端報告更新程序的目前狀態和潛在錯誤條件。 這個模式對於大量裝置的管理很有幫助，因為它可讓解決方案後端具有所有裝置上設定程序狀態的完整可見性。

> [!NOTE]
> 在裝置是以更為互動的方式控制的案例中 (從使用者控制的應用程式開啟風扇)，請考量使用[直接方法][lnk-methods]。
> 
> 

在本教學課程中，解決方案後端會變更目標裝置的遙測組態，因此，裝置應用程式會遵循多步驟程序以套用組態更新 (例如，需要軟體模組重新啟動)，此教學課程會模擬簡單的延遲。

解決方案後端會以下列方式在裝置對應項的所需屬性中儲存組態：

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [!NOTE]
> 因為設定可以是複雜的物件，所以會為它們指派唯一的識別碼 (雜湊或 [GUID][lnk-guid]) 以簡化其比較。
> 
> 

裝置應用程式會在報告屬性中報告其目前組態鏡像所需屬性 **telemetryConfig**︰

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

請注意報告 **telemetryConfig** 如何具有額外屬性 **status**，用來報告組態更新程序的狀態。

當收到新的所需組態時，裝置應用程式會藉由變更資訊來報告暫止的組態︰

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

然後，在稍後的時間，裝置應用程式會藉由更新上述屬性來報告這項作業成功或失敗。
請注意解決方案後端如何隨時查詢所有裝置的組態程序狀態。

本教學課程說明如何：

* 建立模擬裝置應用程式，接收來自解決方案後端的組態更新，並且將多個更新報告為組態更新程序上的「報告屬性」。
* 建立後端應用程式，更新裝置的所需組態，然後查詢組態更新程序。

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
