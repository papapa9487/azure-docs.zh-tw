---
title: "使用快取規則控制 Azure 內容傳遞網路快取行為 | Microsoft Docs"
description: "您可以使用 CDN 快取規則，以全域及有條件 (例如 URL 路徑和副檔名) 的方式設定或修改預設快取到期行為。"
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 8f89ef5a1763d5fc4ad09a9aeae89ccf683138c7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-caching-rules"></a>使用快取規則控制 Azure 內容傳遞網路快取行為

> [!NOTE] 
> 快取規則僅適用於**來自 Verizon 的 Azure CDN 標準**和**來自 Akamai 的 Azure CDN 標準**。 針對**來自 Verizon Premium 的 Azure CDN**，您可以使用 [管理] 入口網站中的 [Azure CDN 規則引擎](cdn-rules-engine.md)來執行類似功能。
 
Azure 內容傳遞網路提供兩種方式來控制檔案的快取方式： 

- 快取規則：本文說明如何使用內容傳遞網路 (CDN) 快取規則，以全域及有自訂條件 (例如 URL 路徑和副檔名) 的方式設定或修改預設快取到期行為。 Azure CDN 提供兩種類型的快取規則：
   - 全域快取規則：您可以針對設定檔中的每個端點設定一個全域快取規則，這會影響針對端點的所有要求。 全域快取規則會覆寫任何 HTTP 快取指示詞標頭 (如果已設定)。
   - 自訂快取規則：您可以針對設定檔中的每個端點設定一或多個自訂快取規則。 自訂快取規則會比對特定路徑和副檔名、會依序處理，並會覆寫全域快取規則 (如果已設定)。 

- 查詢字串快取：您可以調整 Azure CDN 使用查詢字串處理要求快取的方式。 如需相關資訊，請參閱[使用查詢字串控制 Azure CDN 快取行為](cdn-query-string.md)。 如果檔案無法快取，根據快取規則和 CDN 預設行為，查詢字串快取設定不會有任何效果。

如需預設快取行為和快取指示詞標頭的相關資訊，請參閱[快取如何運作](cdn-how-caching-works.md)。

## <a name="tutorial"></a>教學課程

如何設定 CDN 快取規則：

1. 開啟 Azure 入口網站，選取 CDN 設定檔，然後選取端點。
2. 在左窗格的 [設定] 下方，按一下 [快取]。
3. 建立全域快取規則，如下所示：
   1. 在 [全域快取規則] 下方，將 [查詢字串快取行為] 設定為 [忽略查詢字串]。
   2. 將 [快取行為] 設定為 [缺少時才設定]。
   3. 針對 [快取到期期間]，在 [天數] 欄位中輸入 10。

       全域快取規則會影響針對端點的所有要求。 如果原始快取指示詞標頭存在 (`Cache-Control` 或 `Expires`)，此規則就會優先採用它們；否則，系統會在未指定的情況下將快取設定為 10 天。 

4. 建立自訂快取規則，如下所示：
    1. 在 [自訂快取規則] 下方，將 [比對條件] 設定為 [路徑]，並將 [比對值] 設定為 `/images/*.jpg`。
    2. 將 [快取行為] 設定為 [覆寫]，並在 [天數] 欄位中輸入 30。
       
       此自訂快取規則會在您端點的 `/images` 資料夾中，於任何 `.jpg` 影像檔上設定 30 天的快取持續時間。 它會覆寫由原始伺服器所傳送的任何 `Cache-Control` 或 `Expires` HTTP 標頭。

  ![快取規則對話方塊](./media/cdn-caching-rules/cdn-caching-rules-dialog.png)

> [!NOTE] 
> 在規則變更之前快取的檔案會維持其原始的快取持續時間設定。 若要重設它們的快取持續時間，您必須[清除檔案](cdn-purge-endpoint.md)。 針對**來自 Verizon 的 Azure CDN** 端點，快取規則可能需要 90 分鐘才會生效。

## <a name="reference"></a>參考

### <a name="caching-behavior-settings"></a>快取行為設定
針對全域和自訂快取規則，您可以指定下列**快取行為**設定：

- **略過快取**：不要快取並忽略原始提供的快取指示詞標頭。
- **覆寫**：忽略原始提供的快取指示詞標頭，改用提供的快取持續時間。
- **缺少時才設定**：如果原始提供的快取指示詞標頭存在，就優先採用它們；否則，使用提供的快取持續時間。

### <a name="cache-expiration-duration"></a>快取到期期間
針對全域和自訂快取規則，您能以天數、小時數、分鐘數和秒數來指定快取到期期間：

- 針對 [覆寫] 和 [缺少時才設定] 的**快取行為**設定，有效快取持續時間的範圍介於 0 秒到 366 天。 若值為 0 秒，CDN 會快取內容，但必須向原始伺服器重新驗證每個要求。
- 針對 [略過快取] 設定，快取持續時間會自動設為 0 秒且無法變更。

### <a name="custom-caching-rules-match-conditions"></a>自訂快取規則比對條件

針對自訂快取規則，有兩個可用的比對條件：
 
- **路徑**：這個條件會比對 URL 的路徑，排除網域名稱，並支援萬用字元符號 (\*)。 例如，`/myfile.html`、`/my/folder/*` 及 `/my/images/*.jpg`。 長度上限是 260 個字元。

- **副檔名**：這個條件會比對所要求檔案的副檔名。 您可以提供一份要比對的副檔名清單 (以逗號分隔)。 例如，`.jpg`、`.mp3` 或 `.png`。 副檔名的個數上限是 50，且每個副檔名的最大字元數目為 16。 

### <a name="global-and-custom-rule-processing-order"></a>全域和自訂規則處理順序
全域和自訂快取規則會依下列順序處理：

- 全域快取規則的優先順序高於預設的 CDN 快取行為 (HTTP 快取指示詞標頭設定)。 

- 自訂快取規則的優先順序高於全域快取規則 (在其適用處)。 自訂快取規則的處理順序為從上而下。 也就是說，如果要求符合這兩個條件，位於清單底部之規則的優先順序會高於位於清單頂端的規則。 因此，您應該將更特定的規則置於清單的較低處。

**範例**：
- 全域快取規則： 
   - 快取行為：**覆寫**
   - 快取到期期間：1 天

- 自訂快取規則 #1：
   - 比對條件：**路徑**
   - 比對值：`/home/*`
   - 快取行為：**覆寫**
   - 快取到期期間：2 天

- 自訂快取規則 #2：
   - 比對條件：**副檔名**
   - 比對值：`.html`
   - 快取行為：**缺少時才設定**
   - 快取到期期間：3 天

若設定了這些規則，針對 `<endpoint>.azureedge.net/home/index.html` 的要求就會觸發自訂快取規則 #2，它會設定為：**缺少時才設定**和 3 天。 因此，如果 `index.html` 檔案具有 `Cache-Control` 或 `Expires` HTTP 標頭，則會優先採用它們，否則，如果未設定這些標頭，就會將檔案快取 3 天。

