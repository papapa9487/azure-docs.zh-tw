---
title: "自訂預先設定解決方案 | Microsoft Docs"
description: "提供如何自訂 Azure IoT 套件預先設定解決方案的指引。"
services: 
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: corywink
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: bdf4cd89d5ad0392337dfe761108608d506adf18
ms.contentlocale: zh-tw
ms.lasthandoff: 05/15/2017

---
# <a name="customize-a-preconfigured-solution"></a>自訂預先設定的方案

Azure IoT Suite 提供的預先設定解決方案能夠示範套件中共同運作的服務可提供端對端解決方案。 從這裡開始，有多個不同地方可以擴充並針對特定案例自訂解決方案。 下列各節說明這些常見的自訂點。

## <a name="find-the-source-code"></a>尋找原始程式碼

預先設定解決方案的原始程式碼可在以下 GitHub 的儲存機制取得：

* 遠端監視： [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* 預測性維護︰ [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* 已連線的處理站︰[https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

提供預先設定解決方案原始程式碼的目的，在於示範實作使用 Azure IoT 套件之 IoT 解決方案的端對端功能時，所採用的模式和作法。 您可以找到如何在 GitHub 儲存機制中建置和部署解決方案的詳細資訊。

## <a name="change-the-preconfigured-rules"></a>變更預先設定的規則

遠端監視解決方案包含三個 [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)作業，這些作業可處理解決方案中的裝置資訊、遙測及規則邏輯。

[遠端監視預先設定解決方案逐步解說](iot-suite-remote-monitoring-sample-walkthrough.md)提供這三個串流分析作業和其語法的深入描述。 

您可以直接編輯這些工作以改變邏輯，或新增案例特有的邏輯。 您可以尋找串流分析工作，如下所示︰

1. 移至 [[Azure 入口網站] ](https://portal.azure.com) 。
2. 瀏覽至名稱與 IoT 解決方案相同的資源群組。 
3. 選取要修改的 Azure 串流分析作業。 
4. 在命令集中選取 [停止] 以停止作業。 
5. 編輯輸入、查詢及輸出。
   
    簡單修改的目的在於變更**規則**作業的查詢，以便使用 **"<"** 而不是 **">"**。 編輯規則時，解決方案入口網站仍會顯示 **">"**，不過因為基礎作業中的變更，您可以發現行為已翻轉。
6. 啟動工作

> [!NOTE]
> 遠端監視儀表板依賴特定資料，因此變更工作可能會造成儀表板失敗。

## <a name="add-your-own-rules"></a>新增自己的規則

除了變更預先設定的 Azure 串流分析工作，您也可以使用 Azure 入口網站新增工作或新增現有工作的查詢。

## <a name="customize-devices"></a>自訂裝置

最常見的擴充功能活動之一是使用案例特定的裝置。 使用裝置的方法有數種。 這些方法包括變更模擬裝置以符合您的案例，或使用 [IoT Device SDK][IoT Device SDK] 將實體裝置連接到解決方案。

如需新增裝置的逐步指南，請參閱 [Iot 套件連接裝置](iot-suite-connecting-devices.md)一文和[遠端監視 C SDK 範例](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring)。 此範例設計用於搭配預先設定的遠端監視解決方案。

### <a name="create-your-own-simulated-device"></a>建立自己的模擬裝置

[遠端監視解決方案原始程式碼](https://github.com/Azure/azure-iot-remote-monitoring)中包含 .NET 模擬器。 此模擬器是解決方案中佈建的模擬器，您可加以變更，以傳送不同的中繼資料、遙測，以及回應不同的命令和方法。

預先設定的遠端監視解決方案中預先設定的模擬器會模擬可發出溫度和濕度遙測的冷卻裝置。 當您分接 GitHub 儲存機制後，可以在 [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) 專案中修改模擬器。

### <a name="available-locations-for-simulated-devices"></a>模擬裝置的可用位置

預設的一組位置是在美國華盛頓州的西雅圖市/雷德蒙德市。 您可以在 [SampleDeviceFactory.cs][lnk-sample-device-factory] 變更這些位置。

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>將所需屬性更新處理常式新增至模擬器

您可以在解決方案入口網站中設定裝置的所需屬性值。 當裝置擷取所需屬性值，裝置必須處理屬性變更要求。 若要透過所需屬性新增屬性值變更支援，您需要將處理常式新增至模擬器。

此模擬器包含 **SetPointTemp** 和 **TelemetryInterval** 屬性的處理常式，而您可以在解決方案入口網站中設定這些屬性的所需值來加以更新。

下列範例顯示 **CoolerDevice** 類別中 **SetPointTemp** 所要屬性的處理常式︰

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

這個方法會更新遙測點溫度，然後藉由設定報告屬性向 IoT 中樞回報變更。

您可以依照上述範例中的模式，為自己的屬性新增自己的處理常式。

您也必須將所需屬性從 **CoolerDevice** 建構函式繫結至如下列範例所示的處理常式︰

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

請注意，**SetPointTempPropertyName** 是定義為 "Config.SetPointTemp" 的常數。

### <a name="add-support-for-a-new-method-to-the-simulator"></a>將新方法支援新增至模擬器

您可以自訂模擬器，以新增[方法 (直接方法)][lnk-direct-methods] 支援。 有兩個必備主要步驟︰

- 模擬器必須將方法的詳細資料告知預先設定之解決方案中的 IoT 中樞。
- 模擬器必須包含程式碼，以便在您從解決方案總管的 [裝置詳細資料] 面板或透過作業叫用方法時，處理方法呼叫。

預先設定的遠端監視解決方案會使用*報告屬性*，將所支援方法的詳細資料傳送至 IoT 中樞。 解決方案後端會維護每個裝置支援的所有方法清單，以及方法引動過程的歷程記錄。 您可以檢視有關裝置的這項資訊，並且在解決方案入口網站中叫用方法。

若要通知 IoT 中樞，裝置可支援某種方法，裝置必須將此方法的詳細資訊新增至報告屬性中的 **SupportedMethods** 節點︰

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

方法簽章具有下列格式︰`<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`。 例如，若要指定預期有名為 **FwPackageURI** 之字串參數的 **InitiateFirmwareUpdate** 方法，請使用下列方法簽章︰

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

如需支援的參數類型清單，請參閱 Infrastructure 專案中的 **CommandTypes** 類別。

若要刪除方法，將報告屬性中的方法簽章設定為 `null`。

> [!NOTE]
> 當解決方案後端從裝置收到「裝置資訊」訊息時，只會更新所支援方法的相關資訊。

下列來自 Common 專案中 **SampleDeviceFactory** 類別的程式碼範例示範如何將方法新增至裝置所傳送之報告屬性的 **SupportedMethods** 清單：

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

此程式碼片段會新增 **InitiateFirmwareUpdate** 方法的詳細資料，包括要在解決方案入口網站中顯示的文字以及所需方法參數的詳細資料。

模擬器會在啟動時將報告屬性 (包括支援的方法清單) 傳送至 IoT 中樞。

針對模擬器支援的每個方法，將處理常式新增至模擬器程式碼。 您可以在 Simulator.WebJob 專案的 **CoolerDevice** 類別中看到現有的處理常式。 下列範例顯示 **InitiateFirmwareUpdate** 方法的處理常式︰

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

方法處理常式名稱的開頭必須是 `On` 後面加上方法名稱。 **MethodRequest** 參數包含從解決方案後端叫用方法時所傳遞的任何參數。 傳回值的類型必須是 **Task&lt;MethodResponse&gt;**。 **BuildMethodResponse** 公用程式方法可協助您建立傳回值。

在方法處理常式中，您可以︰

- 啟動非同步工作。
- 從 IoT 中樞的*裝置對應項*擷取所需屬性。
- 使用 **CoolerDevice** 類別中的 **SetReportedPropertyAsync** 方法，更新單一報告屬性。
- 建立 **TwinCollection** 執行個體並呼叫 **Transport.UpdateReportedPropertiesAsync** 方法，以更新多個報告屬性。

先前的韌體更新範例會執行下列步驟：

- 檢查裝置能夠接受韌體更新要求。
- 以非同步方式起始韌體更新作業，並在作業完成時重設遙測。
- 立即傳回「已接受 FirmwareUpdate」訊息，表示裝置已接受此要求。

### <a name="build-and-use-your-own-physical-device"></a>建置並使用自己的 (實體) 裝置

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 提供用來將各種裝置類型 (語言和作業系統) 連接至 IoT 解決方案中的程式庫。

## <a name="modify-dashboard-limits"></a>修改儀表板限制

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>儀表板下拉式清單中顯示的裝置數目

預設值為 200。 您可以在 [DashboardController.cs][lnk-dashboard-controller] 變更這個數字。

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Bing 地圖控制項中要顯示的釘選數目

預設值為 200。 您可以在 [TelemetryApiController.cs][lnk-telemetry-api-controller-01] 變更這個數字。

### <a name="time-period-of-telemetry-graph"></a>遙測圖形的期間

預設值是 10 分鐘。 您可以在 [TelmetryApiController.cs][lnk-telemetry-api-controller-02] 變更此值。

## <a name="manually-set-up-application-roles"></a>手動設定應用程式角色

以下程序描述如何將 **Admin** 和 **ReadOnly** 應用程式角色新增至預先設定的解決方案中。 請注意，從 azureiotsuite.com 網站佈建的預先設定解決方案已經包含 **Admin** 和 **ReadOnly** 角色。

**ReadOnly** 角色的成員可以看到儀表板和裝置清單，但不能加入裝置、變更裝置屬性或傳送命令。  **Admin** 角色的成員具有解決方案中所有功能的完整存取權。

1. 前往 [Azure 傳統入口網站][lnk-classic-portal]。
2. 選取 **Active Directory**。
3. 按一下您在佈建解決方案時所使用的 AAD 租用戶名稱。
4. 按一下 [應用程式] 。
5. 按一下符合預先設定之方案名稱的應用程式名稱。 如果清單中看不到您的應用程式，請選取 [顯示] 下拉式清單中的 [我公司所擁有的應用程式]，然後按一下核取記號。
6. 在頁面底部，按一下 [管理資訊清單]，然後按一下 [下載資訊清單]。
7. 這個程序會將 .json 檔案下載到本機電腦。 使用您選擇的文字編輯器開啟此檔案並加以編輯。
8. 在 .json 檔案的第三行，您會看到︰

   ```json
   "appRoles" : [],
   ```
   使用以下程式碼來取代這一行：

   ```json
   "appRoles": [
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Administrator access to the application",
   "displayName": "Admin",
   "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
   "isEnabled": true,
   "value": "Admin"
   },
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Read only access to device information",
   "displayName": "Read Only",
   "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
   "isEnabled": true,
   "value": "ReadOnly"
   } ],
   ```

9. 儲存更新後的.json 檔案 (可以覆寫現有的檔案)。
10. 在 Azure 傳統入口網站中，選取頁面底部的 [管理資訊清單]，然後選取 [上傳資訊清單] 上傳您在上一個步驟儲存的 .json 檔案。
11. 您現在已為您的應用程式新增 **Admin** 和 **ReadOnly** 角色。
12. 若要將其中一個角色指派給您目錄中的使用者，請參閱 [azureiotsuite.com 網站的權限][lnk-permissions]。

## <a name="feedback"></a>意見反應

本文件是否涵蓋您感興趣的自訂內容？ 請在 [User Voice (使用者心聲)](https://feedback.azure.com/forums/321918-azure-iot) 中加入功能建議，或在本文留言。 

## <a name="next-steps"></a>後續步驟

若要深入了解自訂預先設定的解決方案的選項，請參閱︰

* [將邏輯應用程式連接至 Azure IoT 套件遠端監視預先設定解決方案][lnk-logicapp]
* [搭配使用動態遙測與遠端監視預先設定解決方案][lnk-dynamic]
* [遠端監視預先設定方案中的裝置資訊中繼資料][lnk-devinfo]
* [自訂連線工廠解決方案顯示 OPC UA 伺服器資料的方式][lnk-cf-customize]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]: iot-suite-connected-factory-customize.md
