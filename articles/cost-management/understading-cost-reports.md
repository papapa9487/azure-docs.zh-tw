---
title: "了解 Azure 成本管理的成本報告 | Microsoft Docs"
description: "本文可協助您了解 Cloudyn 報告基本結構和功能。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/27/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: df2108a6e2a01195340a09eacf1c56f9d738c923
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="understanding-cost-reports"></a>了解成本報告

本文可協助您了解 Cloudyn 報告基本結構和功能。 大部分 Cloudyn 報告是直覺式的，並且具有一致的外觀及操作。 閱讀完本文之後，就做好了使用所有報告的準備。 各種不同的報告提供了許多標準功能，讓您可以輕鬆瀏覽報告。 報告可自訂，而且可以選取數個選項來計算和呈現結果。

## <a name="report-fields-and-options"></a>報告欄位和選項

以下是成本隨時間變化的報告範例。 大部分 Cloudyn 報告都有類似的版面配置。

![範例報告](./media/understanding-cost-reports/sample-report.png)

下列資訊將詳細說明上述影像中的每個編號區域：

1. **日期範圍**

    使用日期範圍清單定義預設或自訂的報告時間間隔。
2. **儲存的篩選條件**

    使用儲存的篩選條件，可將套用至報告的目前群組和篩選條件儲存起來。 可在成本和效能報告使用儲存的篩選條件，包括：

      - 成本分析
      - 配置
      - 資產管理
      - 最佳化

  輸入篩選名稱，然後按一下 [儲存]。

3. **標記**

    使用標記分類依標記區域分組。 功能表中列出的標記是 Azure 部門或成本中心標記，或是 Cloudyn 的成本實體和訂用帳戶標記。 選取標記來篩選結果。 也可以輸入標記名稱 (關鍵字) 來篩選結果。

    ![選取選項](./media/understanding-cost-reports/select-options.png)

    按一下 [新增] 以新增新的篩選條件。

    ![新增篩選條件](./media/understanding-cost-reports/add-filter.png)

    標記群組或篩選作業與 Azure 資源或資源群組的標記無關。

    [群組] 功能表選項有成本配置標記分組和篩選的功能。

4. **報告中的群組**

    請使用成本分析報告中的群組，來顯示回報的帳單資料中的標準、分項類別。  不過，成本配置報告的群組顯示的是檢視標記型類別。 標記型類別是由計費資料的成本配置模型和標準分項類別所定義。

    ![群組標記](./media/understanding-cost-reports/groups-tags01.png)

    ![群組標記](./media/understanding-cost-reports/groups-tags02.png)

    在成本配置報告中，標記型群組類別中的群組可能包括：
      - 標記
      - 資源群組標記
      - Cloudyn 成本實體標記
      - 成本配置用途的訂用帳戶標記類別

  範例可能包括：
     - 成本中心
     - department
     - 應用程式
     - Environment
     - 成本代碼

5. **篩選條件**

    使用單一或多重選取的篩選條件，將範圍設定為選取的值。 若要設定篩選條件，請按一下 [新增]，然後選取篩選條件類別和值。

6. **成本模型**

    使用成本模型選取您先前使用成本配置 360 建立的成本模型。 端視您的成本配置需求而定，您可能有多個 Cloudyn 成本模型。 您的部份組織小組可能有彼此不同的成本配置需求。 每個小組都可以有本身專用的成本模型。

    如需建立成本配置模型定義的資訊，請參閱[使用自訂標記來配置成本](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)。

7. **分攤**

    使用成本配置報告中的分攤來檢視以線性非使用量作為基礎的服務費用或單次應支付成本，並在其存留期間，隨時間平均分散其成本。 一次性費用的範例可能包含：
    - 每年的支援費用
    - 每年的安全性元件費用
    - 保留執行個體購買費用
    - 一些 Azure Marketplace 項目。

  在 [分攤] 下，選取 [分攤成本] 或 [實際成本]。

8. **解決方案**

    使用 [解析] 選取在所選日期範圍內的時間解析。 時間解析會決定如何在報告中顯示單位，而且可以是：
    - 每日
    - 每週
    - 每月
    - 每季
    - 每年

9. **配置規則**

    使用配置規則來套用或停用成本配置成本重新計算。 您可以啟用或停用帳單資料的成本配置重新計算。 重新計算適用於報告中選取的類別。 它可讓您針對未經處理的帳單資料評估成本配置重新計算影響。

10. **未分類**

    使用 [未分類] 包含或排除報告中未分類的成本。

11. **顯示/隱藏欄位**

    顯示/隱藏選項在報告中沒有任何作用。

12.   **顯示格式**

    使用顯示格式選取不同的圖表或資料表檢視。

    ![顯示格式](./media/understanding-cost-reports/display-formats.png)

13. **多色**

    使用多色設定報告中的圖表色彩。

14. **動作**

    使用動作儲存、匯出或排定報告。

## <a name="next-steps"></a>後續步驟

- 如果您尚未完成成本管理的第一個教學課程，請在[檢閱使用量和成本](tutorial-review-usage.md)中閱讀。
