---
title: "Azure IoT Edge C# 模組 | Microsoft Docs"
description: "使用 C# 程式碼建立 IoT Edge 模組並將它部署到邊緣裝置"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b8afc266cd416f39a895285d05b8ff323fb46330
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>開發 C# IoT Edge 模組並部署到您的模擬裝置 - 預覽

您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 您將會使用模擬的 IoT Edge 裝置，其建立方法詳述於[在 Windows 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-win]，以及[在 Linux 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-lin] 這兩個教學課程中。 在本教學課程中，您將了解如何：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 建立以 .NET core 2.0 為基礎的 IoT Edge 模組
> * 使用 Visual Studio Code 和 Docker 建立 Docker 映像，並將其發行至您的登錄中 
> * 將模組部署到您的 IoT Edge 裝置
> * 檢視產生的資料


您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您的裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。 

## <a name="prerequisites"></a>必要條件

* 您在快速入門或第一份教學課程中所建立的 Azure IoT Edge 裝置。
* IoT Edge 裝置的主索引鍵連接字串。  
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
* 在具有 Visual Studio Code 的電腦上也已安裝 [Docker](https://docs.docker.com/engine/installation/) \(英文\)。 針對本教學課程，使用 Community Edition (CE) 便已足夠。 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="create-a-container-registry"></a>建立容器登錄庫
在本教學課程中，您會使用適用於 VS Code 的 Azure IoT Edge 擴充功能來建置模組，並從檔案建立**容器映像**。 接著，您會將此映像推送至儲存並管理映像的**登錄**。 最後，您會從登錄部署該映像，以在 IoT Edge 裝置上執行。  

您可以此教學課程中使用任何與 Docker 相容的登錄。 於雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) \(英文\)。 本教學課程使用的是 Azure Container Registry。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源] > [容器] > [Azure Container Registry]。
2. 為登錄提供名稱，選擇訂用帳戶，選擇資源群組，然後將 SKU 設定為 [基本]。 
3. 選取 [ **建立**]。
4. 建立容器登錄之後，請瀏覽至它並選取 [存取金鑰]。 
5. 將 [管理使用者] 切換為 [啟用]。
6. 複製 [登入伺服器]、[使用者名稱] 及 [密碼] 的值。 您在本教學課程後續的內容中將會用到這些值。 

## <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案
下列步驟會示範如何 使用 Visual Studio Code 和 Azure IoT Edge 擴充功能，來建立以 .NET core 2.0 為基礎的 IoT Edge 模組。
1. 在 Visual Studio Code 中，選取 [檢視] > [整合式終端機] 以開啟 VS Code 整合式終端機。
3. 在整合式終端機中，輸入下列命令來安裝 (或更新) dotnet 中的 **AzureIoTEdgeModule** 範本：

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. 針對新模組建立專案。 下列命令會在目前的工作資料夾中建立專案資料夾 **FilterModule**：

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. 選取 [檔案] > [開啟資料夾]。
4. 瀏覽至 **FilterModule** 資料夾，然後按一下 [選取資料夾] 以在 VS Code 中開啟專案。
5. 在 VS Code 檔案總管中，按一下 **Program.cs** 來將其開啟。

   ![開啟 Program.cs][1]

6. 將 `temperatureThreshold` 變數新增至 **Program** 類別。 此變數會設定在將資料傳送至 IoT 中樞之前，測量的溫度必須超過的值。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. 將 `MessageBody`、`Machine` 及 `Ambient` 類別新增至 **Program** 類別。 這些類別會定義內送郵件本文的預期結構描述。

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

8. 在 **Init** 方法中，該程式碼會建立並設定 **DeviceClient** 物件。 此物件可允許模組連線至本機的 Azure IoT Edge 執行階段，以便傳送和接收訊息。 用於 **Init** 方法的連接字串，將會由 IoT Edge 執行階段提供給模組。 建立 **DeviceClient** 之後，程式碼會註冊回呼以透過 **input1** 端點接收來自 IoT Edge 中樞的訊息。 將 `SetInputMessageHandlerAsync` 方法取代為新的版本，然後針對所需的屬性更新新增 `SetDesiredPropertyUpdateCallbackAsync` 方法。 若要進行這項變更，請使用下列程式碼取代 **Init** 方法的最後一行：

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. 將 `onDesiredPropertiesUpdate` 方法新增至 **Program** 類別。 此方法會從模組對應項接收所需的屬性，並會更新 **temperatureThreshold** 變數以符合該屬性。 所有模組都具有自己的模組對應項，這可讓您直接從雲端設定於模組內執行的程式碼。

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

10. 使用 `FilterMessages` 方法來取代 `PipeMessage` 方法。 每當模組從 IoT Edge 中樞接收到訊息時，就會呼叫此方法。 它會篩選所報告溫度低於 (透過模組對應項所設定) 之溫度閾值的訊息。 針對具有設定為 [警示] 之值的訊息，此方法也會將 **MessageType** 屬性新增至該訊息。 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. 若要建置專案，請在檔案總管中以滑鼠右鍵按一下 **FilterModule.csproj** 檔案，然後按一下 [建置 IoT Edge 模組]。 此程序會編譯模組，並將二進位檔及其相依性匯出至用於建立 Docker 映像的資料夾中。

   ![建置 IoT Edge 模組][2]

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>建立 Docker 映像並發行到您的登錄中

1. 在 VS Code 檔案總管中，展開 [Docker] 資料夾。 然後展開適用於您容器平台的資料夾：**linux-x64** 或 **windows-nano**。

   ![選取 Docker 容器平台][3]

2. 以滑鼠右鍵按一下 [Dockerfile] 檔案，然後按一下 [建置 IoT Edge 模組的 Docker 映像]。 
3. 在 [選取資料夾] 視窗中，瀏覽至或是輸入 `./bin/Debug/netcoreapp2.0/publish`。 按一下 [選取資料夾為 EXE_DIR]。
4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如： `<your container registry address>/filtermodule:latest`。 容器登錄位址與您從登錄複製的登入伺服器相同。 其格式應該是 `<your container registry name>.azurecr.io`。
5. 透過在 VS Code 整合式終端機中輸入下列命令來登入 Docker： 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   使用您在建立 Azure 容器登錄時從中複製的使用者名稱、密碼及登入伺服器。

3. 將映像推送到您的 Docker 存放庫。 選取 [檢視] > [命令選擇區]，然後搜尋 [Edge: 推送 IoT Edge 模組 Docker 映像] 功能表命令。 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 使用您在步驟 1.d 中使用的相同映像名稱。

## <a name="add-registry-credentials-to-edge-runtime"></a>將登錄認證新增至 Edge 執行階段
在執行 Edge 裝置的電腦上，將登錄的認證新增至 Edge 執行階段。 這些認證會將提取容器的存取權提供給執行階段。 

- 針對 Windows，請執行下列命令：
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- 針對 Linux，請執行下列命令：
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>執行解決方案

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 IoT 中樞。
2. 移至 [IoT Edge (預覽)] 並選取您的 IoT Edge 裝置。
3. 選取 [設定模組]。 
2. 檢查 **tempSensor** 模組是否已自動填入。 如果沒有，請使用下列步驟來新增它：
    1. 選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `tempSensor`。
    3. 在 [映像 URI] 欄位中，輸入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 其他設定保留不變，然後按一下 [儲存]。
9. 新增您在先前小節中建立的 **filterModule** 模組。 
    1. 選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `filterModule`。
    3. 在 [映像 URI] 欄位中，輸入您的映像位址，例如 `<your container registry address>/filtermodule:latest`。
    4. 選取 [啟用] 方塊以編輯模組對應項。 
    5. 使用下列 JSON 取代模組對應項文字方塊中的 JSON： 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. 按一下 [儲存] 。
12. 按一下 [下一步] 。
13. 在 [指定路由] 步驟中，將下列 JSON 複製到文字方塊。 模組會將所有訊息發佈到 Edge 執行階段。 執行階段中的宣告式規則會定義訊息的流向。 在本教學課程中，您需要兩個路由。 第一個路由會透過使用 **FilterMessages** 處理常式設定的 "input1" 端點，將訊息從溫度感應器傳輸至篩選模組。 第二個路由會將訊息從篩選模組傳輸到 IoT 中樞。 在此路由中，`upstream` 是告知 Edge 中樞將訊息傳送至 IoT 中樞的特殊目的地。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. 按一下 [下一步] 。
5. 在 [檢閱範本] 步驟中，按一下 [提交]。 
6. 返回 IoT Edge 裝置的詳細資料頁面，按一下 [重新整理]。 您應該會看到新的 **filtermodule** 正在與 **tempSensor** 模組和 **IoT Edge 執行階段**一起執行。 

## <a name="view-generated-data"></a>檢視產生的資料

監視從 IoT Edge 裝置傳送到 IoT 中樞的「裝置到雲端」訊息：
1. 使用 IoT 中樞連接字串來設定 Azure IoT Toolkit 擴充功能： 
    1. 選取 [檢視] > [檔案總管] 來開啟 VS Code 檔案總管。 
    3. 在檔案總管中，按一下 [IOT HUB DEVICES]，然後按一下 [...]。按一下 [設定 IoT 中樞連接字串]，然後在快顯視窗中，輸入與您 IoT Edge 裝置連線的 IoT 中樞連接字串。 

        若要尋找連接字串，在 Azure 入口網站中按一下 IoT 中樞圖格，然後按一下 [共用存取原則]。 在 [共用存取原則] 中，按一下 [iothubowner] 原則，然後複製 [iothubowner] 視窗中的 IoT 中樞連接字串。   

1. 若要監視抵達 IoT 中樞的資料，請選取 [檢視] > [命令選擇區]，然後搜尋 [IoT: 開始監視 D2C 訊息] 功能表命令。 
2. 若要停止監視資料，請使用 [IoT:停止監視 D2C 訊息] 功能表命令。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。 您可以繼續閱讀下列任何教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [將 Azure Functions 部署為模組](tutorial-deploy-function.md)
> [將 Azure 串流分析部署為模組](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
