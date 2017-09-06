---
title: "利用 Xamarin.Forms 開始使用 Mobile Apps"
description: "遵循此教學課程，開始使用 Mobile Apps 進行 Xamarin.Forms 開發"
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
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: ee12caaad4095cff6dae3282f747ae804f93db81
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="create-a-xamarinforms-app"></a>建立 Xamarin.Forms 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概觀
本教學課程說明如何使用 Azure App Service 的 Mobile Apps 功能作為後端，將雲端型後端服務新增到 Xamarin.Forms 行動裝置應用程式。 您會同時建立新的 Mobile Apps 後端，以及可在 Azure 中儲存應用程式資料的簡易待辦事項清單 Xamarin.Forms 應用程式。

完成本教學課程是所有其他 Xamarin.Forms 應用程式的行動應用程式教學課程的必要條件。

## <a name="prerequisites"></a>必要條件
若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* Visual Studio 和 Xamarin。 如需相關資訊，請參閱[設定及安裝 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 頁面。

* 已安裝 Xcode v7.0 或更新版本以及 Xamarin Studio Community 的 Mac。 如需相關資訊，請參閱[設定及安裝 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 及[針對 Mac 使用者設定、安裝和驗證](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN)。

## <a name="create-a-new-mobile-apps-back-end"></a>建立新的 Mobile Apps 後端

若要建立新的 Mobile Apps 後端，請執行下列作業：

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

您現在已設定您的行動用戶端應用程式可以使用的 Mobile Apps 後端。 接下來，您可下載簡易待辦事項清單後端的伺服器專案，然後將它發佈至 Azure。

## <a name="configure-the-server-project"></a>設定伺服器專案

若要將伺服器專案設定為使用 Node.js 或 .NET 後端，請執行下列作業：

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>下載並執行 Xamarin.Forms 方案

您可以用任一種方式下載方案。 將方案下載到 Mac 並在 Xamarin Studio 中開啟，或者將它下載到 Windows 電腦並使用已加入網路的 Mac 在 Visual Studio 開啟，以便建置 iOS 應用程式。 如需詳細資訊，請參閱[設定及安裝 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)。

在 Mac 或 Windows 電腦上，執行下列作業：

1. 移至 [Azure 入口網站]。

2. 在行動裝置應用程式的 [設定] 刀鋒視窗上，於 [行動] 之下，選取 [開始使用] > [Xamarin.Forms]。 在**步驟 3** 之下，選取 [建立新的應用程式]，然後選取 [下載]。

   此動作會下載包含連線到您行動裝置應用程式之用戶端應用程式的專案。 將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

3. 將您下載的專案解壓縮，然後在 Xamarin Studio (Mac) 或 Visual Studio (Windows) 中開啟。

   ![在 Xamarin Studio 中解壓縮的專案][9]

   ![在 Visual Studio 中解壓縮的專案][8]

## <a name="optional-run-the-ios-project"></a>(選擇性) 執行 iOS 專案
這一節中，您會執行適用於 iOS 裝置的 Xamarin iOS 專案。 如果未使用 iOS 裝置，可以略過這一節。

#### <a name="in-xamarin-studio"></a>在 Xamarin Studio 中
1. 以滑鼠右鍵按一下 iOS 專案，然後選取 [設為起始專案]。

2. 在 [執行] 功能表上，選取 [開始偵錯] 以建置專案並在 iPhone 模擬器中啟動應用程式。

#### <a name="in-visual-studio"></a>在 Visual Studio 中
1. 以滑鼠右鍵按一下 iOS 專案，然後選取 [設為起始專案]。

2. 在 [建置] 功能表上，選取 [組態管理員]。

3. 在 [組態管理員] 對話方塊中，選取 iOS 專案旁邊的 [建置] 和 [部署] 核取方塊。

4. 若要建置專案並在 iPhone 模擬器中啟動應用程式，請選取 **F5** 鍵。

   > [!NOTE]
   > 如果在建置專案時發生問題，請執行 NuGet 套件管理員並更新為最新版的 Xamarin 支援套件。 快速入門專案可能較慢更新為最新版本。    
   >
   >

5. 在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後選取加號 (**+**)。

    ![][10]

    此動作會傳送 post 要求到 Azure 中代管的新 Mobile Apps 後端。 要求中的資料會插入 TodoItem 資料表中。 Mobile Apps 後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

    > [!NOTE]
    > 您將會在您方案的可攜式類別庫專案的 TodoItemManager.cs C# 檔案中，發現可存取 Mobile Apps 後端的程式碼。
    >
    >

## <a name="optional-run-the-android-project"></a>(選擇性) 執行 Android 專案
這一節中，您會執行適用於 Android 的 Xamarin Android 專案。 如果未使用 Android 裝置，可以略過這一節。

#### <a name="in-xamarin-studio"></a>在 Xamarin Studio 中

1. 以滑鼠右鍵按一下 Android 專案，然後選取 [設為起始專案] 。

2. 若要建置專案並在 Android 模擬器中啟動應用程式，請在 [執行] 功能表上選取 [開始偵錯]。

#### <a name="in-visual-studio"></a>在 Visual Studio 中

1. 以滑鼠右鍵按一下 Android (Droid) 專案，然後選取 [設為起始專案]。

2. 在 [建置] 功能表上，選取 [組態管理員]。

3. 在 [組態管理員] 對話方塊中，選取 Android 專案旁邊的 [建置] 和 [部署] 核取方塊。

4. 若要建置專案並在 Android 模擬器中啟動應用程式，請選取 **F5** 鍵。

   > [!NOTE]
   > 如果在建置專案時發生問題，請執行 NuGet 套件管理員並更新為最新版的 Xamarin 支援套件。 快速入門專案可能較慢更新為最新版本。    
   >
   >

5. 在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後選取加號 (**+**)。

    ![][11]
    
    此動作會傳送 post 要求到 Azure 中代管的新 Mobile Apps 後端。 要求中的資料會插入 TodoItem 資料表中。 Mobile Apps 後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。
    
    > [!NOTE]
    > 您將會在您方案的可攜式類別庫專案的 TodoItemManager.cs C# 檔案中，發現可存取 Mobile Apps 後端的程式碼。
    >
    >

## <a name="optional-run-the-windows-project"></a>(選擇性) 執行 Windows 專案

這一節中，您會執行適用於 Window 裝置的 Xamarin WinApp 專案。 如果未使用 Windows 裝置，可以略過這一節。

#### <a name="in-visual-studio"></a>在 Visual Studio 中

1. 以滑鼠右鍵按一下任何一個 Windows 專案，然後選取 [設為起始專案]。

2. 在 [建置] 功能表上，選取 [組態管理員]。

3. 在 [組態管理員] 對話方塊中，選取所選之 Windows 專案旁邊的 [建置] 和 [部署] 核取方塊。

4. 若要建置專案並在 Windows 模擬器中啟動應用程式，請選取 **F5** 鍵。

   > [!NOTE]
   > 如果在建置專案時發生問題，請執行 NuGet 套件管理員並更新為最新版的 Xamarin 支援套件。 快速入門專案可能較慢更新為最新版本。    
   >
   >

5. 在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後選取加號 (**+**)。

    此動作會傳送 post 要求到 Azure 中代管的新 Mobile Apps 後端。 要求中的資料會插入 TodoItem 資料表中。 Mobile Apps 後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。
    
    ![][12]
    
    > [!NOTE]
    > 您將會在您方案的可攜式類別庫專案的 TodoItemManager.cs C# 檔案中，發現可存取 Mobile Apps 後端的程式碼。
    >
    >

## <a name="next-steps"></a>後續步驟

* [將驗證新增至應用程式中](app-service-mobile-xamarin-forms-get-started-users.md)  
  了解如何利用識別提供者來驗證應用程式的使用者。

* [將推播通知新增至應用程式](app-service-mobile-xamarin-forms-get-started-push.md)  
  了解如何將推播通知支援新增至應用程式，並設定 Mobile Apps 後端以使用 Azure 通知中樞傳送推播通知。

* [啟用應用程式的離線同步處理](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  了解如何使用 Mobile Apps 後端，為應用程式新增離線支援。 使用離線同步，即便在沒有網路連線的情況下，您也能檢視、新增或修改行動裝置應用程式的資料。

* [對 Mobile Apps 使用受管理的用戶端](app-service-mobile-dotnet-how-to-use-client-library.md)  
  了解如何在 Xamarin 應用程式中使用受管理的用戶端 SDK。

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


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

