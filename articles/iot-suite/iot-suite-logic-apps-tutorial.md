---
title: "Azure IoT 套件和 Logic Apps | Microsoft Docs"
description: "如何將 Logic Apps 連結至 Azure IoT 套件以執行商務程序的教學課程。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: corywink
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 5e3221395082513f842863615d40f7d3ebf2562e
ms.contentlocale: zh-tw
ms.lasthandoff: 03/10/2017

---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>教學課程：將邏輯應用程式連接至 Azure IoT 套件遠端監視預先設定解決方案
[Microsoft Azure IoT 套件][lnk-internetofthings]遠端監視預先設定解決方案以一套端對端功能集來示範 IoT 解決方案，是快速入門的好工具。 本教學課程逐步引導您將邏輯應用程式連接至 Microsoft Azure IoT 套件遠端監視預先設定解決方案。 這些步驟示範如何將 IoT 解決方案連接至商務程序，以進一步發展此 IoT 解決方案。

如果您要尋找有關如何佈建遠端監視預先設定解決方案的逐步解說，請參閱[教學課程：開始使用 IoT 預先設定的解決方案][lnk-getstarted]。

在開始本教學課程之前，您應該：

* 在您的 Azure 訂用帳戶中佈建遠端監視預先設定的解決方案
* 建立 SendGrid 帳戶，用來讓您傳送可觸發商務程序的電子郵件。 您可以在 [SendGrid](https://sendgrid.com/) 按一下 [免費試用]，註冊免費試用帳戶。 註冊免費試用帳戶後，您必須在 SendGrid 中建立 [API 金鑰](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) 來授權傳送郵件。 稍後在教學課程中需要此 API 金鑰。

若要完成本教學課程，您需要 Visual Studio 2015 或 Visual Studio 2017，才能在預先設定的解決方案後端中修改動作。

假設您已佈建遠端監視預先設定解決方案，請在 [Azure 入口網站][lnk-azureportal]中瀏覽至該解決方案的資源群組。 資源群組的名稱與您在佈建遠端監視解決方案時所選擇的解決方案名稱相同。 在資源群組中，您可以看到解決方案已佈建的所有 Azure 資源，但在 Azure 傳統入口網站中可找到的 Azure Active Directory 應用程式除外。 下列螢幕擷取畫面顯示遠端監視預先設定解決方案的 [資源群組]  刀鋒視窗範例︰

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

若要開始，請設定邏輯應用程式來使用預先設定的解決方案。

## <a name="set-up-the-logic-app"></a>設定邏輯應用程式
1. 在 Azure 入口網站中，按一下資源群組刀鋒視窗頂端的 [新增]。
2. 搜尋 [邏輯應用程式]，選取它，然後按一下 [建立]。
3. 填寫 [名稱]，並使用您佈建遠端監視解決方案時使用的相同 [訂用帳戶] 和 [資源群組]。 按一下 [建立] 。
   
    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)
4. 部署完成時，您會看到邏輯應用程式列為資源群組中的資源。
5. 按一下邏輯應用程式來瀏覽至 [邏輯應用程式] 刀鋒視窗，選取 [空白邏輯應用程式] 範本以開啟 [Logic Apps 設計工具]。
   
    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)
6. 選取 [要求]。 這個動作會指定以內送 HTTP 要求加上特定 JSON 格式化承載做為觸發程序。
7. 將下列程式碼貼到 [要求本文 JSON 結構描述] 中：
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > 您可以在儲存邏輯應用程式之後複製 HTTP post 要求的 URL，但必須先新增動作。
   > 
   > 
8. 按一下手動觸發程序下的 [+ 新增步驟]。 然後按一下 [加入動作] 。
   
    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)
9. 搜尋 [SendGrid - 傳送電子郵件]  並按一下它。
   
    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)
10. 輸入連接的名稱 (如 **SendGridConnection**)，並輸入您設定 SendGrid 帳戶時設定的 **SendGrid API 金鑰**，然後按一下 [建立]。
    
    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)
11. 將您擁有的電子郵件地址新增至 [寄件者] 和 [收件者] 欄位。 將**遠端監視警示 [DeviceId]** 新增至 [主旨] 欄位。 在 [電子郵件內文] 欄位中，新增**裝置 [DeviceId] 已報告 [measurementName] 與 [measuredValue] 值**。 您可以按一下 [您可以從先前步驟中插入資料] 區段，以新增 **[DeviceId]**、**[measurementName]** 和 **[measuredValue]**。
    
    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)
12. 按一下頂端功能表中的 [儲存]。
13. 按一下 [要求] 觸發程序，複製**此 URL 的 Http Post** 值。 稍後在本教學課程中需要此 URL。

> [!NOTE]
> Logic Apps 可讓您執行[許多不同類型的動作][lnk-logic-apps-actions]，包括 Office 365 中的動作。 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>設定 EventProcessor Web 作業
在本節中，您會將您預先設定的解決方案連接到您建立的邏輯應用程式。 為了完成此工作，您要將用於觸發邏輯應用程式的 URL，加入當裝置感應器值超過臨界值時引發的動作。

1. 使用您的 git 用戶端複製最新版的 [azure-iot-remote-monitoring github 儲存機制][lnk-rmgithub]。 例如：
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. 在 Visual Studio 中，從儲存機制的本機複本開啟 **RemoteMonitoring.sln**。
3. 開啟 **Infrastructure\\Repository** 資料夾中的 **ActionRepository.cs** 檔案。
4. 使用您從邏輯應用程式記下的**此 URL 的 Http Post** 更新 **actionIds** 字典，如下所示︰
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. 在方案中儲存所做的變更並結束 Visual Studio。

## <a name="deploy-from-the-command-line"></a>從命令列部署
本節中，您要部署已更新的遠端監視解決方案，取代目前在 Azure 中執行的版本。

1. 遵循[開發設定][lnk-devsetup]的指示，設定您的環境準備部署。
2. 若要在本機部署，請遵循[本機部署][lnk-localdeploy]指示。
3. 若要部署至雲端並更新現有的雲端部署，請遵循[雲端部署][lnk-clouddeploy]指示。 使用原始部署的名稱做為部署名稱。 例如，如果原始部署為 **demologicapp**，請使用下列命令︰
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   當建置指令碼執行時，請務必使用您佈建解決方案時所使用的相同 Azure 帳戶、訂用帳戶、區域和 Active Directory 執行個體。

## <a name="see-your-logic-app-in-action"></a>了解邏輯應用程式的實際運作
當您佈建解決方案時，根據預設，遠端監視預先設定的解決方案會設定兩個規則。 這兩個規則都在 **SampleDevice001** 裝置上︰

* 溫度 > 38.00
* 溼度 > 48.00

溫度規則會觸發 [引發警示] 動作，溼度規則會觸發 **SendMessage** 動作。 假設您在 **ActionRepository** 類別中對這兩個動作使用相同的 URL，邏輯應用程式將會觸發其中一條規則。 兩條規則皆使用 SendGrid 將警示的詳細資料透過電子郵件傳送至 [收件者]  地址。

> [!NOTE]
> 邏輯應用程式會在每次達到臨界值時繼續觸發。 若要避免不必要的電子郵件，您可以在解決方案入口網站中停用規則，或在 [Azure 入口網站][lnk-azureportal]中停用邏輯應用程式。
> 
> 

除了接收電子郵件，您也可以查看邏輯應用程式在入口網站中運作情形︰

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>後續步驟
既然您已使用邏輯應用程式將預先設定的解決方案連接到商務程序，您可以深入了解自訂預先設定的解決方案的選項。

* [搭配使用動態遙測與遠端監視預先設定解決方案][lnk-dynamic]
* [遠端監視預先設定方案中的裝置資訊中繼資料][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md

