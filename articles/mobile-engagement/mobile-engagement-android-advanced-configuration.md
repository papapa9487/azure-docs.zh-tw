---
title: "Azure Mobile Engagement Android SDK 的進階組態"
description: "描述包含隨附 Azure Mobile Engagement Android SDK 之 Android 資訊清單的進階組態選項"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 0301f71c76872714aa1bf727a6c21dd7a63db036
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Azure Mobile Engagement Android SDK 的進階組態
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

此程序描述如何設定 Azure Mobile Engagement Android 應用程式的各種組態選項。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>權限需求
有一些選項需要特定權限，全部列在這裡方便參考，也內嵌在特定功能中。 將這些權限加入專案的 AndroidManifest.xml 中，而且要緊接在 `<application>` 標記之前或之後。

請根據下表填入適當的權限，權限程式碼必須看起來如下所示。

    <uses-permission android:name="android.permission.[specific permission]"/>


| 權限 | 使用時機 |
| --- | --- |
| 網際網路 |必要。 針對基本報告 |
| ACCESS_NETWORK_STATE |必要。 針對基本報告 |
| RECEIVE_BOOT_COMPLETED |必要。 若要在裝置重新啟動後顯示通知中心 |
| WAKE_LOCK |建議使用。 啟用「在使用 WiFi 或螢幕關閉時收集統計資料」 |
| VIBRATE |選用。 在收到通知後啟用震動 |
| DOWNLOAD_WITHOUT_NOTIFICATION |選用。 啟用「Android 重點通知」 |
| WRITE_EXTERNAL_STORAGE |選用。 啟用「Android 重點通知」 |
| ACCESS_COARSE_LOCATION |選用。 啟用「即時位置報告」 |
| ACCESS_FINE_LOCATION |選用。 啟用「GPS 式即時位置報告」 |

從 Android M 開始，[某些權限是在執行階段管理](mobile-engagement-android-location-reporting.md#android-m-permissions)。

如果您已使用 ``ACCESS_FINE_LOCATION``，則不需要同時使用 ``ACCESS_COARSE_LOCATION``。

## <a name="android-manifest-configuration-options"></a>Android 資訊清單組態選項
### <a name="crash-report"></a>當機報告
若要停用當機報告，請在 `<application>` 和 `</application>` 標記之間加入此程式碼：

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>高載閾值
根據預設，Engagement 會即時報告記錄檔。 如果應用程式報告記錄檔經常有變化，建議您緩衝記錄檔，以固定時段一次報告全部的記錄檔 (稱為「高載模式」)。 若要這樣做，請在 `<application>` 和 `</application>` 標籤之間加入此程式碼：

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

高載模式可以稍微延長電池使用時間但對 Engagement 監視器會有影響：所有工作階段和工作持續時間會調整為高載閾值 (因此，可能將看不到時間比高載閾值短的工作階段和作業)。 高載閾值不能超過 30000 (30 秒)。

### <a name="session-timeout"></a>工作階段逾時
 您可以按下 **Home** 或 **Back** 鍵結束活動，或跳到另一個應用程式設定行動電話閒置。 根據預設，工作階段會在最後一個活動結束之後 10 秒鐘結束。 這是為了避免當使用者退出但很快又返回應用程式 (例如使用者挑選影像、檢查通知等等) 時，工作階段產生分割的狀況。您可以修改這個參數。 若要這樣做，請在 `<application>` 和 `</application>` 標籤之間加入此程式碼：

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>停用記錄報告
### <a name="using-a-method-call"></a>使用方法呼叫
如果您想要 Engagement 停止傳送記錄檔，可以呼叫：

    EngagementAgent.getInstance(context).setEnabled(false);

這個呼叫是持續性的：它使用共用的喜好設定檔案。

如果當您呼叫此函式時 Engagement 已啟用，停止服務可能需要 1 分鐘的時間。 不過，當您下次啟動應用程式時，完全不會啟動服務。

您可以使用 `true`呼叫相同函式，即可再次啟用記錄報告。

### <a name="integration-in-your-own-preferenceactivity"></a>在您自己的 `PreferenceActivity`
如不呼叫此函數，您也可以在現有的 `PreferenceActivity` 中直接整合此設定。

您可以在 `AndroidManifest.xml` 檔案中使用 `application meta-data`，設定 Engagement 使用您的喜好設定檔案 (搭配所需的模式)：

* `engagement:agent:settings:name` 機碼可用來定義共用喜好設定檔案的名稱。
* `engagement:agent:settings:mode` 機碼可用來定義共用喜好設定檔案的模式。 使用與您的 `PreferenceActivity`相同的模式。 模式必須以數字傳遞：如果您在程式碼中使用常數旗標的組合，請檢查總值。

Engagement 在喜好設定檔案內會一律使用 `engagement:key` 布林值機碼來管理這項設定。

下列 `AndroidManifest.xml` 範例顯示的是預設值：

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

接著您可以將 `CheckBoxPreference` 加入您的喜好設定配置，如下所示：

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />
