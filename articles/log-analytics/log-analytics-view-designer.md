---
title: "在 OMS Log Analytics 中建立檢視以分析資料 | Microsoft Docs"
description: "Log Analytics 中的檢視設計工具可讓您建立要在 OMS 和 Azure 入口網站中顯示的自訂檢視，以及包含 OMS 存放庫中不同資料的視覺效果。 本文包含檢視設計工具的概觀以及建立和編輯自訂檢視的程序。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: e3c463d749dc4179df58286b9bb75584880a6bc6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>在 Log Analytics 中使用檢視表設計工具來建立自訂檢視
[Log Analytics](log-analytics-overview.md) 中的檢視設計工具可讓您在 OMS 主控台中建立自訂檢視，其中包含 OMS 存放庫中不同資料的視覺效果。 本文包含檢視設計工具的概觀以及建立和編輯自訂檢視的程序。

其他與檢視設計工具相關的文章︰

* [圖格參考](log-analytics-view-designer-tiles.md) - 可用於自訂檢視之每個圖格的設定參考。
* [視覺效果組件參考](log-analytics-view-designer-parts.md) - 可用於自訂檢視之每個圖格的設定參考。

>[!NOTE]
> 如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則所有檢視中的查詢必須以[新的查詢語言](https://go.microsoft.com/fwlink/?linkid=856078)撰寫。  在工作區升級之前建立的任何檢視都會自動轉換。

## <a name="concepts"></a>概念
以檢視設計工具建立的檢視包含下表中的各部分。

| 部分 | 說明 |
|:--- |:--- |
| 圖格 |顯示在主要 Log Analytics 概觀儀表板上。  是自訂檢視包含之資訊的視覺化摘要。  不同的圖格類型提供 OMS 存放庫中不同記錄的視覺效果。  按一下圖格即可開啟其自訂檢視。 |
| 自訂檢視 |會在使用者按一下圖格時顯示。  包含一或多個視覺效果組件。 |
| 視覺效果組件 |根據一或多個針對 OMS 存放庫中資料進行[記錄搜尋](log-analytics-log-searches.md)，所得結果的視覺效果。  大部分組件包含標頭 (提供高階的視覺效果) 和前幾名搜尋結果清單。  不同的組件類型提供 OMS 存放庫中不同記錄的視覺效果。  按一下組件中的項目可執行記錄檔搜尋以提供詳細的記錄。 |

![檢視設計工具概觀](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>將檢視設計工具新增至工作區
檢視設計工具為預覽版，您必須選取 OMS 入口網站的 [設定] 區段中的 [預覽功能]，將它新增至您的工作區。

![啟用預覽](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>建立和編輯檢視
### <a name="create-a-new-view"></a>建立新的檢視
按一下主要 OMS 儀表板中的 [檢視表設計工具] 圖格，即可在**檢視表設計工具**中開啟新的檢視。

![檢視設計工具圖格](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>編輯現有檢視
若要在檢視表設計工具中編輯現有檢視，按一下主要 OMS 儀表板中該檢視的圖格即可開啟它。  然後按一下 [編輯] 按鈕在檢視設計工具中開啟該檢視。

![編輯檢視](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>複製現有檢視
複製檢視時，它會建立新的檢視，並且在檢視設計工具中開啟。  新檢視會有與原始檢視相同的名稱再加上「複製」二字。  若要複製檢視，按一下主要 OMS 儀表板中該現有檢視的圖格即可開啟它。  然後按一下 [複製] 按鈕在檢視設計工具中開啟該檢視。

![複製檢視](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>刪除現有檢視
若要刪除現有檢視，按一下主要 OMS 儀表板中該檢視的圖格即可開啟它。  然後按一下 [編輯] 按鈕在檢視設計工具中開啟該檢視，接著按一下 [刪除檢視]。

![刪除檢視](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>匯出現有檢視
您可以將檢視匯出至 JSON 檔案，用於匯入另一個工作區，或在 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)中使用。  若要匯出現有檢視，按一下主要 OMS 儀表板中該檢視的圖格即可開啟它。  然後按一下 [匯出] 按鈕便可在瀏覽器的下載資料夾中建立檔案。  檔案的名稱是檢視的名稱，副檔名為 omsview。

![匯出檢視](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>匯入現有檢視
您可以匯入您從其他管理群組匯出的 omsview 檔案。  若要匯入現有檢視，請先建立新的檢視。  然後按一下 [匯入] 按鈕，並選取該 omsview 檔案。  檔案中的組態將會複製到現有的檢視。

![匯出檢視](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>使用檢視設計工具
檢視設計工具有三個窗格。  **設計**窗格呈現自訂檢視。  當您從 [控制] 窗格將圖格和組件新增至 [設計]，就會將它們新增檢視。  [屬性] 窗格會列出圖格或選取組件的屬性。

![[檢視設計工具]](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>設定檢視圖格
自訂檢視可以只有單一圖格。  選取 [控制] 窗格中的 [圖格] 索引標籤來檢視目前圖格或選取另一個圖格。  [屬性] 窗格會列出目前圖格的屬性。  根據 [圖格參考](log-analytics-view-designer-tiles.md) 中的詳細資訊來設定圖格屬性，然後按一下 [套用] 儲存變更。

### <a name="configure-visualization-parts"></a>設定視覺效果組件
檢視可以包含任意數目的視覺效果組件。  選取 [檢視] 索引標籤，然後要新增至檢視的視覺效果組件。  [屬性] 窗格會列出選取組件的屬性。  根據 [視覺效果組件參考](log-analytics-view-designer-parts.md) 中的詳細資訊來設定檢視的屬性，然後按一下 [套用] 儲存變更。

### <a name="delete-a-visualization-part"></a>刪除視覺效果組件
按一下組件右上角的 [X] 按鈕，即可從檢視中移除視覺效果組件。

### <a name="rearrange-visualization-parts"></a>重新整理視覺效果組件
檢視只有一列視覺效果組件。  按一下現有組件並拖曳至新的位置，即可重新排列檢視中的組件。

## <a name="next-steps"></a>後續步驟
* 將[圖格](log-analytics-view-designer-tiles.md)新增至您的自訂檢視。
* 將[視覺效果組件](log-analytics-view-designer-parts.md)新增至您的自訂檢視。

