---
title: "開始使用適用於 Web Apps 的 Azure Mobile Engagement | Microsoft Docs"
description: "了解如何使用 Web Apps 的 Azure Mobile Engagement 與分析和推播通知。"
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: abcb04e4e0a3ae4fdba3a4ded20b3846ac3b21e6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>開始使用適用於 Web Apps 的 Azure Mobile Engagement
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure Mobile Engagement 來了解 Web 應用程式的使用量。

> [!NOTE]
> Azure Mobile Engagement 服務將於 2018 年 3 月淘汰，目前僅供現有客戶使用。 如需詳細資訊，請參閱 [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)。

本教學課程需要下列各項：

* Visual Studio 2015 或您選擇的其他任何編輯器
* [Web SDK](http://aka.ms/P7b453)

此 Web SDK 目前為預覽狀態，目前僅支援分析，尚未支援傳送瀏覽器或應用程式內推播通知。 

> [!NOTE]
> 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started)。
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>為 Web 應用程式設定 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端
本教學課程將說明「基本整合」，這是收集資料時必要的最低設定。

我們將使用 Visual Studio 來建立基本 Web 應用程式，以便示範整合，但您也可以使用在 Visual Studio 之外建立的任何 Web 應用程式來進行這些步驟。 

### <a name="create-a-new-web-app"></a>建立新的 Web 應用程式
即使下列步驟與舊版 Visual Studio 中的步驟類似，但這些步驟假設使用的是 Visual Studio 2015。 

1. 啟動 Visual Studio，並在 [首頁] 畫面上選取 [新增專案]。
2. 在快顯視窗中，選取 [Web] -> [ASP.Net Web 應用程式]。 填入應用程式的 [名稱]、[位置] 和 [方案名稱]，然後按一下 [確定]。
3. 在 [選取範本] 快顯視窗中，選取 [ASP.Net 4.5 範本] 底下的 [空白]，然後按一下 [確定]。 

現在已建立新的空白 Web 應用程式專案，接著我們要將 Azure Mobile Engagement Web SDK 整合至其中。

### <a name="connect-your-app-to-mobile-engagement-backend"></a>將您的應用程式連線至 Mobile Engagement 後端
1. 在方案中建立稱為 **javascript** 的新資料夾，並在其中新增 Web SDK JS 檔案 **azure-engagement.js**。 
2. 在此 javascript 資料夾中，新增名為 **main.js** 的新檔案和下列程式碼。 請務必更新連接字串。 此 `azureEngagement` 物件將會用來存取 Web SDK 方法。 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![Visual Studio 和 js 檔案][1]

## <a name="enable-real-time-monitoring"></a>啟用即時監視
若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個活動到 Mobile Engagement 後端。 在 Web 應用程式的內容中，活動就是網頁。 

1. 在方案中建立名為 **home.html** 的新頁面，並將其設為 Web 應用程式的開始頁面。 
2. 在 body 標籤內新增下列內容，以在此頁面中包含我們稍早新增的兩個 javascript。 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. 更新 body 標籤以呼叫 EngagementAgent 的 `startActivity` 方法
   
        <body onload="engagement.agent.startActivity('Home')">
4. 您的 **home.html** 看起來會像下面這樣
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-real-time-monitoring"></a>將應用程式與即時監視連接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>擴充分析
以下是 Web SDK 中目前可用於分析的所有可用方法︰

1. 活動/網頁︰
   
        engagement.agent.startActivity(name);
        engagement.agent.endActivity();
2. 事件
   
        engagement.agent.sendEvent(name, extras);
3. 錯誤數
   
        engagement.agent.sendError(name, extras);
4. 作業
   
        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png


