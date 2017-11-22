---
title: "使用查詢字串控制 Azure CDN 快取行為 - Premium | Microsoft Docs"
description: "Azure CDN 查詢字串快取可控制檔案內含查詢字串時的檔案快取方式。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: ba9c28f0e6df25b101b45edf836d0b95056cbc6f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>使用查詢字串控制 Azure 內容傳遞網路快取行為 - 進階
> [!div class="op_single_selector"]
> * [標準](cdn-query-string.md)
> * [來自 Verizon 的 Azure CDN 進階](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>概觀
使用 Azure 內容傳遞網路 (CDN)，可以控制 Web 要求內含查詢字串時的檔案快取方式。 在包含查詢字串的 Web 要求中，查詢字串是要求之中 `?` 字元之後的部分。 查詢字串可包含一或多個參數，以 `&` 字元分隔參數。 例如： `http://www.domain.com/content.mov?data1=true&data2=false`。 如果要求中不止一個查詢字串參數，參數的順序不影響作業。 

> [!IMPORTANT]
> 「標準」與「進階」CDN 產品提供相同的查詢字串快取功能，但兩者的使用者介面不同。  本文描述**來自 Verizon 的進階 Azure CDN** 介面。 如需利用**來自 Akamai 的 Azure CDN 標準**和**來自 Verizon 的 Azure CDN 標準**查詢字串快取，請參閱[利用查詢字串控制 CDN 要求的快取行為](cdn-query-string.md)。
>

查詢字串模式有三種：

- **標準快取**：預設模式。 在此模式中，CDN 邊緣節點會將要求者發出的查詢字串，傳遞至第一個要求的來源並快取資產。 快取的資產到期之前，所有從邊緣節點提供且針對該資產的後續查詢皆會忽略查詢字串。
- **無快取**：在此模式中，系統不會於 CDN 邊緣節點快取使查詢字串的要求。 邊緣節點會直接從來源擷取資產，然後透過每個要求將其傳遞給要求者。
- **唯一快取**：在此模式中，每個要求都有一個唯一 URL (包含查詢字串)，會被視為具有專屬快取的唯一資產。 例如，系統會於邊緣節點快取 `example.ashx?q=test1` 要求的來源回應，並使用相同的查詢字串針對後續快取傳回。 系統快取針對 `example.ashx?q=test2` 的要求，會將其視為具有專屬存留時間設定的個別資產。

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>變更進階 CDN 設定檔的查詢字串快取設定
1. 開啟 CDN 設定檔，然後按一下 [管理]。
   
    ![CDN 設定檔 [管理] 按鈕](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    隨即開啟 CDN 管理入口網站。
2. 將滑鼠移至 [HTTP 大型] 索引標籤上，然後將滑鼠移至 [快取設定] 飛出功能表上。 按一下 [查詢字串快取]。
   
    查詢字串快取選項隨即顯示。
   
    ![CDN 查詢字串快取選項](./media/cdn-query-string-premium/cdn-query-string.png)
3. 選取查詢字串模式，然後按一下 [更新]。

> [!IMPORTANT]
> 因為註冊資訊需要一段時間才能傳遍 CDN，快取字串設定的變更可能無法立即看見。 以**來自 Verizon 的進階 Azure CDN** 設定檔來說，通常會在 90 分鐘之內完成傳播，但在某些情況下可能會更久。
 

