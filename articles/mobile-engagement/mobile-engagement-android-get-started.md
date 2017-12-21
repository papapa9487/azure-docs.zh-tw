---
title: "開始使用 Android 應用程式 Azure Mobile Engagement"
description: "了解如何使用 Android 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 4acd06591b05b9328b35400e0932cfcf92cb7e80
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>開始使用適用於 Android 應用程式的 Azure Mobile Engagement
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure Mobile Engagement 來了解您應用程式的使用情形，以及如何將推播通知傳送給 Android 應用程式的分段使用者。
本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。 在此案例中，您先建立空白的 Android 應用程式，使用 Google 雲端通訊 (GCM) 來收集基本資料並接收推播通知。

## <a name="prerequisites"></a>必要條件
完成本教學課程需要 [Android Developer Tools](https://developer.android.com/sdk/index.html)，其中包括 Android Studio 整合式開發環境，以及最新的 Android 平台。

還需要 [Mobile Engagement Android SDK](https://aka.ms/vq9mfn)。

> [!IMPORTANT]
> 若要完成此教學課程，您需要一個有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started)。
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>為您的 Android 應用程式設定 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>將您的應用程式連線至 Mobile Engagement 後端
本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。 您使用 Android Studio 建立一個基本應用程式來示範整合。

完整的整合文件位於 [Mobile Engagement Android SDK 整合](mobile-engagement-android-sdk-overview.md)中。

### <a name="create-an-android-project"></a>建立 Android 專案
1. 啟動 **Android Studio**，然後在快顯視窗中選取 [開始新的 Android Studio 專案]。

    ![][1]
2. 提供 App 名稱與公司網域。 記下您填入的內容，因為稍後會用到。 按一下 [下一步] 。

    ![][2]
3. 選取目標尺寸和 API 層級，然後按 [下一步] 。

   > [!NOTE]
   > Mobile Engagement 至少需要 API 層級 10 (Android 2.3.3)。
   >
   >

    ![][3]
4. 在此處選取 [空白活動]，這是此應用程式唯一的畫面，然後按 [下一步]。

    ![][4]
5. 最後，依原樣保留預設值，然後按一下 [完成] 。

    ![][5]

Android Studio 現在要建立會和 Mobile Engagement 整合的示範應用程式。

### <a name="include-the-sdk-library-in-your-project"></a>在您的專案中包含 SDK 程式庫
1. 下載 [Mobile Engagement Android SDK](https://aka.ms/vq9mfn)。
2. 將封存檔案解壓縮至電腦中的資料夾。
3. 找出此 SDK 目前版本的 .jar 程式庫，並將它複製到剪貼簿。

      ![][6]
4. 瀏覽到 [專案]  區段 (1)，然後將 .jar 貼到 libs 資料夾 (2)。

      ![][7]
5. 若要載入程式庫，請同步處理專案。

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>使用「連線字串」將您的應用程式連線到 Mobile Engagement 後端
1. 將下列程式碼行複製到活動建立中 (必須只在應用程式中的一個位置完成，通常是主要活動)。 針對此範例 App，開啟 src -> main -> java 資料夾下方的 MainActivity，然後新增下列內容：

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. 按 Alt + Enter 鍵或新增下列匯入陳述式來解析參考：

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. 回到 Azure 傳統入口網站中您應用程式的 [連線資訊] 頁面，並複製 [連接字串]。

      ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)

4. 將它貼到 `setConnectionString` 參數內，取代如下列程式碼所示的整個字串︰

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>新增權限和服務宣告
1. 將這些權限加入至您專案的 Manifest.xml 中，緊接在 `<application>` 標記之前或之後：

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. 若要宣告代理程式服務，在 `<application>` 和 `</application>` 標籤之間新增此程式碼：

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. 在您貼上的程式碼內，取代標籤中的 `"<Your application name>"`，標籤會顯示在 [設定] 功能表中，該處可以看到裝置上執行的服務。 例如，您可以在該標籤中加入「服務」這個字。

### <a name="send-a-screen-to-mobile-engagement"></a>傳送畫面到 Mobile Engagement
若要開始傳送資料並確定使用者正在使用，您必須至少將一個畫面 (活動) 傳送到 Mobile Engagement 後端。

請前往 **MainActivity.java**，然後新增下列項目，以便將 **MainActivity** 的基底類別取代為 **EngagementActivity**：

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> 如果基底類別不是 *Activity*，請參閱[進階 Android 報告](mobile-engagement-android-advanced-reporting.md)以了解如何從不同的類別繼承。
>
>

針對此簡單範例案例，在下一行進行註解：

    // setSupportActionBar(toolbar);

如果您想要將 `ActionBar` 保留在應用程式中，請參閱 [進階 Android 報告](mobile-engagement-android-advanced-reporting.md)。

## <a name="connect-app-with-real-time-monitoring"></a>將應用程式與即時監視連接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>啟用推播通知與應用程式內傳訊
活動進行期間，Mobile Engagement 可讓您透過推播通知和應用程式內傳訊與使用者互動和「觸達」。 此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。
接下來的一節將設定您的應用程式來接收它們。

### <a name="copy-sdk-resources-in-your-project"></a>複製您專案中的 SDK 資源
1. 瀏覽回您的 SDK 下載內容，並且複製 **res** 資料夾。

    ![][10]
2. 返回 Android Studio，選取專案檔案的 **main** 目錄，然後貼上以將資源加入您的專案。

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>後續步驟
移至 [Android SDK](mobile-engagement-android-sdk-overview.md) 以取得有關 SDK 整合的詳細資訊。

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
