---
title: "Windows 通用進階報告與 MobileApps Engagement"
description: "如何將 Azure Mobile Engagement 與 Windows 通用 app 整合"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>使用 Windows 通用 App Engagement SDK 的進階報告
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

本主題說明 Windows 通用應用程式中的其他報告案例。 這些案例包含的選項可供您選擇套用至 [快速入門](mobile-engagement-windows-store-dotnet-get-started.md) 教學課程中建立的應用程式。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

開始本教學課程之前，您必須先完成 [快速入門](mobile-engagement-windows-store-dotnet-get-started.md) 教學課程，此教學課程相當直接明瞭。 本教學課程涵蓋您可以選擇的其他選項。

## <a name="specifying-engagement-configuration-at-runtime"></a>指定執行階段的 Engagement 組態
Engagement 組態集中在您專案的 `Resources\EngagementConfiguration.xml` 檔案，這是在 [快速入門](mobile-engagement-windows-store-dotnet-get-started.md) 主題中指定的位置。

但是您也可以在執行階段指定它：您可以在 Engagement 代理程式初始化之前呼叫下列方法：

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>建議使用的方法：多載您的 `Page` 類別
若要啟用 Engagement 計算使用者、工作階段、活動、當機和技術的統計資料所需的所有記錄檔報告，請讓所有的 `Page` 子類別繼承自 `EngagementPage` 類別。

以下是您應用程式其中一個頁面的範例。 您可以將相同的方法用於您應用程式的所有頁面。

### <a name="c-source-file"></a>C# 來源檔案
修改您頁面的 `.xaml.cs` 檔案：

* 新增至您的 `using` 陳述式：
  
      using Microsoft.Azure.Engagement;
* 以 `EngagementPage` 取代 `Page`：

**沒有 Engagement：**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**有 Engagement：**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> 如果您的頁面會覆寫 `OnNavigatedTo` 方法，請務必呼叫 `base.OnNavigatedTo(e)`。 否則不會報告活動 (`EngagementPage` 會在其 `OnNavigatedTo` 方法內呼叫 `StartActivity`)。
> 
> 

### <a name="xaml-file"></a>XAML 檔案
修改您頁面的 `.xaml` 檔案：

* 新增命名空間宣告：
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* 以 `engagement:EngagementPage` 取代 `Page`：

**沒有 Engagement：**

        <Page>
            <!-- layout -->
            ...
        </Page>

**有 Engagement：**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>覆寫預設行為
根據預設，頁面的類別名稱會在報告時做為活動名稱 (沒有額外的名稱)。 如果類別使用 "Page" 尾碼，Engagement 會移除它。

若要覆寫名稱的預設行為，請加入下列程式碼︰

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

若要報告活動的額外資訊，請加入下列程式碼︰

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

系統會從您頁面的 `OnNavigatedTo` 方法中呼叫這些方法。

### <a name="alternate-method-call-startactivity-manually"></a>替代方法：手動呼叫 `StartActivity()`
如果您無法或不想要多載您的 `Page` 類別，您可以改為透過直接呼叫 `EngagementAgent` 方法來啟動活動。

我們建議您於 Page 的 `OnNavigatedTo` 方法內呼叫 `StartActivity`。

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> 請確定您正確地結束工作階段。
> 
> 應用程式關閉時，Windows 通用 SDK 會自動呼叫 `EndActivity` 方法。 因此，「強烈」建議每當使用者的活動變更時便叫呼叫 `StartActivity` 方法，並且「絕對不要」呼叫 `EndActivity` 方法。 這個方法會通知 Engagement 伺服器目前的使用者已離開應用程式，這會影響所有應用程式記錄檔。
> 
> 

## <a name="advanced-reporting"></a>進階報告
(選擇性) 您可以報告應用程式的特定事件、錯誤和工作；若要這樣做，請使用 `EngagementAgent` 類別中找到的其他方法。 Engagement API 允許使用所有 Engagement 的進階功能。

如需詳細資訊，請參閱 [如何在 Windows 通用 app 中使用進階的 Mobile Engagement 標記 API](mobile-engagement-windows-store-use-engagement-api.md)。

