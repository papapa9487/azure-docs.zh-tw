---
title: "在 Azure IoT 套件中建立自訂規則 | Microsoft Docs"
description: "如何在 IoT 套件所預先設定的解決方案中建立自訂規則。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bf2a13035de141766fd935966ce18459dccdaab
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>在遠端監視預先設定的解決方案中建立自訂規則

## <a name="introduction"></a>簡介

在預先設定的解決方案中，您可以設定[會在裝置的遙測值觸達特定臨界值時觸發的規則][lnk-builtin-rule]。 [搭配使用動態遙測與遠端監視預先設定解決方案][lnk-dynamic-telemetry]會說明如何在解決方案中新增自訂遙測值，例如 ExternalTemperature。 本文則會示範如何在解決方案中建立動態遙測類型的自訂規則。

本教學課程使用簡單的 Node.js 模擬裝置，來產生要傳送至預先設定之解決方案後端的動態遙測。 然後，您要在 **RemoteMonitoring** Visual Studio 解決方案中新增自訂規則，並將此自訂後端部署至 Azure 訂用帳戶。

若要完成本教學課程，您需要：

* 有效的 Azure 訂用帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk_free_trial]。
* [Node.js][lnk-node] 0.12.x 版或更新版本，以建立模擬裝置。
* Visual Studio 2015 或 Visual Studio 2017，以使用新規則修改預先設定的解決方案後端。

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

請記下您為部署選擇的解決方案名稱。 本教學課程稍後需要用到此解決方案名稱。

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

當您確認 Node.js 主控台應用程式會將 **ExternalTemperature** 遙測傳送至預先設定的解決方案時，便可將其停止。 在將自訂規則新增至解決方案後，您會再次執行此 Node.js 主控台應用程式，因此請將主控台視窗保持開啟狀態。

## <a name="rule-storage-locations"></a>規則的儲存位置

規則的相關資訊會保存在兩個位置︰

* **DeviceRulesNormalizedTable** 資料表 – 此資料表會儲存解決方案入口網站所定義之規則的標準化參考。 解決方案入口網站在顯示裝置規則時，會查詢此資料表以取得規則定義。
* **DeviceRules** Blob – 此 Blob 會儲存所有註冊裝置已定義的所有規則，並且會定義為 Azure 串流分析作業的參考輸入。
 
當您在解決方案入口網站中更新現有規則或定義新規則時，資料表和 Blob 皆會更新，以反映所做的變更。 入口網站中顯示的規則定義來自資料表存放區，串流分析作業所參考的規則定義則來自 Blob。 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>更新 RemoteMonitoring Visual Studio 解決方案

下列步驟說明如何修改 RemoteMonitoring Visual Studio 解決方案，以納入使用模擬裝置所傳來之 **ExternalTemperature** 遙測的新規則︰

1. 如果您尚未這麼做，請使用下列 Git 命令將 **azure-iot-remote-monitoring** 儲存機制複製到本機電腦上的適當位置︰

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. 在 Visual Studio 中，從 **azure-iot-remote-monitoring** 儲存機制的本機複本開啟 RemoteMonitoring.sln 檔案。

3. 開啟檔案 Infrastructure\Models\DeviceRuleBlobEntity.cs 並新增 **ExternalTemperature** 屬性，如下所示︰

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. 在相同的檔案中新增 **ExternalTemperatureRuleOutput** 屬性，如下所示︰

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. 開啟檔案 Infrastructure\Models\DeviceRuleDataFields.cs，並在現有的 **Humidity** 屬性後新增下列 **ExternalTemperature** 屬性：

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. 在相同的檔案中更新 **_availableDataFields** 方法，以納入 **ExternalTemperature**，如下所示︰

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. 開啟檔案 Infrastructure\Repository\DeviceRulesRepository.cs 並修改 **BuildBlobEntityListFromTableRows** 方法，如下所示︰

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>重建並重新部署解決方案。

您現在可以在 Azure 訂用帳戶中部署已更新的解決方案。

1. 開啟提高權限的命令提示字元，並瀏覽至 azure-iot-remote-monitoring 儲存機制本機複本的根目錄。

2. 若要部署已更新的解決方案，請執行下列命令，並將 **{deployment name}** 換成您先前記下之預先設定解決方案部署的名稱︰

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>更新串流分析作業

部署完成時，您可以將串流分析作業更新為使用新的規則定義。

1. 在 Azure 入口網站中，瀏覽至預先設定之解決方案資源所在的資源群組。 此資源群組的名稱和您在部署期間為解決方案所指定的名稱相同。

2. 瀏覽至 {deployment name}-Rules 串流分析作業。 

3. 按一下 [停止] 讓串流分析作業停止執行  (您必須等到串流作業停止後，才能編輯查詢)。

4. 按一下 [查詢]。 編輯查詢以納入 **ExternalTemperature** 的 **SELECT** 陳述式。 下列範例示範具有全新 **SELECT** 陳述式的完整查詢︰

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. 按一下 [儲存] 來變更已更新的規則查詢。

6. 按一下 [啟動] 讓串流分析作業再次開始執行。

## <a name="add-your-new-rule-in-the-dashboard"></a>在儀表板中新增規則

您現在可以在解決方案儀表板中對裝置新增 **ExternalTemperature** 規則。

1. 瀏覽至解決方案入口網站。

2. 瀏覽至 [裝置] 面板。

3. 找出您所建立會傳送 **ExternalTemperature** 遙測的自訂裝置，然後在 [裝置詳細資料] 面板上按一下 [新增規則]。

4. 在 [資料欄位] 中選取 [ExternalTemperature]。

5. 將 [閾值] 設定為 56。 然後按一下 [儲存及檢視規則]。

6. 返回儀表板以檢視警示歷程記錄。

7. 在您保持開啟的主控台視窗中，啟動 Node.js 主控台應用程式，以開始傳送 **ExternalTemperature** 遙測資料。

8. 請注意，當有新規則觸發時，[警示歷程記錄] 資料表便會顯示新警示。
 
## <a name="additional-information"></a>其他資訊

變更運算子 **>** 的程序更為複雜，不在本教學課程所述步驟的適用範圍內。 雖然您可以變更串流分析作業，以使用您所想要的任何運算子，但在解決方案入口網站中反映該運算子是更為複雜的工作。 

## <a name="next-steps"></a>後續步驟
既然您已了解如何建立自訂規則，您可以進一步了解預先設定的解決方案︰

- [將邏輯應用程式連接至 Azure IoT 套件遠端監視預先設定解決方案][lnk-logic-app]
- [遠端監視預先設定方案中的裝置資訊中繼資料][lnk-devinfo]。

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-v1-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-v1-logic-apps-tutorial.md