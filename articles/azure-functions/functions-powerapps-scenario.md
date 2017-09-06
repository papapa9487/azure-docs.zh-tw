---
title: "從 PowerApps 呼叫函式 | Microsoft Docs"
description: "建立自訂連接器，然後使用該連接器來呼叫函式。"
services: functions
keywords: "雲端應用程式, 雲端服務, PowerApps, 商務程序, 商務應用程式"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: mblythe
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 89d9fa8f11a4c6ae3860b91e246716aad071870f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="call-a-function-from-powerapps"></a>從 PowerApps 呼叫函式
[PowerApps](https://powerapps.microsoft.com) 平台的設計是為了讓商務專家不需要傳統應用程式程式碼，就能建置應用程式。 專業開發人員可以使用 Azure Functions 來擴充 PowerApps 的功能，同時透過技術詳細資料來防護 PowerApps 應用程式建立器。

您會在本主題中根據風力渦輪機的維護案例建置一個應用程式。 本主題說明如何呼叫您在[為函式建立 OpenAPI 定義](functions-openapi-definition.md)中定義的函式。 此函式可判斷風力渦輪機的緊急修復是否符合成本效益。

![PowerApps 中已完成的應用程式](media/functions-powerapps-scenario/finished-app.png)

在本主題中，您將了解如何：

> [!div class="checklist"]
> * 在 Excel 中準備範例資料。
> * 匯出 API 定義。
> * 新增 API 連線。
> * 建立應用程式並新增資料來源。
> * 新增控制項以檢視應用程式中的資料。
> * 新增控制項以呼叫函式並顯示資料。
> * 執行應用程式以判斷修復是否符合成本效益。

## <a name="prerequisites"></a>必要條件

+ 使用中的 [PowerApps 帳戶](https://powerapps.microsoft.com/tutorials/signup-for-powerapps.md)，其登入認證與您的 Azure 帳戶相同。 
+ Excel，因為您將使用 Excel 作為應用程式的資料來源。
+ 完成[為函式建立 OpenAPI 定義](functions-openapi-definition.md)教學課程。


## <a name="prepare-sample-data-in-excel"></a>在 Excel 中準備範例資料
一開始會準備要在應用程式中使用的範例資料。 將下列表格複製到 Excel。 

| Title      | 緯度  | 經度  | 上次維修日期 | 最大輸出 | 需要維修 | 預計投入的時間 | 檢查備註                            |
|------------|-----------|-------------|-----------------|-----------|-----------------|-----------------|--------------------------------------------|
| 渦輪機 1  | 47.438401 | -121.383767 | 2017 年 2 月 23 日       | 2850      | 是             | 6               | 這是本月第二次發生此問題。       |
| 渦輪機 4  | 47.433385 | -121.383767 | 2017 年 5 月 8 日        | 5400      | 是             | 6               |                                            |
| 渦輪機 33 | 47.428229 | -121.404641 | 2017 年 6 月 20 日       | 2800      |                 |                 |                                            |
| 渦輪機 34 | 47.463637 | -121.358824 | 2017 年 2 月 19 日       | 2800      | 是             | 7               |                                            |
| 渦輪機 46 | 47.471993 | -121.298949 | 2017 年 3 月 2 日        | 1200      |                 |                 |                                            |
| 渦輪機 47 | 47.484059 | -121.311171 | 2016 年 8 月 2 日        | 3350      |                 |                 |                                            |
| 渦輪機 55 | 47.438403 | -121.383767 | 2016 年 10 月 2 日       | 2400      | 是             | 40               | 此渦輪機有一些新進零件。 |

1. 在 Excel 中選取資料，然後在 [首頁] 索引標籤上，按一下 [格式化​​為表格]。

    ![格式化為表格](media/functions-powerapps-scenario/format-table.png)

1. 選取任何樣式，然後按一下 [確定]。

1. 在選取表格時，於 [設計] 索引標籤的 [表格名稱] 中，輸入 `Turbines`。

    ![資料表名稱](media/functions-powerapps-scenario/table-name.png)

1. 儲存 Excel 活頁簿。

## <a name="export-an-api-definition"></a>匯出 API 定義
您從[為函式建立 OpenAPI 定義](functions-openapi-definition.md)取得函式的 OpenAPI 定義。 此程序的下一個步驟是匯出 API 定義，以便 PowerApps 和 Microsoft Flow 可以在自訂 API 中使用。

> [!IMPORTANT]
> 請記住，您必須使用與用於 PowerApps 和 Microsoft Flow 租用戶相同的認證，來登入 Azure。 這可讓 Azure 建立自訂 API，並將其提供給 PowerApps 和 Microsoft Flow。

1. 按一下函式應用程式名稱 (例如 **function-demo-energy**) > [平台功能] > [API 定義]。

    ![API 定義](media/functions-powerapps-scenario/api-definition.png)

1. 按一下 [Export to PowerApps + Flow] (匯出至 PowerApps + Flow)。

    ![API 定義來源](media/functions-powerapps-scenario/export-api-1.png)

1. 在右窗格中，使用表格中所指定的設定。

    |設定|說明|
    |--------|------------|
    |**匯出模式**|選取 [快速] 以自動產生自訂 API。 選取 [手動] 以匯出 API 定義，但您必須接著手動將它匯入 PowerApps 和 Microsoft Flow。|
    |**環境**|選取應該儲存自訂 API 的目標環境。 如需詳細資訊，請參閱[環境概觀](https://powerapps.microsoft.com/tutorials/environments-overview/)。|
    |**自訂 API 名稱**|輸入名稱，例如 `Turbine Repair`。|
    |**API 金鑰名稱**|輸入應該會在自訂 API UI 中看到的應用程式和流程建立器名稱。 請注意，此範例包含有用的資訊。|
 
    ![API 定義來源](media/functions-powerapps-scenario/export-api-2.png)

1. 按一下 [確定] 。 自訂 API 現已建置並新增至您指定的環境。

## <a name="add-a-connection-to-the-api"></a>新增 API 連線
自訂 API (也稱為自訂連接器) 適用於 PowerApps，但您必須建立 API 連線，才能在應用程式中使用。

1. 在 [web.powerapps.com](https://web.powerapps.com) 中，按一下 [連線]。

    ![PowerApps 連線](media/functions-powerapps-scenario/powerapps-connections.png)

1. 按一下 [新增連線]，然後向下捲動至 [渦輪機修復] 連接器並按一下。

    ![新增連線](media/functions-powerapps-scenario/new-connection.png)

1. 輸入 API 金鑰，然後按一下 [建立]。

    ![建立連線](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> 如果您與其他人共用應用程式，處理或使用應用程式的所有人也必須輸入 API 金鑰才能連線到 API。 此行為在未來可能會變更，我們將會更新本主題，以反映這一點。

## <a name="create-an-app-and-add-data-sources"></a>建立應用程式並新增資料來源
現在，您已準備好在 PowerApps 中建立應用程式，並新增 Excel 資料和自訂 API 作為應用程式的資料來源。

1. 在 [web.powerapps.com](https://web.powerapps.com) 的左窗格中，按一下 [新增應用程式]。

1. 在 [空白應用程式] 下，按一下 [手機配置]。

    ![建立平板電腦應用程式](media/functions-powerapps-scenario/create-phone-app.png)

    此應用程式會在適用於 Web 的 PowerApps Studio 中開啟。 下圖顯示 PowerApps Studio 的不同部分。 此圖適用於已完成的應用程式，您最初會在中間窗格看到一個空白畫面。

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(1) 左導覽列**，您會在其中看到每個畫面上所有控制項的階層檢視

    **(2) 中間窗格**，顯示您正在工作的畫面

    **(3) 右窗格**，您可以在其中設定版面配置和資料來源等選項

    **(4) [屬性]** 下拉式清單，您可以在其中選取要套用公式的屬性

    **(5) 公式列**，您可以在其中新增定義應用程式行為的公式 (如 Excel 所示)
    
    **(6) 功能區**，您可以在其中新增控制項並自訂設計項目

1. 新增 Excel 檔案作為資料來源。

    1. 在右窗格的 [資料] 索引標籤上，按一下 [新增資料來源]。

        ![新增資料來源](media/functions-powerapps-scenario/add-data-source.png)

    1. 按一下 [對應用程式新增靜態資料]。

        ![新增資料來源](media/functions-powerapps-scenario/add-static-data.png)

        在一般情況下，您可以從外部來源讀取和寫入資料，但由於這是範例，因此您將新增 Excel 資料作為靜態資料。

    1. 巡覽至您儲存的 Excel 檔案，選取 [渦輪機] 表格，然後按一下 [連線]。

        ![新增資料來源](media/functions-powerapps-scenario/choose-table.png)

1. 新增自訂 API 作為資料來源。

    1. 在 [資料] 索引標籤上，按一下 [新增資料來源]。

    1. 按一下 [渦輪機修復]。

        ![[渦輪機修復] 連接器](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>新增控制項以檢視應用程式中的資料
現在，資料來源可供應用程式使用，您要將一個畫面新增至應用程式，以便檢視渦輪機資料。

1. 在 [首頁] 索引標籤上，按一下 [新增畫面] > [清單畫面]。

    ![清單畫面](media/functions-powerapps-scenario/list-screen.png)

    PowerApps 會新增一個畫面，其中包含用以顯示項目的「資源庫」，以及可進行搜尋、排序和篩選的其他控制項。

1. 將標題列變更為 `Turbine Repair`，並調整資源庫大小以挪出空間給更多控制項。

    ![變更標題並調整資源庫大小](media/functions-powerapps-scenario/gallery-title.png)

1. 在選取資源庫時，於右窗格的 [資料] 索引標籤上，將資料來源從 [CustomGallerySample] 變更為 [渦輪機]。

    ![變更資料來源](media/functions-powerapps-scenario/change-data-source.png)

    資料集未包含影像，因此接下來您要變更為更適合資料的版面配置。 

1. 仍在右窗格中，將 [版面配置] 變更為 [標題、子標題與本文]。

    ![變更資源庫版面配置](media/functions-powerapps-scenario/change-layout.png)

1. 最後一個步驟是在右窗格中，變更資源庫中所顯示的欄位。

    ![變更資源庫欄位](media/functions-powerapps-scenario/change-fields.png)
    
    + **本文1** = 上次維修日期
    + **子標題2** = 需要維修
    + **標題2** = 標題 

1. 在選取資源庫時，將 **TemplateFill** 屬性設定為下列公式：`If(ThisItem.IsSelected, Orange, White)`。

    ![範本填滿公式](media/functions-powerapps-scenario/formula-fill.png)

    現在，您可以更輕鬆地看到選取了哪一個資源庫項目。

    ![選取的項目](media/functions-powerapps-scenario/selected-item.png)

1. 您不需要應用程式中的原始畫面。 在左窗格中，將滑鼠停留在 [畫面1] 上，然後依序按一下 [...] 和 [刪除]。

    ![刪除畫面](media/functions-powerapps-scenario/delete-screen.png)

您通常會在生產環境應用程式中執行許多其他格式化工作，但我們將前往此案例的重要部分，那就是呼叫函式。

## <a name="add-controls-to-call-the-function-and-display-data"></a>新增控制項以呼叫函式並顯示資料
您有一個應用程式會顯示每個渦輪機的摘要資料，現在可以新增控制項以呼叫您所建立的函式，並顯示傳回的資料。 您可以根據在 OpenAPI 定義中命名函式的方式來存取函式，在本例中為 `TurbineRepair.CalculateCosts()`。

1. 在功能區的 [插入] 索引標籤上，按一下 [按鈕]。 然後在相同的索引標籤上，按一下 [標籤]

    ![插入按鈕和標籤](media/functions-powerapps-scenario/insert-controls.png)

1. 將按鈕和標籤拖曳至資源庫下方，並調整標籤大小。 

1. 選取按鈕文字，並將它變更為 `Calculate costs`。 應用程式看起來應該如下圖所示。

    ![含有按鈕的應用程式](media/functions-powerapps-scenario/move-button-label.png)

1. 選取按鈕，然後針對按鈕的 **OnSelect** 屬性輸入下列公式。

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    此公式會在按下按鈕時執行，並在所選取資源庫項目的 [需要維修] 值為 `Yes` 時執行下列動作：

    + 清除「集合」`DetermineRepair` 以移除先前呼叫的資料。 集合是表格式變數。

    + 將呼叫函式 `TurbineRepair.CalculateCosts()` 所傳回的資料指派給集合。 
    
        傳遞至函式的值是來自資源庫中所選項目的 [預計投入的時間] 和 [最大輸出] 欄位。 這些欄位不會顯示在資源庫中，但仍可在公式中使用。

1. 選取標籤，然後輸入下列公式作為標籤的 **Text** 屬性。

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    此公式會使用 `First()` 函式只存取 `DetermineRepair` 集合的第一列。 接著會顯示函式所傳回的三個值：`message`、`costToFix` 和 `revenueOpportunity`。 第一次執行應用程式之前，這些值為空白。

    完成的應用程式看起來應該如下圖所示。

    ![執行前已完成的應用程式](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>執行應用程式
您有一個完整的應用程式！ 現在可以執行並查看函式呼叫的運作狀況。

1. 在 PowerApps Studio 的右上角，按一下 [執行] 按鈕： ![執行應用程式按鈕](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. 選取 [需要維修] 的值為 [`Yes`] 的渦輪機，然後按一下 [計算成本] 按鈕。 您應該會看到如下圖所示的結果。

    ![PowerApps 中已完成的應用程式](media/functions-powerapps-scenario/finished-app.png)

1. 試試看其他渦輪機，以查看函式每次傳回的結果。

## <a name="next-steps"></a>後續步驟
在本主題中，您已了解如何：

> [!div class="checklist"]
> * 在 Excel 中準備範例資料。
> * 匯出 API 定義。
> * 新增 API 連線。
> * 建立應用程式並新增資料來源。
> * 新增控制項以檢視應用程式中的資料。
> * 新增控制項以呼叫函式並顯示資料
> * 執行應用程式以判斷修復是否符合成本效益。

若要深入了解 PowerApps，請參閱 [PowerApps 簡介](https://powerapps.microsoft.com/tutorials/getting-started/)。

若要了解另一個使用 Azure Functions 的相關案例，請參閱[建立與 Azure Logic Apps 整合的函式](functions-twitter-email.md)。
