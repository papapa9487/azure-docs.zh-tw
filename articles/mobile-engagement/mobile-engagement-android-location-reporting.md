---
title: "Azure Mobile Engagement Android SDK 位置報告"
description: "描述如何設定 Azure Mobile Engagement Android SDK 位置報告"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 777d5719cce505b55dfb61c91dcac7e713b077a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Azure Mobile Engagement Android SDK 位置報告
> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

本主題說明如何為您的 Android 應用程式進行位置報告。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>位置報告
如果想要報告位置，您需要加入幾行組態 (在 `<application>` 和 `</application>` 標記之間)。

### <a name="lazy-area-location-reporting"></a>簡易區域位置報告
簡易區域位置報告可報告國家、地區以及與裝置相關聯的位置。 這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)。 每個工作階段最多報告一次裝置區域。 絕不會使用 GPS，因此這類位置報告對於電池的影響很小。

報告的區域用來計算關於使用者、工作階段、事件與錯誤的地理統計資料。 它們也可用來做為觸達活動的準則。

您可以使用此程序中先前所述的組態，啟用簡易區域位置報告：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您也必須指定位置權限。 此程式碼使用 ``COARSE`` 權限︰

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

如果您的應用程式需要，可以改用 ``ACCESS_FINE_LOCATION`` 。

### <a name="real-time-location-reporting"></a>即時位置報告
即時位置報告可報告與裝置相關聯的緯度和經度。 這類位置報告預設只會根據基地台識別碼或 WIFI 使用網路位置。 報告只在應用程式在前景 (例如在工作階段) 中執行時才會為作用中。

即時位置「不會」  用來計算統計資料。 其唯一用途，是允許在觸達活動中使用即時地理圍欄 \<Reach-Audience-geofencing\> 準則。

若要啟用即時位置報告，請在您在啟動器活動中設定 Engagement 連接字串的地方加入一行程式碼。 結果看起來如下：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS 式報告
根據預設，即時位置報告只會使用網路位置。 若要啟用 GPS 位置 (精準度大為提高)，請使用組態物件：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您還需要新增下列權限 (如果未有此權限)：

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>背景報告
根據預設，即時位置報告只在應用程式在前景 (例如在工作階段) 中執行時才會為作用中。 若也要在背景中啟用報告，請使用此組態物件：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> 當應用程式在背景中執行，即使啟用 GPS，也只會報告網路位置。
> 
> 

如果使用者重新啟動他們的裝置，則會停止背景位置報告。 若要在開機時自動重新啟動，請加入下列程式碼。

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

您還需要新增下列權限 (如果未有此權限)：

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M 權限
從 Android M 開始，某些權限會在執行階段管理，而且需要使用者核准。

如果您針對 Android API 層級 23，新的應用程式安裝預設會關閉執行階段權限。 否則預設會開啟。

您可以從裝置設定功能表啟用/停用這些權限。 從系統功能表關閉權限會刪除應用程式的背景處理程序，這是一種系統行為，對於在背景中接收推播的功能不會有任何影響。

在 Mobile Engagement 位置報告的環境中，需要在執行階段核准的權限為：

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

使用標準系統對話方塊向使用者要求權限。 如果使用者核准，告訴 ``EngagementAgent`` 即時列入這項變更。 否則變更會在下次使用者啟動應用程式時處理。

以下是在應用程式要求權限並轉送結果 (如果 ``EngagementAgent``收到肯定) 時的程式碼範例：

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
