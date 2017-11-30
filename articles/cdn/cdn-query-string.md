---
title: "使用查詢字串控制 Azure 內容傳遞網路快取行為 | Microsoft Docs"
description: "Azure CDN 查詢字串快取可控制檔案內含查詢字串時的檔案快取方式。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 04c9ad5e58af073204eb6a16df96f0517a0ee668
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>使用查詢字串控制 Azure 內容傳遞網路快取行為
> [!div class="op_single_selector"]
> * [標準](cdn-query-string.md)
> * [來自 Verizon 的 Azure CDN 進階](cdn-query-string-premium.md)
> 

## <a name="overview"></a>概觀
使用 Azure 內容傳遞網路 (CDN)，可以控制 Web 要求內含查詢字串時的檔案快取方式。 在包含查詢字串的 Web 要求中，查詢字串是要求中問號 (?) 之後的部分。 查詢字串可以包含一或多個索引鍵/值組，其中的欄位名稱與其值是以等號 (=) 分隔。 每個索引鍵/值組是以 & 符號分隔。 例如 `http://www.contoso.com/content.mov?field1=value1&field2=value2`。 如果要求的查詢字串中有不止一個索引鍵/值組，其順序無關緊要。 

> [!IMPORTANT]
> 「標準」與「進階」CDN 產品提供相同的查詢字串快取功能，但兩者的使用者介面不同。  本文描述**來自 Akamai 的 Azure CDN 標準**和**來自 Verizon 的 Azure CDN 標準**的介面。 如需利用 **來自 Verizon 的 Azure CDN 進階**查詢字串快取，請參閱 [利用查詢字串控制 CDN 要求的快取行為 - 進階](cdn-query-string-premium.md)。

查詢字串模式有三種：

- **忽略查詢字串**：預設模式。 在此模式中，CDN 邊緣節點會將要求者發出的查詢字串，傳遞至第一個要求的來源並快取資產。 快取的資產到期之前，所有從邊緣節點提供且針對該資產的後續查詢皆會忽略查詢字串。
- **略過包含查詢字串的快取**：在此模式中，系統不會於 CDN 邊緣節點快取包含查詢字串的要求。 邊緣節點會直接從來源擷取資產，然後透過每個要求將其傳遞給要求者。
- **快取所有不重複的 URL**：在此模式中，每個要求都有一個唯一的 URL (包含查詢字串)，會被視為具有專屬快取的唯一資產。 例如，系統會於邊緣節點快取 `example.ashx?q=test1` 要求的來源回應，並使用相同的查詢字串針對後續快取傳回。 系統快取針對 `example.ashx?q=test2` 的要求，會將其視為具有專屬存留時間設定的個別資產。

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>變更標準 CDN 設定檔的查詢字串快取設定
1. 開啟的 CDN 設定檔，然後選取您想要管理的 CDN 端點。
   
   ![CDN 設定檔端點](./media/cdn-query-string/cdn-endpoints.png)
   
2. 在 [設定] 下方，按一下 [快取]。
   
    ![[CDN 設定檔快取] 按鈕](./media/cdn-query-string/cdn-cache-btn.png)
   
3. 在 [查詢字串快取行為] 清單中，選取查詢字串模式，然後按一下 [儲存]。
   
  <!--- Replace screen shot after general caching goes live ![CDN query string caching options](./media/cdn-query-string/cdn-query-string.png) --->

> [!IMPORTANT]
> 因為註冊資訊需要一段時間才能傳遍 CDN，快取字串設定的變更可能無法立即看見。 若為 **來自 Akamai 的 Azure CDN** 設定檔，通常會在一分鐘之內完成傳播。 若為**來自 Verizon 的 Azure CDN** 設定檔，則通常會在 90 分鐘之內完成傳播，但在某些情況下可能會更久。


