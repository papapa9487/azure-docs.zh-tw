---
title: "自訂連線處理站解決方案 - Azure | Microsoft Docs"
description: "說明如何自訂連線工廠預先設定之解決方案的行為。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 4505023c1869c0bb23e99dc7db1ee6e65466c2cc
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>自訂連線工廠解決方案顯示 OPC UA 伺服器資料的方式

## <a name="introduction"></a>簡介

連線工廠解決方案會彙總並顯示連接到解決方案之 OPC UA 伺服器的資料。 您可以瀏覽並傳送命令至解決方案的 OPC UA 伺服器。 如需 OPC UA 的詳細資訊，請參閱[連線的處理站常見問題集](iot-suite-faq-cf.md)。

解決方案中的彙總資料範例包括「整體設備效率 (OEE)」和「關鍵效能指標 (KPI)」，您可以在儀表板的工廠、生產線與工作站層級分別檢視這些資料。 下列螢幕擷取畫面顯示 [Munich (慕尼黑)] 工廠的 [Production line 1 (生產線 1)] 的 [Assembly (組件)] 工作站的 OEE 和 KPI 值：

![解決方案中的 OEE 和 KPI 值範例][img-oee-kpi]

解決方案可讓您檢視 OPC UA 伺服器 (稱為「工作站」) 中特定資料項目的詳細資訊。 下列螢幕擷取畫面顯示特定工作站製造的項目數的繪圖︰

![製造項目數的繪圖][img-manufactured-items]

如果您按一下其中一個圖形，可使用 Time Series Insights (TSI) 進一步探索資料︰

![使用 Time Series Insights 探索資料][img-tsi]

本文章說明：

- 解決方案中各種檢視的資料是如何取得的。
- 如何自訂解決方案顯示資料的方式。

## <a name="data-sources"></a>資料來源

連線工廠解決方案會顯示連接到解決方案之 OPC UA 伺服器的資料。 預設安裝包含數台執行工廠模擬的 OPC UA 伺服器。 您可以將[透過閘道連線][lnk-connect-cf]的自有 OPC UA 伺服器新增到解決方案。

您可以在儀表板中瀏覽連線的 OPC UA 伺服器可傳送至您的解決方案的資料項目︰

1. 瀏覽至 [Select an OPC UA server (選取 OPC UA 伺服器)] 檢視︰

    ![瀏覽至 [Select an OPC UA server (選取 OPC UA 伺服器)] 檢視][img-select-server]

1. 選取伺服器，然後按一下 [Connect (連線)]。 出現安全性警告時，按一下 [Proceed (繼續)]。

    > [!NOTE]
    > 這個警告每台伺服器只會出現一次，是建立解決方案儀表板和伺服器之間的信任關係。

1. 您現在可以瀏覽伺服器可傳送至解決方案的資料項目。 正在傳送至解決方案的項目會有綠色的核取記號︰

    ![發行的項目][img-published]

1. 如果您是解決方案的*管理員*，您可以選擇發行資料項目，以在連線工廠解決方案中提供。 身為管理員，您也可以變更 OPC UA 伺服器中的資料項目值及呼叫方法。

## <a name="map-the-data"></a>對應資料

連線工廠解決方案會將 OPC UA 伺服器中的已發行資料項目對應並彙總至解決方案中的各種檢視。 當您佈建連線工廠解決方案時，解決方案會部署到您的 Azure 帳戶。 Visual Studio 連線工廠方案中的 JSON 檔案會儲存此對應資訊。 您可以檢視及修改連線工廠 Visual Studio 解決方案中的這個 JSON 設定檔。 您進行變更之後，可以重新部署解決方案。

您可以使用此組態檔來︰

- 編輯現有的模擬工廠、生產線和工作站。
- 從連接到解決方案的實際 OPC UA 伺服器對應資料。

若要複製一份連線工廠 Visual Studio 方案，請使用下列 git 命令︰

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

**ContosoTopologyDescription.json** 檔案定義 OPC UA 伺服器資料項目與連線工廠解決方案儀表板檢視之間的對應。 您可以在 Visual Studio 方案的 **WebApp** 專案的 **Contoso\Topology** 資料夾中找到此組態檔。

JSON 檔案的內容是以工廠、生產線和工作站節點的階層來組織。 此階層定義了連線工廠儀表板中的瀏覽階層。 階層中每個節點的值決定儀表板中顯示的資訊。 例如，慕尼黑工廠的 JSON 檔案包含下列值︰

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

名稱、描述和位置會出現在儀表板的這個檢視中︰

![儀表板中的幕尼黑資料][img-munich]

每個工廠、生產線和工作站都有一個映像屬性。 您可以在 **WebApp** 專案的 **Content\img** 資料夾中找到這些 JPEG 檔案。 這些影像檔會顯示在連線工廠儀表板中。

每個工作站均包含數個詳細屬性，定義與 OPC UA 資料項目的對應。 這些屬性將於下列各節中說明：

### <a name="opcuri"></a>OpcUri

**OpcUri** 值是可唯一識別 OPC UA 伺服器的 OPC UA 應用程式 URI。 例如，慕尼黑生產線 1 的組件工作站的 **OpcUri** 值看起來就像這樣︰**urn:scada2194:ua:munich:productionline0:assemblystation**。

您可以在解決方案儀表板中檢視連接的 OPC UA 伺服器的 URI：

![檢視 OPC UA 伺服器 URI][img-server-uris]

### <a name="simulation"></a>模擬

**Simulation** 節點是 OPC UA 模擬特有的，會在預設佈建的 OPC UA 伺服器中執行。 它不會使用在實際的 OPC UA 伺服器上。

### <a name="kpi1-and-kpi2"></a>Kpi1 和 Kpi2

這些節點描述工作站中的資料如何影響儀表板中的這兩個 KPI 值。 在預設部署中，這些 KPI 值是每小時的單位及每小時的 kWh。 解決方案會在工作站層級計算 KPI 值，並於生產線和工廠層級彙總這些 KPI 值。

每個 KPI 都有最小值、最大值和目標值。 每個 KPI 值也可定義連線工廠解決方案要執行的警示動作。 下列程式碼片段顯示慕尼黑生產線 1 的組件工作站的 KPI 定義︰

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

下列螢幕擷取畫面顯示儀表板中的 KPI 資料。

![儀表板中的 KPI 資訊][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

**OpcNodes** 節點識別 OPC UA 伺服器中發行的資料項目，並指定如何處理該資料。

**NodeId** 值識別 OPC UA 伺服器中特定的 OPC UA NodeID。 慕尼黑生產線 1 的組件工作站中的第一個節點的值為 **ns=2;i=385**。 **NodeId** 值指定要從 OPC UA 伺服器讀取的資料項目，**SymbolicName** 則為該資料提供使用於儀表板中的易記名稱。

下表摘要說明每個節點相關聯的其他值︰

| 值 | 說明 |
| ----- | ----------- |
| 相關性  | 此資料影響的 KPI 和 OEE 值。 |
| OpCode     | 資料彙總的方式。 |
| Units      | 儀表板中使用的單位。  |
| 可見    | 是否在儀表板中顯示此值。 部分值使用於計算中而不會顯示。  |
| 最大值    | 觸發儀表板警示的最大值。 |
| MaximumAlertActions | 回應警示所採取的動作。 例如，傳送命令到工作站。 |
| ConstValue | 使用於計算的常數值。 |

## <a name="deploy-the-changes"></a>部署變更

完成 **ContosoTopologyDescription.json** 檔案的變更後，您必須將連線工廠解決方案重新部署到您的 Azure 帳戶。

**azure-iot-connected-factory** 存放庫包含一個 **build.ps1** PowerShell 指令碼，可用於重新建置並部署解決方案。

## <a name="next-steps"></a>後續步驟

閱讀下列文章，深入了解連線工廠預先設定的解決方案︰

* [連線處理站預先設定的解決方案逐步解說][lnk-rm-walkthrough]
* [為連線處理站部署閘道][lnk-connect-cf]
* [azureiotsuite.com 網站的權限][lnk-permissions]
* [連線的處理站常見問題](iot-suite-faq-cf.md)
* [常見問題集][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md