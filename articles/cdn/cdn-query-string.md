---
title: "使用查詢字串控制 Azure CDN 快取行為 | Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 8d79626fa8516f226a82d3dac693c2033904c91d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings"></a>使用查詢字串控制 Azure CDN 快取行為
> [!div class="op_single_selector"]
> * [標準](cdn-query-string.md)
> * [來自 Verizon 的 Azure CDN 進階](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>概觀
查詢字串快取可控制檔案內含查詢字串時的檔案快取方式。

> [!IMPORTANT]
> 「標準」與「進階」CDN 產品提供相同的查詢字串快取功能，但兩者的使用者介面不同。  本文件描述**來自 Akamai 的 Azure CDN 標準**和**來自 Verizon 的 Azure CDN 標準**的介面。  如需利用 **來自 Verizon 的 Azure CDN 進階**查詢字串快取，請參閱 [利用查詢字串控制 CDN 要求的快取行為 - 進階](cdn-query-string-premium.md)。
> 
> 

提供三種可用模式：

* **忽略查詢字串**：這是預設模式。  CDN 邊緣節點會將要求者發出的查詢字串，傳遞至第一個要求的來源並快取資產。  快取的資產到期之前，所有從邊緣節點提供且針對該資產的後續查詢皆會忽略查詢字串。
* **略過包含查詢字串之 URL 的快取**：在此模式中，系統不會於 CDN 邊緣節點快取包含查詢字串的要求。  邊緣節點會直接從來源擷取資產，然後透過每個要求將其傳遞給要求者。
* **快取每個唯一 URL**：此模式會將包含查詢字串的每個要求視為具有專屬快取的唯一資產。  例如，系統會於邊緣節點快取 *foo.ashx?q=bar* 要求的來源回應，並使用相同的查詢字串針對後續快取傳回。  針對 *foo.ashx?q=somethingelse* 要求，系統會將其快取為具專屬存留時間的個別資產。

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>變更標準 CDN 設定檔的查詢字串快取設定
1. 在 [CDN 設定檔] 刀鋒視窗中，按一下您要管理的 CDN 端點。
   
    ![[CDN 設定檔] 刀鋒視窗端點](./media/cdn-query-string/cdn-endpoints.png)
   
    隨即開啟 [CDN 端點] 刀鋒視窗。
2. 按一下 [設定]  按鈕。
   
    ![[CDN 設定檔] 刀鋒視窗管理按鈕](./media/cdn-query-string/cdn-config-btn.png)
   
    [CDN 組態] 刀鋒視窗隨即開啟。
3. 從 [查詢字串快取行為]  下拉式清單選取設定。
   
    ![CDN 查詢字串快取選項](./media/cdn-query-string/cdn-query-string.png)
4. 進行選擇後，按一下 [儲存]  按鈕。

> [!IMPORTANT]
> 設定變更可能無法立即看見，因為註冊資訊需要一段時間才能傳遍 CDN。  在 [通訊協定] <b>來自 Akamai 的 Azure CDN</b> 設定檔，通常會在一分鐘之內完成傳播。  若為<b>來自 Verizon 的 Azure CDN</b> 設定檔，則通常會在 90 分鐘之內完成傳播，但在某些情況下可能會更久。
> 
> 

