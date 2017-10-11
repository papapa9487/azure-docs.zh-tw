---
title: "使用 C# 建立 Azure IoT Edge 模組 | Microsoft Docs"
description: "本教學課程示範如何使用最新的 Azure IoT Edge NuGet 套件、Visual Studio Code 與 C# 撰寫 BLE 資料轉換器模組。"
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: "azure, iot, 教學課程, 模組, nuget, vscode, csharp, edge"
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.openlocfilehash: 7175ffc8de2c043593d61143b402484d33e4a8cc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>使用 C&#x23; 建立 Azure IoT Edge 模組

本教學課程示範如何使用 `Visual Studio Code` 和 `C#` 建立 `Azure IoT Edge` 的模組。

在本教學課程中，我們將逐步說明環境設定，以及如何使用最新的 `Azure IoT Edge NuGet` 套件撰寫 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 資料轉換器模組。 

>[!NOTE]
本教學課程使用支援跨平台相容性的 `.NET Core SDK`。 下列教學課程使用 `Windows 10` 作業系統撰寫。 此教學課程中的某些命令可能因您的 `development environment` 而不同。 

## <a name="prerequisites"></a>必要條件

在本節中，我們會針對 `Azure IoT Edge` 模組開發設定您的環境。 它同時適用於 **64 位元 Windows** 和 **64 位元 Linux (Ubuntu/Debian 8)** 作業系統。

需要下列軟體：

- [Git 用戶端](https://git-scm.com/downloads)
- [.NET Core SDK](https://www.microsoft.com/net/core#windowscmd)
- [Visual Studio Code](https://code.visualstudio.com/)

您不需要針對此範例複製存放庫，但是在本教學課程中討論的所有範例程式碼都位於下列存放庫中：

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`。
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>開始使用

1. 安裝 `.NET Core SDK`。
2. 從 Visual Studio Code Marketplace 安裝 `Visual Studio Code` 和 `C# extension`。

有關如何開始使用 `Visual Studio Code` 和 `.NET Core SDK`，請檢視此[快速影片教學課程](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows)。

## <a name="creating-the-azure-iot-edge-converter-module"></a>建立 Azure IoT Edge 轉換器模組

1. 初始化新 `.NET Core` 類別庫 C# 專案：
    - 開啟命令提示字元 (`Windows + R` -> `cmd` -> `enter`)。
    - 瀏覽到您想要建立 `C#` 專案的資料夾。
    - 輸入 **dotnet new classlib -o IoTEdgeConverterModule -f netstandard1.3**。 
    - 此命令會在專案目錄中建立名為 `Class1.cs` 的空類別。
2. 輸入 **cd IoTEdgeConverterModule**，瀏覽到我們剛剛建立類別庫專案的資料夾。
3. 在 `Visual Studio Code` 中輸入 **code .**，開啟專案。
4. 在 `Visual Studio Code` 中開啟專案後，按一下 **IoTEdgeConverterModule.csproj** 便可如下圖所示開啟檔案：

    ![Visual Studio Code 編輯視窗](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. 在上圖的第六行中，於結尾 `PropertyGroup` 標記和結尾 `Project` 標記之間插入下列程式碼片段所顯示的 `XML` Blob，然後按 `Ctrl` + `S` 儲存檔案。

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. 儲存 `.csproj` 檔案之後，`Visual Studio Code` 應會以 `unresolved dependencies` 對話方塊提示您，如下圖所示： 

    ![Visual Studio Code 還原相依性對話方塊](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) 按一下 `Restore` 可還原專案 `.csproj` 檔案中的所有相依性，包括我們已新增的 `PackageReferences`。 

    b) `Visual Studio Code` 會自動在專案 `obj` 資料夾中建立 `project.assets.json` 檔案。 此檔案包含您專案相依性的相關資訊，可使後續還原速度更快。
 
    >[!NOTE]
    `.NET Core Tools` 現在以 MSBuild 為基礎。 這表示會建立 `.csproj` 專案檔案而非 `project.json`。

    - 如果 `Visual Studio Code` 沒有提示您也沒有關係，我們可以手動執行此動作。 按下 `Ctrl` + `backtick` 鍵或使用功能表 `View` -> `Integrated Terminal`，開啟 `Visual Studio Code` 整合式終端機視窗。
    - 在 `Integrated Terminal` 視窗中輸入 **dotnet restore**。
    
7. 將 `Class1.cs` 檔案重新命名為 `BleConverterModule.cs`。 

    a) 若要重新命名檔案，請先按一下檔案，然後按 `F2` 鍵。
    
    b) 輸入新名稱 **BleConverterModule**，如下圖所示：

    ![重新命名類別的 Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. 將下列程式碼片段複製並貼上到 `BleConverterModule.cs` 檔案中，取代 `BleConverterModule.cs` 檔案中的現有程式碼。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. 按 `Ctrl` + `S` 儲存檔案。

10. 按 `Ctrl` + `N` 鍵建立名為 `Untitled-1` 的新檔案，如下圖所示：

    ![Visual Studio Code 新檔案](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. 若要還原序列化我們從模擬的 `BLE` 裝置收到的 `JSON` 物件，請將下列程式碼複製到 `Untitled-1` 檔案程式碼編輯器視窗。 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. 按 `Ctrl` + `Shift` + `S` 鍵將檔案儲存為 `BleData.cs`。
    - 在另存新檔對話方塊的 `Save as Type` 下拉式功能表中，選取 `C# (*.cs;*.csx)`，如下圖所示：

    ![Visual Studio Code 另存新檔對話方塊](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. 按 `Ctrl` + `N` 鍵建立名為 `Untitled-1` 的新檔案。

14. 將以下程式碼片段複製並貼上到 `Untitled-1` 檔案中。 這個類別是 `Azure IoT Edge` 模組，我們會使用它來輸出從 `BleConverterModule` 接收的資料。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. 按 `Ctrl` + `Shift` + `S` 將檔案另存為 `DotNetPrinterModule.cs`。
    - 在另存新檔對話方塊的 `Save as Type` 下拉式功能表中，選取 `C# (*.cs;*.csx)`。

16. 按 `Ctrl` + `N` 鍵建立名為 `Untitled-1` 的新檔案。

17. 若要還原序列化我們從 `BleConverterModule` 收到的 `JSON` 物件，請將以下程式碼片段複製並貼上到 `Untitled-1` 檔案中。 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. 按 `Ctrl` + `Shift` + `S` 將檔案另存為 `BleConverterData.cs`。
    - 在另存新檔對話方塊的 `Save as Type` 下拉式功能表中，選取 `C# (*.cs;*.csx)`。

19. 按 `Ctrl` + `N` 鍵建立名為 `Untitled-1` 的新檔案。

20. 將以下程式碼片段複製並貼上到 `Untitled-1` 檔案中。

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. 按 `Ctrl` + `Shift` + `S` 將檔案另存為 `gw-config.json`。
    - 在另存新檔對話方塊的 `Save as Type` 下拉式功能表中，選取 `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`。

22. 若要啟用將組態檔複製到輸出目錄，請將 `IoTEdgeConverterModule.csproj` 更新為下列 XML blob：

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - 更新後的 `IoTEdgeConverterModule.csproj` 看起來應該如下圖所示：

    ![Visual Studio Code 更新後的 .csproj 檔案](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. 按 `Ctrl` + `N` 鍵建立名為 `Untitled-1` 的新檔案。

24. 將以下程式碼片段複製並貼上到 `Untitled-1` 檔案中。

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. 按 `Ctrl` + `Shift` + `S` 將檔案另存為 `binplace.ps1`。
    - 在另存新檔對話方塊的 `Save as Type` 下拉式功能表中，選取 `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`。

26. 按 `Ctrl` + `Shift` + `B` 鍵建置專案。 當您第一次建置專案時，`Visual Studio Code` 會以 `No build task defined.` 對話方塊提示您，如下圖所示：

    ![Visual Studio Code 建置工作對話方塊](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) 按一下 `Configure Build Task` 按鈕。

    b) 在 `Select a Task Runner` 對話方塊下拉式功能表中。 選取 `.NET Core`，如下圖所示： 

    ![Visual Studio Code 選取工作對話方塊](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) 按一下 `.NET Core` 項目可在 `.vscode` 目錄中建立 `tasks.json` 檔案，並在 `code editor` 視窗中開啟該檔案。 這個檔案不需修改，請關閉索引標籤。

27.  按 `Ctrl` + `backtick` 鍵或使用功能表 `View` -> `Integrated Terminal` 開啟 `Visual Studio Code` 整合式終端機視窗，並將 **.\binplace.ps1** 輸入到 `PowerShell` 命令提示字元。 此命令會將我們的所有相依性複製到輸出目錄。

28. 輸入 **cd .\bin\Debug\netstandard1.3**，瀏覽到 `Integrated Terminal` 視窗中的專案輸出目錄。

29. 將 **.\gw.exe gw-config.json** 輸入到 `Integrated Terminal` 視窗提示中，執行範例專案。 
    - 如果您嚴格遵照本教學課程中的步驟執行，現在應正執行 `Azure IoT Edge BLE Data Converter Module` 範例專案，如下圖所示：
    
        ![在 Visual Studio Code 中執行的模擬裝置範例](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - 如果您想要終止應用程式，請按 `<Enter>` 鍵。

>[!IMPORTANT]
不建議使用 `Ctrl` + `C` 來終止 `IoT Edge` 閘道應用程式 (即 **gw.exe**)。 因為此動作可能會造成程序異常終止。

