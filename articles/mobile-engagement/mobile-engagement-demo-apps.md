---
title: "Azure Mobile Engagement 示範應用程式 | Microsoft Docs"
description: "描述由何處下載、如何使用以及使用 Azure Mobile Engagement 示範應用程式的優點"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: 8381edb569e19a85c1259f7791b477cfa6e51ea3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="azure-mobile-engagement-demo-app"></a>Azure Mobile Engagement 示範應用程式
我們已針對 **iOS**、**Android** 和 **Windows** 平台發行 Azure Mobile Engagement 示範應用程式，協助您找到有用的資源，並深入了解 Mobile Engagement。

應用程式可協助您︰

* 輕鬆地找到有用的 Mobile Engagement 資源連結，例如影片、文件、支援論壇，以及可在何處提出功能要求。
* 體驗 Mobile Engagement 支援的範例通知，以獲得自己的行動應用程式的想法。
* 使用參考實作來研究如何將 Mobile Engagement 實作到您自己的應用程式。 您可以學習如何：
  
  * 收集分析資料。
  * 實作例如「全螢幕插入」或「快顯」等類型的進階通知案例。
  * 實作問卷調查和投票。
  * 實作無訊息推播與資料推播案例。   

## <a name="app-installation"></a>應用程式安裝
此應用程式會在以下應用程式市集中推出：

* **Windows Universal 示範應用程式**
  
  * 在 [Windows 應用程式市集](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2)中下載應用程式。
  * 應用程式已開發為 Windows 10 通用應用程式。 原始程式碼位於 [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows)。
* **iOS 示範應用程式**：
  
  * 在 [Apple store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090)下載應用程式。
  * 應用程式以 iOS Swift 開發。 原始程式碼位於 [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios)。
* **Android 示範應用程式**：
  
  * 在 [Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)下載應用程式。
  * 原始程式碼位於 [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android)。

![Windows Universal 示範應用程式][1]

![iOS 示範應用程式][2]
![Android 示範應用程式][3]

## <a name="usage"></a>使用量
您可以透過下列方式使用此應用程式︰

**從先前所提供的應用程式市集連結，在您的裝置上下載應用程式：**

> [!IMPORTANT]
> 您不需要 Azure 帳戶，也不需要將應用程式連接至後端。 應用程式可獨立運作。
> 
> 

* 在您的裝置上已有應用程式之後，就可以在左側功能表中瀏覽連結，尋找有關 Mobile Engagement 的有用資源。
* 我們已將 [服務 RSS 摘要](https://aka.ms/azmerssfeed) 加入此應用程式中，因此您隨時都可以得知最新的產品更新。
* 您也可以瀏覽範例通知案例，體驗 Mobile Engagement 針對各平台所支援類型的通知。 這些通知可以在本機體驗，也就是您可以按一下畫面上的按鈕，顯示與從 Mobile Engagement 平台傳送通知時相同的通知體驗。

![Windows 版應用程式功能表][4]

![適用於 iOS 的應用程式功能表][5]
![適用於 Android 的應用程式功能表][6]

**從先前所提供的 GitHub 連結下載原始程式：**

* 在您已下載原始程式碼後，請在個別的開發環境中加以開啟：適用於 iOS 的 XCode、適用於 Android 的 Android Studio、適用於 Windows 的 Visual Studio。
* 接下來您應該遵循我們的 [基本 SDK 整合步驟](mobile-engagement-windows-store-dotnet-get-started.md) ，以便能夠將此應用程式連接到自己的 Mobile Engagement 後端執行個體。
  * 您必須在應用程式中設定連接字串。
  * 您也必須設定應用程式的推播通知平台。
* 您會注意到應用程式本身會搭配 Mobile Engagement 進行檢測。 因此當您在將它連接到後端並開啟應用程式時，就能在 [監視]  索引標籤上看到使用者工作階段、活動、事件及其他項目。
* 您也能從自己的 Mobile Engagement 執行個體傳送通知給此應用程式，而不是使用本機通知。
  
  * 這裡使用應用程式的 [Get the Device ID] (取得裝置識別碼)  功能表項目將裝置加入作為測試裝置。 此能提供您裝置識別碼，接著您會向平台後端執行個體註冊為測試裝置。
    
    ![Windows 上的裝置識別碼][7]
    
    ![在 iOS 上的裝置識別碼][8]
    ![在 Android 上的裝置識別碼][9]

## <a name="key-features-of-the-demo-app"></a>示範應用程式的主要功能
* 如先前所述，使用此應用程式，您已經掌握了 Mobile Engagement 的所有重要資源。 您可以瀏覽左側功能表中的連結。
* 您可以體驗每種平台的應用程式外通知。 這些通知可以傳遞為**僅通知**，按一下通知僅會開啟應用程式的原生螢幕 (使用**深層連結**)，或是作為**網頁公告**，您可以從 Mobile Engagement 後端傳遞要在按一下通知時顯示的其他 HTML 內容。
  
    ![應用程式外通知][29]
* 在 iOS 上，您必須關閉應用程式，才能看到應用程式外通知或系統推播通知。 您可以查看此處的實作，加入**動作按鈕**，就像加入此處應用程式外通知的「意見反應」和「共用」按鈕，讓使用者可以直接從通知本身採取動作。
  
    ![iOS 上的應用程式外通知][11] ![在 iOS 上顯示的應用程式外通知][14]
* 在 Android 上，所支援的選項是在通知中加入多行文字 (**大型文字**) 或加入影像 (**概略圖**)，也會看到如同 iOS 所支援的**動作按鈕**。
  
    ![Android 上的應用程式外通知][12] ![在 Android 上顯示的應用程式外通知][15]
* 在 Windows 10 中，您可以看到通知在電腦上顯示的外觀。 此通知也會顯示在 Windows 10 **通知中心**。 目前不支援使用 Windows SDK 加入 **動作按鈕** 。
  
    ![Windows 上的應用程式外通知][10] ![Windows 上的應用程式外顯示][13]
* 您可以體驗每種平台的預設「應用程式內通知」。 這是兩個步驟的體驗，當中會先顯示 [通知] 視窗。 當您按一下時，它會開啟全螢幕的**宣告**，如下列螢幕擷取畫面所示。 此宣告的內容來自您的 Mobile Engagement 後端執行個體。 SDK 有適用於通知和宣告的範本。 您可以輕鬆地自訂這些項目，而且就像此示範應用程式所顯示的一樣，可以加上我們的標誌和色彩。  
  
    ![Windows 上的應用程式內通知][16]
  
    ![iOS 上的應用程式內通知][17]  ![Android 上的應用程式內通知][18]
  
    **iOS**、**Android**
* 您也可以使用 Mobile Engagement 的 [類別]  功能來自訂此預設體驗。 在示範應用程式中，我們已示範了兩種常見的方式來變更通知的體驗。 請注意，在 Windows SDK 中尚未支援類別功能。
  
    **全螢幕插入：**
  
    ![應用程式內通知 - 插入類別][30]
  
    ![iOS 上的插入類別][21]     ![Android 上的插入類別][22]
  
    **快顯通知：**
  
    ![應用程式內通知 - 快顯類別][31]
  
    ![iOS 上的快顯通知][19]    ![Android 上的快顯通知][20]

**iOS**、**Android**

* Mobile Engagement 也支援特殊類型的應用程式內通知，稱為 **投票**。 這讓您能將快速問卷調查傳送給您的區隔應用程式使用者。 您可以加入問題和每個問題的選項，如下列螢幕擷取畫面所示。 然後當作應用程式內通知，向應用程式使用者顯示。   
  
    ![輪詢通知][32]
  
    ![Windows 上的問卷調查][26]
  
    ![iOS 上的問卷調查][27]   ![Android 上的問卷調查][28]

**iOS**、**Android**

* Mobile Engagement 也支援傳送無聲 **資料推送** 通知。 有了這些通知，您可以從服務傳送資料 (就像下列範例中的 JSON 資料)，以便在應用程式中處理並採取某種動作。 比方說，我們如何選擇性地使用資料推送通知改變項目的價格。
  
    ![資料推送通知推播通知][33]
  
    ![Windows 上的資料推送通知][23]
  
    ![iOS 上的資料推送通知][24]  ![Android 上的資料推送通知][25]

**iOS**、**Android**

> [!NOTE]
> 您可以按一下任何範例通知畫面上的 [如需如何從 Mobile Engagement 平台傳送這些通知的指示，請按一下這裡]，檢視任何這些通知的詳細逐步指示。
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
