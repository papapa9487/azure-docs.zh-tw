---
title: "開始使用適用於 Xamarin.Android 的 Azure Mobile Engagement"
description: "了解如何使用 Xamarin.Android 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: 
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 7b3d01b32c2d5a40448fc22861cd45f612238f2f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>開始使用適用於 Xamarin.Android 應用程式的 Azure Mobile Engagement
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure Mobile Engagement 來了解您應用程式的使用情形，以及如何傳送推播通知給 Xamarin.Android 應用程式的區隔使用者。
本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。 在本教學課程中，您將使用 Google 雲端通訊 (GCM)，建立可收集基本資料和接收推播通知的空白 Xamarin.Android 應用程式。

> [!NOTE]
> Azure Mobile Engagement 服務將於 2018 年 3 月淘汰，目前僅供現有客戶使用。 如需詳細資訊，請參閱 [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)。

本教學課程需要下列各項：

* [Xamarin Studio](http://xamarin.com/studio)。 您也可以使用 Visual Studio 搭配 Xamarin，但本教學課程會使用 Xamarin Studio。 如需安裝指示，請參閱[設定和安裝 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)。
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started)。
> 
> 

## <a id="setup-azme"></a>為您的 Android 應用程式設定 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端
本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。 

我們將會使用 Xamarin Studio 建立基本應用程式來示範整合。

### <a name="create-a-new-xamarinandroid-project"></a>建立新的 Xamarin.Android 專案
1. 啟動 [Xamarin Studio]，移至 [檔案] -> [新增] -> [方案] 
   
    ![][1]
2. 選取 [Android 應用程式]，然後確定選取的語言是 **C#** 並按 [下一步]。
   
    ![][2]
3. 填入 [應用程式名稱] 和 [組織識別碼]。 請確定核取 [Google Play 服務]，然後按 [下一步]。 
   
    ![][3]
4. 如有必要，請更新 [專案名稱]、[方案名稱] 和 [位置]，然後按一下 [建立]。
   
    ![][4]

Xamarin Studio 會建立應用程式，我們將在其中整合 Mobile Engagement 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>將您的應用程式連線至 Mobile Engagement 後端
1. 以滑鼠右鍵按一下 [方案] 視窗中的 [套件] 資料夾，然後選取 [新增套件...]
   
    ![][5]
2. 搜尋 **Microsoft Azure Mobile Engagement Xamarin SDK** 並將它新增至您的方案。  
   
    ![][6]
3. 開啟 **MainActivity.cs** ，並新增下列 using 陳述式：
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. 在 `OnCreate` 方法中，加入下列程式碼來初始化與 Mobile Engagement 後端的連接。 請務必新增您的 **ConnectionString**。 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>新增權限和服務宣告
1. 開啟 [屬性] 資料夾下的 **Manifest.xml** 檔案。 選取 [來源] 索引標籤，以便直接更新 XML 來源。
2. 將這些權限新增至您專案的 Manifest.xml (位於 [屬性]**`<application>` 資料夾下) 中，緊接在**  標記之前或之後：
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. 在 `<application>` 和 `</application>` 標記之間加入下列內容，宣告代理程式服務：
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. 在您剛貼上的程式碼中，更換標籤中的 `"<Your application name>"`。 這會顯示於 [設定]  功能表，使用者可在其中看到裝置上正在執行的服務。 例如，您可以在該標籤中加入「服務」這個字。

### <a name="send-a-screen-to-mobile-engagement"></a>傳送畫面到 Mobile Engagement
若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個螢幕到 Mobile Engagement 後端。 若要執行此作業 - 請確定 `MainActivity` 繼承自 `EngagementActivity`，而不是 `Activity`。

    public class MainActivity : EngagementActivity

或者，如果您無法繼承自 `EngagementActivity`，則必須分別在 `OnResume` 和 `OnPause` 中新增 `.StartActivity` 和 `.EndActivity` 方法。  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>將 App 與即時監視連接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>啟用推播通知與應用程式內傳訊的功能
Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和「觸達」。 此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。
以下各節將設定您的應用程式來接收它們。

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png

