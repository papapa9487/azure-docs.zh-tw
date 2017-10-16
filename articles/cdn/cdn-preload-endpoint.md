---
title: "在 Azure CDN 端點上預先載入資產 | Microsoft Docs"
description: "了解如何預先載入 Azure CDN 端點上的快取內容。"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 1f2dcd9a91bb6e883cbef06373c1acd98bf8d45f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>在 Azure CDN 端點上預先載入資產
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

根據預設，資產被要求時會先快取。 這表示每個區域的第一個要求可能需要較長的時間，因為 Edge Server 沒有快取的內容，而且必須將要求轉送至原始伺服器。 預先載入內容以避免此第一次點擊的延遲。

除了提供較佳的客戶體驗，預先載入快取的資產也可以減少原始伺服器上的網路流量。

> [!NOTE]
> 預先載入資產對於大型事件或同時提供給大量使用者的內容很有用，例如新的影片版本或軟體更新。
> 
> 

本教學課程將逐步引導您在 Azure CDN 邊緣節點上預先載入快取的所有內容。

## <a name="walkthrough"></a>逐步介紹
1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到包含您希望預載之端點的 CDN 設定檔。  設定檔刀鋒視窗隨即開啟。
2. 按一下清單中的端點。  端點刀鋒視窗隨即開啟。
3. 從 CDN 端點刀鋒視窗，按一下 [載入] 按鈕。
   
    ![CDN 端點刀鋒視窗](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    [載入] 刀鋒視窗隨即開啟。
   
    ![CDN 載入刀鋒視窗](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. 在 [路徑] 文字方塊中輸入每個您希望載入之資產的完整路徑 (例如，`/pictures/kitten.png`)。
   
   > [!TIP]
   > 在您輸入文字之後會出現更多 [路徑] 文字方塊，讓您能夠建立多個資產的清單。  按一下刪節號 (...) 按鈕可以將資產從清單刪除。
   > 
   > 路徑必須是符合下列[規則運算式](https://msdn.microsoft.com/library/az24scfc.aspx)的相對 URL：  
   > >載入單一檔案路徑 `@"^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$"`；  
   > >以查詢字串載入單一檔案 `@"^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$";`  
   > 
   > 每個資產都必須有自己的路徑。  預先載入資產沒有萬用字元功能。
   > 
   > 
   
    ![載入按鈕](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. 按一下 [載入]  按鈕。
   
    ![載入按鈕](./media/cdn-preload-endpoint/cdn-load-button.png)

> [!NOTE]
> 每個 CDN 設定檔都有每分鐘 10 個載入要求的限制。 每個要求允許 50 個路徑。 每個路徑都有 1024 個字元的路徑長度限制。
> 
> 

## <a name="see-also"></a>另請參閱
* [清除 Azure CDN 端點](cdn-purge-endpoint.md)
* [Azure CDN REST API 參考資料 - 清除或預先載入端點](https://msdn.microsoft.com/library/mt634451.aspx)

