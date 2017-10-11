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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 145067c2ce50b41c4783f4de4052c0e7cb529fc7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium"></a>使用查詢字串控制 Azure CDN 快取行為 - Premium
> [!div class="op_single_selector"]
> * [標準](cdn-query-string.md)
> * [來自 Verizon 的 Azure CDN 進階](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>概觀
查詢字串快取可控制檔案內含查詢字串時的檔案快取方式。

> [!IMPORTANT]
> 「標準」與「進階」CDN 產品提供相同的查詢字串快取功能，但兩者的使用者介面不同。  本文件描述 **來自 Verizon 的 Azure CDN 進階**的介面。  如需利用**來自 Akamai 的 Azure CDN 標準**和**來自 Verizon 的 Azure CDN 標準**查詢字串快取，請參閱[利用查詢字串控制 CDN 要求的快取行為](cdn-query-string.md)。
> 
> 

提供三種可用模式：

* **標準快取**：此為預設模式。  CDN 邊緣節點會將要求者發出的查詢字串，傳遞至第一個要求的來源並快取資產。  快取的資產到期之前，所有從邊緣節點提供且針對該資產的後續查詢皆會忽略查詢字串。
* **無快取**：在此模式中，系統不會於 CDN 邊緣節點快取包含查詢字串的要求。  邊緣節點會直接從來源擷取資產，然後透過每個要求將其傳遞給要求者。
* **唯一快取**：此模式會將包含查詢字串的每個要求視為具有專屬快取的唯一資產。  例如，系統會於邊緣節點快取 *foo.ashx?q=bar* 要求的來源回應，並使用相同的查詢字串針對後續快取傳回。  針對 *foo.ashx?q=somethingelse* 要求，系統會將其快取為具專屬存留時間的個別資產。

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>變更進階 CDN 設定檔的查詢字串快取設定
1. 在 [CDN 設定檔] 刀鋒視窗中，按一下 [管理]  按鈕。
   
    ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    隨即開啟 CDN 管理入口網站。
2. 將滑鼠移至 [HTTP 大型] 索引標籤上，然後將滑鼠移至 [快取設定] 飛出視窗上。  按一下 [ **查詢字串快取**]。
   
    查詢字串快取選項隨即顯示。
   
    ![CDN 查詢字串快取選項](./media/cdn-query-string-premium/cdn-query-string.png)
3. 進行選擇後，按一下 [ **更新** ] 按鈕。

> [!IMPORTANT]
> 設定變更可能無法立即看見，因為註冊資訊需要一段時間才能傳遍 CDN。  若為<b>來自 Verizon 的 Azure CDN</b> 設定檔，則通常會在 90 分鐘之內完成傳播，但在某些情況下可能會更久。
> 
> 

