---
title: "Azure Mobile Engagement Android SDK 的進階報告選項"
description: "描述如何為 Azure Mobile Engagement Android SDK 進行進階報告以擷取分析"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 2a1445afa2c2fca1a31ad9c012b9c8a917ebf65c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Android 上使用 Engagement 的進階報告
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

本主題說明 Android 應用程式中的其他報告案例。 您可以將這些選項套用至 [快速入門](mobile-engagement-android-get-started.md) 教學課程中建立的應用程式。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

您完成的教學課程相當直接明瞭，但您還有一些進階選項可選擇。

## <a name="modifying-your-activity-classes"></a>修改 `Activity` 類別
在[快速入門教學課程](mobile-engagement-android-get-started.md)中，您只需要讓 `*Activity` 子類別繼承對應的 `Engagement*Activity` 類別即可。 例如，如果您的舊版活動延伸 `ListActivity`，可以將它延伸 `EngagementListActivity`。

> [!IMPORTANT]
> 使用 `EngagementListActivity` 或 `EngagementExpandableListActivity` 時，務必先呼叫 `requestWindowFeature(...);` 再呼叫 `super.onCreate(...);`，否則會發生當機。
> 
> 

您可以在 `src` 資料夾中找到這些類別，並將其複製到您的專案。 這些類別也會顯示在 **JavaDoc** 中。

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>替代方法：手動呼叫 `startActivity()` 和 `endActivity()`
如果您無法或不想多載 `Activity` 類別，可以改為直接呼叫 `EngagementAgent` 的方式來開始和結束您的活動。

> [!IMPORTANT]
> Android SDK 絕不會呼叫 `endActivity()` 方法，即使在關閉應用程式後也不會呼叫 (在 Android 上，應用程式永遠不會關閉)。 因此，「強烈」建議在您「所有」活動的 `onResume` 回呼中呼叫 `startActivity()` 方法，以及在您「所有」活動的 `onPause()` 回呼中呼叫 `endActivity()` 方法。 這是確保不會遺漏工作階段的唯一方法。 如果遺漏了工作階段，Engagement 服務永遠不會從 Engagement 後端中斷連線 (因為只要工作階段處於暫止狀態，服務就會保持連線)。
> 
> 

下列是一個範例：

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

這個範例類似於 `EngagementActivity` 類別及其變體，原始程式碼位於 `src` 資料夾中。

## <a name="using-applicationoncreate"></a>使用 Application.onCreate()
您放置在 `Application.onCreate()` 和其他應用程式回呼中的程式碼，會針對您所有應用程式的處理程序而執行，包括 Engagement 服務。 可能會產生不必要的副作用，例如 Engagement 處理程序中有不必要的記憶體配置和執行緒，或重複的廣播接收器或服務。

如果覆寫 `Application.onCreate()`，建議在 `Application.onCreate()` 函式的開頭加入下列程式碼片段：

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

您可以對 `Application.onTerminate()`、`Application.onLowMemory()` 和 `Application.onConfigurationChanged(...)` 執行相同的動作。

您也可以不延伸 `Application`，改為延伸 `EngagementApplication`：回呼 `Application.onCreate()` 會進行處理程序檢查，並在目前的處理程序不是裝載 Engagement 服務的處理程序時才會呼叫 `Application.onApplicationProcessCreate()`，相同規則也適用於其他回呼。

## <a name="tags-in-the-androidmanifestxml-file"></a>AndroidManifest.xml 檔案中的標籤
在 AndroidManifest.xml 檔案中的 service 標籤中， `android:label` 屬性可讓您選擇 Engagement 服務的名稱，此名稱會出現在使用者電話的「執行中服務」畫面中。 建議將此屬性設定為 `"<Your application name>Service"` (例如 `"AcmeFunGameService"`)。

指定 `android:process` 屬性可確保 Engagement 服務在本身的處理程序中執行 (在與應用程式相同的處理程序中執行 Engagement，可能會造成主要/UI 執行緒回應速度較慢)。

## <a name="building-with-proguard"></a>使用 ProGuard 建置
如果您使用 ProGuard 建立應用程式封裝，您需要保留一些類別。 您可以使用下列組態程式碼片段：

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }
