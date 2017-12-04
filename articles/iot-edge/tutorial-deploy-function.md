---
title: "使用 Azure IoT Edge部署 Azure Function | Microsoft Docs"
description: "將 Azure Function 作為模組部署至 Edge 裝置"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cba901e8929d3626dc06e4600437b6d364e9bc44
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>將 Azure Function 部署為 IoT Edge 模組 - 預覽
您可以使用 Azure Functions 來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您建立及部署 Azure Function，以篩選模擬 IoT Edge 裝置上的感應器資料 (模擬裝置是在＜在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 上的模擬裝置中部署 Azure IoT Edge＞教學課程中所建立)。 在本教學課程中，您將了解如何：     

> [!div class="checklist"]
> * 使用 Visual Studio Code 來建立 Azure Function
> * 使用 VS Code 和 Docker 建立 Docker 映像，並將其發行至您的登錄中 
> * 將模組部署到您的 IoT Edge 裝置
> * 檢視產生的資料


您在本教學課程中建立的 Azure Function 會篩選由您裝置產生的溫度資料，而且只會在溫度高於指定臨界值時，將訊息上游傳送到 Azure IoT 中樞。 

## <a name="prerequisites"></a>必要條件

* 您在快速入門或上一個教學課程中所建立的 Azure IoT Edge 裝置。
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 (您可以從 Visual Studio Code 中的擴充功能面板上安裝擴充功能。)
* [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 (您可以從 Visual Studio Code 中的擴充功能面板上安裝擴充功能。)
* [Docker](https://docs.docker.com/engine/installation/)。 在本教學課程中，使用 Community Edition (CE) 作為您的平台是可行的。 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="set-up-a-docker-registry"></a>設定 Docker Registry
在本教學課程中，您會使用 VS Code 的 Azure IoT Edge 擴充功能來建立 Azure Function，並使用它來建置 [Docker 映像](https://docs.docker.com/glossary/?term=image)。 然後將此 Docker 映像推送至 [Docker 登錄](https://docs.docker.com/glossary/?term=registry)所裝載的 [Docker 存放庫](https://docs.docker.com/glossary/?term=repository)。 最後，您會將封裝為 [Docker 容器](https://docs.docker.com/glossary/?term=container) 的 Docker 映像從您的登錄部署至 IoT Edge 裝置。  

您可以此教學課程中使用任何與 Docker 相容的登錄。 可在雲端中使用的兩個常用 Docker 登錄服務為 **Azure Container Registry** 和 **Docker Hub**：

- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/) 適用於[付費的訂用帳戶](https://azure.microsoft.com/en-us/pricing/details/container-registry/)。 在本教學課程中，使用**基本**訂用帳戶即可。 

- 如果您註冊 Docker 識別碼 (免費)，[Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) 即可提供一個私人存放庫。 
    1. 若要註冊 Docker 識別碼，請在 Docker 網站上遵循[註冊 Docker 識別碼](https://docs.docker.com/docker-id/#register-for-a-docker-id)中的指示。 

    2. 若要建立私人 Docker 存放庫，請在 Docker 網站上遵循[在 Docker Hub 上建立新存放庫](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub)中的指示。

在本教學課程中，提供的命令將適用於 Azure Container Registry 和 Docker Hub (如有適用狀況)。

## <a name="create-a-function-project"></a>建立函式專案
下列步驟會示範如何使用 Visual Studio Code 和 Azure IoT Edge 擴充功能來建立 IoT Edge 函式。
1. 開啟 VS Code。
2. 使用**檢視 | 整合式終端機**功能表命令，開啟 VS Code 整合式終端機。
3. 在整合式終端機中，輸入下列命令來安裝 (或更新) dotnet 中的 **AzureIoTEdgeFunction** 範本：

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. 在整合式終端機中，輸入下列命令來建立新的模組專案：

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > 此命令會在目前的工作資料夾中建立專案資料夾 **FilterFunction**。 如果您需要建立在另一個位置中，請先變更目錄再執行此命令。

3. 使用**檔案 |開啟資料夾**功能表命令，瀏覽至 **FilterFunction** 資料夾，然後按一下 [選取資料夾]，在 VS Code 中開啟專案。
4. 在 VS Code 檔案總管中，按一下 [EdgeHubTrigger-Csharp] 資料夾，然後按一下 [run.csx] 檔案將它開啟。
5. 在 `#r "Microsoft.Azure.Devices.Client"` 陳述式後方新增下列陳述式：

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. 使用現有 `using` 陳述式後方的陳述式新增下列項目：

    ```csharp
    using Newtonsoft.Json;
    ```

1. 新增下列類別。 這些類別會定義內送郵件本文的預期結構描述。

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

1. 將 **Run** 方法取代為下列程式碼。 它會以訊息本文中的溫度值及溫度臨界值作為基礎，來篩選訊息。

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. 儲存檔案。

## <a name="publish-a-docker-image"></a>發佈 Docker 映像

1. 建置 Docker 映像。
    1. 在 VS Code 檔案總管中，按一下 [Docker] 資料夾來將其開啟。 然後為您的容器平台選取資料夾，**linux-x64** 或 **windows-nano**。 
    2. 以滑鼠右鍵按一下 [Dockerfile] 檔案，然後按一下 [建置 IoT Edge 模組的 Docker 映像]。 
    3. 在 [選取資料夾] 方塊中，瀏覽至專案資料夾 [FilterFunction]，然後按一下 [選取資料夾作為 EXE_DIR]。 
    4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如，`<docker registry address>/filterfunction:latest`；其中「Docker 登錄位址」 是 Docker 識別碼 (如果您使用 Docker Hub)，或類似於 `<your registry name>.azurecr.io` (如果您使用 Azure Container Registry)。
 
4. 登入 Docker。 在整合式終端機中，輸入下列命令： 

    - Docker Hub (出現提示時，請輸入您的認證)：
        
        ```csh/sh
        docker login
        ```

    - Azure 容器登錄：
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        若要尋找使用者名稱、密碼及登入伺服器，以在此命令中使用，請移至 [Azure 入口網站] (https://portal.azure.com)。 從 [所有資源] 中，按一下 Azure 容器登錄圖格以開啟其內容，然後按一下 [存取金鑰]。 複製 [使用者名稱]、[密碼] 和 [登入伺服器] 欄位中的值。 登入伺服器的格式應是：`<your registry name>.azurecr.io`。

3. 將映像推送到您的 Docker 存放庫。 使用**檢視 | 命令選擇區... | Edge：推送 IoT Edge 模組 Docker 映像**功能表命令，並在 VS Code 視窗頂端的快顯文字方塊中輸入映像名稱。 使用步驟 1.c 中使用的相同名稱。

## <a name="add-registry-credentials-to-your-edge-device"></a>將登錄認證新增至 Edge 裝置
在執行 Edge 裝置的電腦上，將登錄的認證新增至 Edge 執行階段。 這會提供存取權給執行階段，以提取容器。 

- 針對 Windows，請執行下列命令：
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- 針對 Linux，請執行下列命令：
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>執行解決方案

1. 在 **Azure 入口網站**中，瀏覽至您的 IoT 中樞。
2. 移至 [IoT Edge (預覽)] 並選取您的 IoT Edge 裝置。
1. 選取 [設定模組]。 
2. 新增 **tempSensor** 模組。 僅在您先前沒有將 **tempSensor** 模組部署到 IoT Edge 裝置，才需要執行此步驟。
    1. 選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `tempSensor`。
    3. 在 [映像 URI] 欄位中，輸入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 其他設定保留不變，然後按一下 [儲存]。
1. 新增 **filterfunction** 模組。
    1. 再次選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `filterfunction`。
    3. 在 [映像] 欄位中，輸入您的映像地址，例如`<docker registry address>/filterfunction:latest`。
    74. 按一下 [儲存] 。
2. 按一下 [下一步] 。
3. 在 [指定路由] 步驟中，將下列 JSON 複製到文字方塊。 模組會將所有訊息發佈到 Edge 執行階段。 執行階段中的宣告式規則會定義這些訊息的流向。 在本教學課程中，您需要兩個路由。 第一個路由會透過使用 **FilterMessages** 處理常式設定的 "input1" 端點，將訊息從溫度感應器傳輸至篩選模組。 第二個路由會將訊息從篩選模組傳輸到 IoT 中樞。 在此路由中，`upstream` 是告知 Edge 中樞將訊息傳送至 IoT 中樞的特殊目的地。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. 按一下 [下一步] 。
5. 在 [檢閱範本] 步驟中，按一下 [提交]。 
6. 返回 IoT Edge 裝置的詳細資料頁面，按一下 [重新整理]。 您應該會看到新的 **filterfunction** 正在與 **tempSensor** 模組和 **IoT Edge 執行階段**一起執行。 

## <a name="view-generated-data"></a>檢視產生的資料

監視從 IoT Edge 裝置傳送到 IoT 中樞的「裝置到雲端」訊息：
1. 使用 IoT 中樞連接字串來設定 Azure IoT Toolkit 擴充功能： 
    1. 使用**檢視 | 檔案總管**功能表命令來開啟 VS Code 檔案總管。 
    3. 在檔案總管中，按一下 [IOT HUB DEVICES]，然後按一下 [...]。按一下 [設定 IoT 中樞連接字串]，然後在快顯視窗中，輸入與您 IoT Edge 裝置連線的 IoT 中樞連接字串。 

        若要尋找連接字串，在 Azure 入口網站中按一下 IoT 中樞圖格，然後按一下 [共用存取原則]。 在 [共用存取原則] 中，按一下 [iothubowner] 原則，然後複製 [iothubowner] 視窗中的 IoT 中樞連接字串。   

1. 若要監視抵達 IoT 中樞的資料，請使用**檢視 |命令選擇區...| IoT：開始監視 D2C 訊息**功能表命令。 
2. 若要停止監視資料，請使用**檢視 |命令選擇區...| IoT：停止監視 D2C 訊息**功能表命令。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 Azure Function，可篩選您 IoT Edge 裝置所產生的原始資料。 若要保持瀏覽 Azure IoT Edge，請了解如何使用 IoT Edge 裝置作為閘道。 

> [!div class="nextstepaction"]
> [建立 IoT Edge 閘道裝置](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
