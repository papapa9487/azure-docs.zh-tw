## <a name="typical-output"></a>典型輸出

下列範例顯示 Hello World 範例寫入記錄檔中的輸出。 已針對易讀性將輸出格式化︰

```json
[{
    "time": "Mon Apr 11 13:42:50 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:42:50 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:42:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:43:00 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:45:00 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>程式碼片段

本節探討 hello\_world 範例中程式碼的一些重要區段。

### <a name="iot-edge-gateway-creation"></a>建立 IoT Edge 閘道

若要建立閘道，請實作閘道程序。 此程式會建立內部基礎結構 (訊息代理程式)、載入 IoT Edge 模組，以及設定閘道程序。 IoT Edge 提供的 **Gateway\_Create\_From\_JSON** 函式可讓您從 JSON 檔案啟動閘道。 若要使用 **Gateway\_Create\_From\_JSON** 函式，請將它傳遞到 JSON 檔案的路徑，以指定要載入的 IoT Edge 模組。

在 [main.c][lnk-main-c] 檔案中，您可以找到 Hello World 範例中閘道程序的程式碼。 下列程式碼片段顯示精簡版本的閘道程序程式碼，以利閱讀。 此範例會建立閘道，然後先等待使用者按下 **ENTER** 鍵，再終止閘道。

```C
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

JSON 設定檔案包含要載入的 IoT Edge 模組清單以及模組之間的連結。 每個 IoT Edge 模組都必須指定：

* **名稱**：模組的唯一名稱。
* **載入器**︰知道如何載入所需模組的載入器。 載入器是一個用來載入不同模組類型的擴充點。 IoT Edge 提供可與以原生 C、Node.js、Java 和 .NET 撰寫的模組搭配使用的載入器。 Hello World 範例只會使用原生 C 載入器，因為此範例中的所有模組都是以 C 撰寫的動態程式庫。如需有關如何使用以不同語言撰寫之 IoT Edge 模組的詳細資訊，請參閱 [Node.js](https://github.com/Azure/iot-edge/blob/master/samples/nodejs_simple_sample/)、[Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample) 或 [.NET](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample) 範例。
    * **名稱**︰用來載入模組的載入器名稱。
    * **進入點**：包含模組之程式庫的路徑。 在 Linux 上，此程式庫是 .so 檔案，在 Windows 上，此程式庫是 .dll 檔案。 此進入點是要使用的載入器類型特定的。 Node.js 載入器的進入點是 .js 檔案。 Java 載入器的進入點是 classpath 與類別名稱。 .NET 載入器的進入點是組件名稱與類別名稱。

* **args**：模組所需的任何組態資訊。

下列程式碼所示範的 JSON 可用來在 Linux 上宣告適用於 Hello World 範例的所有 IoT Edge 模組。 模組是否需要引數取決於模組的設計。 在此範例中，Logger 模組所採用的引數是輸出檔的路徑，而 hello\_world 模組沒有任何引數。

```json
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

JSON 檔案也包含會傳遞給訊息代理程式之模組之間的連結。 連結有兩個屬性︰

* **來源**：來自 `modules` 區段的模組名稱，或 `\*`。
* **接收**：來自 `modules` 區段的模組名稱。

每個連結都會定義訊息路由和方向。 來自**來源**模組的訊息會傳遞給**接收**模組。 您可以將**來源**模組設定為 `\*`，這表示**接收**模組會從任何模組接收訊息。

下列程式碼所示範的 JSON 可用來在 Linux 上設定在 hello\_world 範例所使用之模組間的連結。 `hello_world` 模組所產生的每則訊息都是由 `logger` 模組取用。

```json
"links":
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="helloworld-module-message-publishing"></a>Hello\_world 模組訊息發佈

您可以在 ['hello_world.c'][lnk-helloworld-c] 檔案中找到 hello\_world 模組所使用的程式碼來發佈訊息。 下列程式碼片段所示範的修改過程式碼版本已新增註解並移除一些錯誤處理程式碼，以利閱讀︰

```C
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

Hello\_world 模組永遠不會處理其他 IoT Edge 模組發佈至訊息代理程式的訊息。 因此，hello\_world 模組中的訊息回呼實作為無作業函式。

```C
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-processing"></a>記錄器模組訊息處理

Logger 模組會接收來自訊息代理程式的訊息，並將它們寫入檔案。 此模組永遠不會發佈任何訊息。 因此，Logger 模組的程式碼永遠不會呼叫 **Broker_Publish** 函式。

[logger.c][lnk-logger-c] 檔案中的 **Logger_Receive** 函式是訊息代理程式所叫用的回呼，以將訊息傳遞到 Logger 模組。 下列程式碼片段所示範的修改過版本已新增註解並移除一些錯誤處理程式碼，以利閱讀︰

```C
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>後續步驟

在本文中，您所執行的簡單 IoT Edge 閘道可將訊息寫入記錄檔中。 若要執行會將訊息傳送到 IoT 中樞的範例，請參閱：

- [IoT Edge：搭配使用模擬裝置與 Linux 來傳送裝置到雲端訊息][lnk-gateway-simulated-linux] 
- [IoT Edge：搭配使用模擬裝置與 Windows 來傳送裝置到雲端訊息][lnk-gateway-simulated-windows]。


<!-- Links -->
[lnk-main-c]: https://github.com/Azure/iot-edge/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/iot-edge/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/iot-edge/blob/master/modules/logger/src/logger.c
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-gateway-simulated-linux]: ../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md
[lnk-gateway-simulated-windows]: ../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md