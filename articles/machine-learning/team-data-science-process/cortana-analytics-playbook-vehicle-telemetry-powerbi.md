---
title: "適用於車輛健全狀態與駕駛習慣的 Power BI 儀表板 - Azure | Microsoft Docs"
description: "使用 Cortana Intelligence 具備的強大功能，取得關於車輛健全狀態與駕駛習慣的即時預測情資。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 39be936520d62cb1c1c28de9bd72f8f489166082
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>車輛遙測分析方案範本 Power BI 儀表板安裝指示
此 **功能表** 連結至此腳本的章節。 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

「車輛遙測分析」方案示範汽車經銷商、汽車製造商和保險公司如何運用 Cortana Intelligence 功能，取得車輛健全狀態與駕駛習慣的即時預測情資，以改善客戶體驗、R&D 和行銷活動等方面。 本文件包含在您的訂用帳戶中部署方案之後，如何設定 Power BI 報告和儀表板的逐步指示。 

## <a name="prerequisites"></a>必要條件
1. 部署[遙測分析](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90)解決方案  
2. [安裝 Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3. [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)。 如果您沒有 Azure 訂用帳戶，請立即取得免費的 Azure 訂用帳戶
4. Microsoft Power BI 帳戶

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence Suite 元件
「車輛遙測分析」方案範本會在您的訂用帳戶中部署下列 Cortana Intelligence 服務。

* 事件中樞可將數百萬計的車輛遙測事件擷取至 Azure。
* **串流分析** 」可取得關於車輛健全狀態的即時情資，同時以長期儲存的方式保存這些資料，供日後進行更豐富廣泛的批次分析之用。
* **機器學習服務** 」可即時進行異常偵測與批次處理以取得預測情資。
* **HDInsight** 用於大規模的資料轉換
* **Data Factory** 可執行批次處理管線的協調、排程、資源管理和監控工作。

**Power BI** 為此解決方案提供具備即時資料與預測性分析視覺效果等豐富功能的儀表板。 

此解決方案使用兩種不同的資料來源：**模擬車輛訊號和診斷資料集**與**車輛類別目錄**。

此方案包含車輛遠程資訊服務模擬器。 它會在指定時間點發出對應於車輛狀態與駕駛模式的診斷資訊和訊號。 

「車輛類別目錄」是包含 VIN 至車型對應的參考資料集

## <a name="power-bi-dashboard-preparation"></a>Power BI 儀表板準備
### <a name="setup-power-bi-real-time-dashboard"></a>設定 Power BI 即時儀表板

啟動即時儀表板應用程式部署完成後，應遵循手動操作說明

* 下載即時儀表板應用程式 RealtimeDashboardApp.zip，並將它解壓縮。
*  在解壓縮的資料夾中，開啟應用程式組態檔「RealtimeDashboardApp.exe.config」，並將事件中樞、Blob 儲存體及 ML 服務連線的 appsettings，更換為手動操作說明中的值，並儲存您的變更。
* 執行應用程式 RealtimeDashboardApp.exe。 將顯示登入視窗，請提供您有效的 PowerBI 認證，然後按一下 [接受] 按鈕。 應用程式將開始執行。

   ![登入 Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
   ![Power BI 儀表板權限](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

* 登入 PowerBI 網站，並建立即時儀表板。

現在，您可以使用豐富的視覺效果來設定 Power BI 儀表板，以取得車輛健全狀況和駕駛習慣的即時預測性情資。 建立所有報告和設定儀表板大約需要 45 分鐘至一小時。 

### <a name="configure-power-bi-reports"></a>設定 Power BI 報告
即時報告和儀表板需要大約 30-45 分鐘的時間才能完成。 瀏覽至 [http://powerbi.com](http://powerbi.com) 並登入。

![登入 Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Power BI 中會產生新的資料集。 按一下 **ConnectedCarsRealtime** 資料集。

![選取連接的汽車即時資料集](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

使用 **Ctrl + s**儲存空白報告。

![儲存空白報告](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

提供報告名稱「車輛遙測分析即時 - 報告」 。

![提供報告名稱](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>即時報告
此解決方案有三份即時報告：

1. 行駛中的車輛
2. 需要維修的車輛
3. 車輛健全狀況統計資料

您可以選擇設定上述三份即時報告，或在任何階段後停止並繼續進行設定批次報告的下一節。 我們建議您建立上述三份報表，以將解決方案即時路徑的完整深入分析視覺化。  

### <a name="1-vehicles-in-operation"></a>1.行駛中的車輛
按兩下 [第 1 頁]，重新命名為「行駛中的車輛」  
    ![Connected Cars - 行駛中的車輛](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

從 [欄位] 中選取 **vin** 欄位，然後選擇「卡片」做為視覺效果類型。  

建立的卡片視覺效果如下圖所示。  
    ![Connected Cars - 選取 vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

按一下空白區域以加入新的視覺效果。  

從欄位中選取 **City** 和 **vin**。 將視覺效果變更為「地圖」。 將 **vin** 拖曳到值區域。 從欄位中將 **city** 拖曳到 [圖例] 區域。   
    ![Connected Cars - 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

從 [視覺效果] 中選取 [格式] 區段，按一下 [標題]，將 [文字] 變更為**行駛中的車輛 (依城市)**。  
    ![Connected Cars - 行駛中的車輛 (依城市)](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

最終的視覺效果如下圖所示。    
    ![Connected Cars - 最終視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

按一下空白區域以加入新的視覺效果。  

選取 **City** 和 **vin**，將視覺效果類型變更為「群組直條圖」。 確定 **City** 欄位在 [軸] 區域，而 **vin** 在 [值] 區域  

依 [vin 的計數]   
    ![Connected Cars - vin 的計數](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

將圖表**標題**變更為「行駛中的車輛 (依城市)」  

按一下 [格式] 區段，然後選取 [資料色彩]，按一下 [全部顯示] 的 [開啟]  
    ![Connected Cars - 顯示所有資料色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

按一下色彩圖示變更個別城市的色彩。  
    ![Connected Cars - 變更色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

按一下空白區域以加入新的視覺效果。  

從 [視覺效果] 中選取「群組直條圖」視覺效果，將 **city** 欄位拖曳到 [軸] 區域、將 **Model** 拖曳到 [圖例] 區域、將 **vin** 拖曳到 [值] 區域。  
    ![Connected Cars - 群組直條圖](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Connected Cars - 轉譯](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

重新排列此頁面上的所有視覺效果，如下圖所示。  
    ![Connected Cars -視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

您已經成功設定「行駛中的車輛」即時報告。 您可以繼續建立下一份即時報告，或在此停止並設定儀表板。 

### <a name="2-vehicles-requiring-maintenance"></a>2.需要維修的車輛
按一下 ![新增](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) 以加入新的報告，並重新命名為「需要維修的車輛」

![Connected Cars - 需要維修的車輛](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

選取 **vin** 欄位，將視覺效果類型變更為「卡片」。  
    ![Connected Cars - Vin 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

我們在資料集中有一個名為 "MaintenanceLabel" 的欄位。 此欄位的值可以是 “0” 或 “1”。 它是由佈建為解決方案一部分並與即時路徑整合的 Azure Machine Learning 模型設定。 值為 "1" 表示車輛需要維修。 

若要加入 [頁面層級]  篩選以顯示需要維修的車輛資料： 

1. 將 **"MaintenanceLabel"** 欄位拖曳到 [頁面層級篩選]。  
   ![Connected Cars - 頁面層級篩選](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  
2. 按一下 MaintenanceLabel 頁面層級篩選底部出現的 [基本篩選]  功能表。  
   ![Connected Cars - 基本篩選](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  
3. 將篩選值設為 **“1”**    
   ![Connected Cars - 篩選值](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

按一下空白區域以加入新的視覺效果。  

從 [視覺效果] 中選取「群組直條圖」  
![Connected Cars - Vind 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Connected Cars - 群組直條圖](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

將 **Model** 欄位拖曳到 [軸] 區域，將 **Vin** 拖曳到 [值] 區域。 然後，依 [vin 的計數] 排序視覺效果。  將圖表**標題**變更為「需要維修的車輛 (依車型)」  

將 **vin** 欄位拖曳到 [視覺效果] 索引標籤的 [欄位] ![欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) 區段上出現的 [色彩飽和度]  
![Connected Cars - 色彩飽和度](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

從 [格式] 區段變更視覺效果中的 [資料色彩]  
將最小值色彩變更為：**F2C812**  
將最大值色彩變更為：**FF6300**  
![Connected Cars - 色彩變更](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Connected Cars - 新的視覺效果色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

按一下空白區域以加入新的視覺效果。  

從 [視覺效果] 中選取「群組直條圖」，將 **vin** 欄位拖曳到 [值] 區域，將 **City** 欄位拖曳到 [軸] 區域。 依 [vin 的計數] 排序圖表。 將圖表**標題**變更為「需要維修的車輛 (依城市)」   
![Connected Cars - 需要維修的車輛 (依城市)](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

按一下空白區域以加入新的視覺效果。  

從 [視覺效果] 中選取「多列卡片」視覺效果，將 **Model** 和 **vin** 拖曳到 [欄位] 區域。  
![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

重新排列所有視覺效果，最終報告看起來如下︰  
![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

您已經成功設定「需要維修的車輛」即時報告。 您可以繼續建立下一份即時報告，或在此停止並設定儀表板。 

### <a name="3-vehicles-health-statistics"></a>3.車輛健全狀況統計資料
按一下 ![新增](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) 以加入新的報告，並重新命名為「車輛健全狀況統計資料」  

從 [視覺效果] 中選取「量測計」視覺效果，然後將 **Speed** 欄位拖曳到 [值]、[最小值]。[最大值] 區域。  
![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

將 [值] 區域中的 **speed** 預設彙總變更為 [平均值] 

將 [最小值] 區域中的 **speed** 預設彙總變更為 [最小值]

將 [最大值] 區域中的 **speed** 預設彙總變更為 [最大值]

![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

將 [量測計標題] 重新命名為「平均速度」 

![Connected Cars - 量測計](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

按一下空白區域以加入新的視覺效果。  

同樣地加入「平均機油」、「平均油量」和「平均引擎溫度」的**量測計**。  

依照上述「平均速度」  量測計中的步驟，變更每個量測計中各欄位的預設彙總。

![Connected Cars - 量測計](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「折線與群組直條圖」，然後將 **City** 欄位拖曳到 [共用軸]，將 **speed**、將 **tirepressure 和 engineoil 欄位**拖曳到 [資料行值] 區域，將其彙總類型變更為 [平均值]。 

將 **engineTemperature** 欄位拖曳到 [折線圖值] 區域，將彙總類型變更為 [平均值]。 

![Connected Cars -視覺效果欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

將圖表**標題**變更為「平均速度、胎壓、機油和引擎溫度」。  

![Connected Cars -視覺效果欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「樹狀圖」視覺效果，將 **Model** 欄位拖曳到 [群組] 區域，將 **MaintenanceProbability** 欄位拖曳到 [值] 區域。

將圖表**標題**變更為「需要維修的車型」。

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「100% 堆疊橫條圖」視覺效果，將 **city** 欄位拖曳到 [軸] 區域，將 **MaintenanceProbability**、**RecallProbability** 欄位拖曳到 [值] 區域。

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

按一下 [格式]，選取 [資料色彩]，並將 **MaintenanceProbability** 色彩設定為值 **"F2C80F"**。

將圖表**標題**變更為「車輛維修和召回的機率 (依城市)」。

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「區域圖」視覺效果，將 **Model** 欄位拖曳到 [軸] 區域，將 **engineOil、tirepressure、speed 和 MaintenanceProbability** 欄位拖曳到 [值] 區域。 將其彙總類型變更為 [平均值] 。 

![Connected Cars - 變更彙總類型](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

將圖表的標題變更為「平均機油、胎壓、速度和維修機率 (依車型)」 。

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

按一下空白區域以加入新的視覺效果：

1. 從 [視覺效果] 中選取「散佈圖」  視覺效果。
2. 將 **Model** 欄位拖曳到 [詳細資料] 和 [圖例] 區域。
3. 將 **fuel** 欄位拖曳到 [X 軸] 區域，將彙總變更為 [平均值]。
4. 將 **engineTemparature** 欄位拖曳到 [Y 軸] 區域，將彙總變更為 [平均值]
5. 將 **vin** 欄位拖曳到 [大小] 區域。

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

將圖表**標題**變更為「燃料、引擎溫度的平均值 (依車型)」。

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

最終的報告顯示如下。

![Connected Cars - 最終報告](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>將報告中的視覺效果釘選到即時儀表板
按一下 [儀表板] 旁邊的加號圖示，以建立空白儀表板。 您可以將它命名為「車輛遙測分析儀表板」

![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

將上述報告中的視覺效果釘選到儀表板。 

![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

已建立上述三份報告並將對應的視覺效果釘選到儀表板時，儀表板應如下所示。 如果您尚未建立所有報告，儀表板看起來可能有所不同。 

![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

恭喜！ 您已成功建立即時儀表板。 隨著您繼續執行 CarEventGenerator.exe 和 RealtimeDashboardApp.exe，您應該會在儀表板上看見即時更新。 應該需要約 10 至 15 分鐘的時間才能完成下列步驟。

## <a name="setup-power-bi-batch-processing-dashboard"></a>設定 Power BI 批次處理儀表板
> [!NOTE]
> 端對端批次處理管線大約需要兩小時 (從成功完成部署開始算起) 的時間才能完成執行並處理一年份的已產生資料。 因此請等處理完成後再繼續進行後續步驟。 
> 
> 

**下載 Power BI 設計工具檔案**

* 預先設定的 Power BI 設計工具檔案會包含於部署中 手動操作說明
* 尋找 2. 安裝 PowerBI 批次處理儀表板，您可以下載批次處理儀表板的 PowerBI 範本，在此稱為 ConnectedCarsPbiReport.pbix。
* 儲存在本機

**設定 Power BI 報告**

* 使用 Power BI Desktop，開啟設計工具檔案「ConnectedCarsPbiReport.pbix」。 如果您還沒有安裝 Power BI Desktop，請從 [Power BI Desktop 安裝](http://www.microsoft.com/download/details.aspx?id=45331)進行安裝。 
* 按一下 [編輯查詢] 。

![編輯 Power BI 查詢](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

* 按兩下 [來源] 。

![設定 Power BI 來源](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

* 以佈建為部署一部分的 Azure SQL Server 更新伺服器連接字串。  在下列位置查看手動操作說明 

    4. Azure SQL Database
    
    * 伺服器︰somethingsrv.database.windows.net
    * 資料庫︰connectedcar
    * 使用者名稱︰使用者名稱
    * 密碼︰ 您可以從 Azure 入口網站管理您的 SQL 伺服器密碼

* 將 [資料庫]  保留為 *connectedcar*。

![設定 Power BI 資料庫](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

* 按一下 [確定] 。
* 依預設會選取 [Windows 認證] 索引標籤，請按一下右邊的 [資料庫] 索引標籤，將它變更為 [資料庫認證]。
* 提供在 Azure SQL Database 部署安裝過程中提供的 [使用者名稱] 和 [密碼]。

![提供資料庫認證](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

* 按一下 [連接] 
* 針對右窗格中出現的其餘三個查詢各重複上述步驟，然後更新資料來源連接詳細資料。
* 按一下 [關閉並載入] 。 Power BI Desktop 檔案資料集會連接到 SQL Azure 資料庫資料表。
* **關閉** Power BI Desktop 檔案。

![關閉 Power BI Desktop](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

* 按一下 [儲存]  按鈕以儲存變更。 

您現在已設定對應至解決方案中的批次處理路徑的所有報告。 

## <a name="upload-to-powerbicom"></a>上傳至 *powerbi.com*
1. 瀏覽至 Power BI Web 入口網站 (http://powerbi.com) 並且登入。
2. 按一下 [取得資料]   
3. 上傳 Power BI Desktop 檔案。  
4. 若要上傳，請按一下 [取得資料] -> [檔案取得]-> [本機檔案]  
5. 瀏覽至**「**ConnectedCarsPbiReport.pbix**」**  
6. 一旦上傳檔案，將會瀏覽回到您的 Power BI 工作區。  

將會為您建立資料集、報告和空白儀表板。  

將圖表釘選至 Power BI 中的新儀表板，名稱為車輛遙測分析儀表板。 按一下上面建立的空白儀表板，然後瀏覽至剛才上傳的報告上的 [報告]  區段。  

![車輛遙測 Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

**注意報告有六頁：**  
第 1 頁︰車輛密度  
第 2 頁︰即時車輛健全狀況  
第 3 頁︰危險駕駛的車輛   
第 4 頁︰召回的車輛  
第 5 頁︰省油的車輛  
第 6 頁︰Contoso 標誌  

![連接的車輛 Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

**從第 3 頁**，釘選下列項目：  

1. VIN 的計數  
   ![連接的車輛 Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 
2. 危險駕駛的車輛 (依車型) – 瀑布圖   
   ![車輛遙測 - 釘選圖表 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**從第 5 頁**，釘選下列項目： 

1. vin 的計數    
   ![車輛遙測 - 釘選圖表 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2. 省油的車輛 (依車型)：群組直條圖   
   ![車輛遙測 - 釘選圖表 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**從第 4 頁**，釘選下列項目：  

1. vin 的計數  
   ![車輛遙測 - 釘選圖表 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 
2. 召回的車輛 (依城市、車型)：樹狀圖   
   ![車輛遙測 - 釘選圖表 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**從第 6 頁**，釘選下列項目：  

1. Contoso Motors 標誌   
   ![車輛遙測 - 釘選圖表 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**組織儀表板**  

1. 瀏覽至儀表板
2. 將滑鼠暫留在每個圖表，根據下列完整儀表板影像中提供的名稱來重新命名。 另外也將圖表排列成如下列儀表板所示。  
   ![車輛遙測 - 組織儀表板 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
   ![車輛遙測 Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. 如果您已建立本文件中提到的所有報報，則最終完成的儀表板應該看起來如下圖。 

![車輛遙測 - 組織儀表板 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

恭喜！ 您已成功建立報報和儀表板，可取得車輛健全狀況和駕駛習慣的即時、預測和批次深入分析。  

