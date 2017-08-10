---
title: "使用 Xamarin.Forms 搭配行動應用程式入門"
description: "遵循此教學課程，開始使用 Azure 行動應用程式進行 Xamarin.Forms 開發。"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: cb959867ccfc85993694bd810f08e2f8150b44f3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/05/2017

---
# <a name="create-a-xamarinforms-app"></a>建立 Xamarin.Forms 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概觀
本教學課程說明如何使用 Azure 行動應用程式後端，將雲端型後端服務新增到 Xamarin.Forms 行動應用程式。 您將同時建立新的行動應用程式後端，以及可在 Azure 中儲存應用程式資料的簡易「待辦事項清單」 Xamarin.Forms 應用程式。

完成本教學課程是所有其他 Xamarin.Forms 應用程式的行動應用程式教學課程的必要條件。

## <a name="prerequisites"></a>必要條件
若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Visual Studio 和 Xamarin。 如需相關指示，請參閱 [設定和安裝 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 。
* 已安裝 Xcode v7.0 或更新版本以及 Xamarin Studio Community 的 Mac。 請參閱[設定和安裝 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)及[針對 Mac 使用者的設定、安裝和驗證](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN)。

## <a name="create-a-new-azure-mobile-app-backend"></a>建立新的 Azure 行動應用程式後端
依照下列步驟建立新的行動應用程式後端。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。 接下來，您將下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

## <a name="configure-the-server-project"></a>設定伺服器專案
請遵循下列步驟來設定伺服器專案使用 Node.js 或 .NET 後端。

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>下載並執行 Xamarin.Forms 方案
您在這裡有幾個選擇。 您可以將方案下載到 Mac 並在 Xamarin Studio 中開啟，或者您也可以下載方案到 Windows 電腦並使用已加入網路的 Mac 在 Visual Studio 開啟，以便建置 iOS 應用程式。 如果您需要更詳細的 Xamarin 設定案例指示，請參閱 [設定和安裝 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 。

讓我們繼續進行︰

1. 在您的 Mac 或 Windows 電腦上，於瀏覽器視窗中開啟 [Azure 入口網站] 。
2. 在行動應用程式的設定刀鋒視窗上，按一下 開始使用 \(在 [行動] 之下) > [Xamarin.Forms]。 在步驟 3 中，按一下 建立新的應用程式 \(如果尚未選取的話)。  接著按一下 [下載]  按鈕。

   這會下載包含連線到您行動應用程式之用戶端應用程式的專案。 將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。
3. 將您下載的專案解壓縮，並在 Xamarin Studio 或 Visual Studio 中開啟。

   ![][9]

   ![][8]

## <a name="optional-run-the-ios-project"></a>(選擇性) 執行 iOS 專案
這一節適用於對 iOS 裝置執行 Xamarin iOS 專案。 如果未使用 iOS 裝置，可以略過這一節。

#### <a name="in-xamarin-studio"></a>在 Xamarin Studio 中
1. 以滑鼠右鍵按一下 iOS 專案，然後按一下 [設為起始專案] 。
2. 在 [執行] 功能表上，按一下 [開始偵錯] 以建置專案並在 iPhone 模擬器中啟動應用程式。

#### <a name="in-visual-studio"></a>在 Visual Studio 中
1. 以滑鼠右鍵按一下 iOS 專案，然後按一下 [設為起始專案] 。
2. 從 [建置] 功能表中，按一下 [組態管理員]。
3. 在 [組態管理員] 對話方塊中，選取 iOS 專案的 [建置] 和 [部署] 核取方塊。
4. 按 **F5** 鍵，以建置專案並在 iPhone 模擬器中啟動 app。

   > [!NOTE]
   > 如果在建置時發生問題，請執行 NuGet 封裝管理員並更新為最新版的 Xamarin 支援封裝。 有時候快速入門專案可能未能及時更新為最新版本。    
   >
   >

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後按一下 **+** 按鈕。

![][10]

如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。 要求中的資料會插入 TodoItem 資料表中。 行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [!NOTE]
> 您將會看到在您方案的可攜式類別庫專案的 TodoItemManager.cs C# 檔案中，存取行動應用程式後端的程式碼。
>
>

## <a name="optional-run-the-android-project"></a>(選擇性) 執行 Android 專案
這一節適用於對 Android 裝置執行 Xamarin Adroid 專案。 如果未使用 Android 裝置，可以略過這一節。

#### <a name="in-xamarin-studio"></a>在 Xamarin Studio 中
1. 以滑鼠右鍵按一下 Android 專案，然後按一下 [設為起始專案] 。
2. 在 [執行] 功能表上按一下 [開始偵錯] 以建置專案，並在 Android 模擬器中啟動應用程式。

#### <a name="in-visual-studio"></a>在 Visual Studio 中
1. 以滑鼠右鍵按一下 Android (Droid) 專案，然後按一下 [設為起始專案] 。
2. 從 [建置] 功能表中，按一下 [組態管理員]。
3. 在 [組態管理員] 對話方塊中，選取 Android 專案的 [建置] 和 [部署] 核取方塊。
4. 按 **F5** 鍵，以建置專案並在 Android 模擬器中啟動應用程式。

   > [!NOTE]
   > 如果在建置時發生問題，請執行 NuGet 封裝管理員並更新為最新版的 Xamarin 支援封裝。 有時候快速入門專案可能未能及時更新為最新版本。    
   >
   >

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後按一下 **+** 按鈕。

![][11]

如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。 要求中的資料會插入 TodoItem 資料表中。 行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [!NOTE]
> 您將會看到在您方案的可攜式類別庫專案的 TodoItemManager.cs C# 檔案中，存取行動應用程式後端的程式碼。
>
>

## <a name="optional-run-the-windows-project"></a>(選擇性) 執行 Windows 專案
這一節適用於對 Windows 裝置執行 WinApp 專案。 如果未使用 Windows 裝置，可以略過這一節。

#### <a name="in-visual-studio"></a>在 Visual Studio 中
1. 以滑鼠右鍵按一下任何一個 Windows 專案，然後按一下 [設為起始專案] 。
2. 從 [建置] 功能表中，按一下 [組態管理員]。
3. 在 [組態管理員] 對話方塊中，選取所選之 Windows 專案的 [建置] 和 [部署] 核取方塊。
4. 按 **F5** 鍵，以建置專案並在 Windows 模擬器中啟動應用程式。

   > [!NOTE]
   > 如果在建置時發生問題，請執行 NuGet 封裝管理員並更新為最新版的 Xamarin 支援封裝。 有時候快速入門專案可能未能及時更新為最新版本。    
   >
   >

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後按一下 **+** 按鈕。

如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。 要求中的資料會插入 TodoItem 資料表中。 行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

![][12]

> [!NOTE]
> 您將會看到在您方案的可攜式類別庫專案的 TodoItemManager.cs C# 檔案中，存取行動應用程式後端的程式碼。
>
>

## <a name="next-steps"></a>後續步驟
* [將驗證新增至應用程式中](app-service-mobile-xamarin-forms-get-started-users.md)  
  了解如何利用識別提供者來驗證應用程式的使用者。
* [將推播通知新增至應用程式中](app-service-mobile-xamarin-forms-get-started-push.md)  
  了解如何將推播通知支援新增至應用程式，並設定行動應用程式後端以使用 Azure 通知中樞傳送推播通知。
* [啟用應用程式的離線同步處理](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  了解如何使用行動應用程式後端，將離線支援新增至應用程式。 離線同步處理可讓使用者與行動應用程式進行互動&mdash;檢視、新增或修改資料&mdash;即使沒有網路連線進也可行。
* [如何針對 Azure Mobile Apps 使用受管理的用戶端](app-service-mobile-dotnet-how-to-use-client-library.md)  
  了解如何在 Xamarin 應用程式中使用受管理的用戶端 SDK。

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 入口網站]: https://portal.azure.com/

