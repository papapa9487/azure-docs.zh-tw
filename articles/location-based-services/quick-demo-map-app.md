---
title: "使用 Azure 位置服務的互動式地圖搜尋 | Microsoft Docs"
description: "Azure 快速入門 - 使用 Azure 位置服務 (預覽版) 啟動示範互動式地圖搜尋"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: quickstart
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0fcd25183617de879ada6d1f7d2a8fcf9551d6de
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="launch-a-demo-interactive-map-search-using-azure-location-based-services-preview"></a>使用 Azure 位置服務 (預覽版) 啟動示範互動式地圖搜尋

本文會使用 Azure Map 來示範 Azure LBS (預覽版，簡稱 LBS) 的功能。 也會引導您完成建立自己 LBS 帳戶的基本步驟，並取得用於示範 Web 應用程式中的帳戶金鑰。 

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-location-based-services-account-and-get-account-key"></a>建立位置服務帳戶並取得帳戶金鑰

1. 按一下 [Azure 入口網站](https://portal.azure.com)左上角的 [建立資源]。
2. 在 [搜尋 Marketplace] 方塊中，輸入 [位置服務]。
3. 按一下 [結果] 中的 [位置服務 (預覽)]。 按一下地圖下方顯示的 [建立] 按鈕。 
4. 在 [建立位置服務帳戶] 頁面上，輸入新帳戶的 [名稱]、選取要使用的 [訂用帳戶]，然後輸入新的或現有的 [資源群組]。 選取資源群組的位置，接受 [預覽條款]，然後按一下 [建立]。

    ![在入口網站中建立位置服務帳戶](./media/quick-demo-map-app/create-lbs-account.png)

5. 成功建立您的帳戶之後，請將其開啟並瀏覽到帳戶的 [設定]。 按一下 [金鑰] 可取得您帳戶的主要和次要訂用帳戶金鑰。 將 [主要金鑰] 值複製到本機剪貼簿，下一節將使用此值。 

## <a name="download-the-demo-application-for-azure-maps"></a>下載 Azure Map 示範應用程式

1. 下載或複製 [interactiveSearch.html](https://github.com/Azure-Samples/location-based-services-samples/blob/master/src/interactiveSearch.html) 檔案的內容。
2. 將此檔案的內容在本機另存為 **AzureMapDemo.html**，並在文字編輯器中開啟。
3. 搜尋 **<insert-key>**字串，並以上一節中所取得的 [主要金鑰] 值加以取代。 


## <a name="launch-the-demo-application-for-azure-maps"></a>啟動 Azure Map 示範應用程式

1. 在您選擇的瀏覽器中開啟 **AzureMapDemo.html** 檔案。
2. 查看地圖所顯示的洛杉磯市。 顯示的城市是由 *AzureMapDemo.html* 中，提供給名為 **center** 的 JavaScript 變數 `[longitude, latitude]` 配對值所決定。 您可以將這些座標變更為您選擇的任何其他城市。 例如，紐約市的座標是 *[-74.0060，40.7128]*。
3. 請在示範 Web 應用程式左上角的搜尋方塊中，輸入您想要搜尋的任何位置類型或地址。 
4. 將滑鼠移到搜尋方塊下方顯示的地址/地點清單上，便可注意到地圖上對應的圖釘會彈出該地點的相關資訊。 例如，此 Web 應用程式的範例啟動會搜尋 [餐廳] 並得到下列結果。 請注意，為了保護私人公司的隱私權，在此顯示的是虛構的名稱和地址。 

    ![互動式搜尋 Web 應用程式](./media/quick-demo-map-app/lbs-interactive-search.png)


## <a name="clean-up-resources"></a>清除資源

本教學課程會詳細說明如何針對您的帳戶來使用和設定 Azure LBS。 如果您打算繼續進行教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟來刪除本快速入門建立的所有資源。

1. 關閉執行 **AzureMapDemo.html** Web 應用程式的瀏覽器。
2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後選取您的 LBS 帳戶。 在 [所有資源] 刀鋒視窗的頂端，按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

本快速入門中，您已建立您的 Azure LBS 帳戶，並使用您的帳戶啟動了示範應用程式。 若要了解如何使用 Azure LBS API 建立自己的應用程式，請繼續下列教學課程。

> [!div class="nextstepaction"]
> [使用者 Azure Map 和搜尋的教學課程](./tutorial-search-location.md)
