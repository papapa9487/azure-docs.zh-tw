---
title: "什麼是行動應用程式"
description: "了解 App Service 為您的企業行動應用程式帶來哪些優勢。"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: dd405eefbd69e1ed2268152017bc1f9992619c5f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/05/2017

---
# <a name="getting-started"></a>什麼是 Mobile Apps？
Azure App Service 是完全受管理的 [平台即服務](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) 產品，適用於專業開發人員，具有一組豐富的功能可用於 Web、行動和整合案例。 *Azure App Service* 中的 Mobile Apps 具有高擴充性且可供全球使用，是專為企業開發人員與系統整合人員設計的行動應用程式開發平台，能提供一組豐富的功能給行動應用程式開發人員。

![Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>為何選擇 Mobile Apps？
*Azure App Service* 中的 Mobile Apps 具有高擴充性且可供全球使用，是專為企業開發人員與系統整合人員設計的行動應用程式開發平台，能提供一組豐富的功能給行動應用程式開發人員。 您可以利用 Mobile Apps：

* **建置原生和跨平台應用程式** - 不論您要建置原生 iOS、Android 和 Windows 應用程式或跨平台 Xamarin 或 Cordova (Phonegap) 應用程式，都可以利用採用原生 SDK 的 App Service。
* **連線到您的企業系統** - 透過 Mobile Apps，您可以在短短幾分鐘內新增公司標誌，並連線至企業內部部署或雲端資源。
* **建置具有資料同步功能的可離線應用程式** - 建置可離線運作的應用程式，並在出現與任何企業資料來源或 SaaS API 的連線時，使用 Mobile Apps 在背景同步處理資料，讓您的行動工作力更具生產力。
* **在數秒內推播通知給數百萬人** - 在任何裝置上利用即時推播通知與客戶聯繫，依照其需求將推播通知個人化並且適時傳送。

## <a name="mobile-app-features"></a>行動應用程式功能
下列功能對於具備雲端功能的行動開發十分重要︰

* **驗證和授權** - 從不斷成長的識別提供者 (包括適用於企業驗證的 Azure Active Directory，以及 Facebook、Google、Twitter 和 Microsoft 帳戶等社交提供者) 清單中選取。  Azure Mobile Apps 可為每個提供者提供 OAuth 2.0 服務。  您也可以為識別提供者整合 SDK，以取得提供者特定功能。

  深入探索我們的 [驗證功能]。
* **資料存取** - Azure Mobile Apps 提供了已連結至 SQL Azure 或內部部署 SQL Server 且適合行動用途的 OData v3 資料來源。  這項服務以 Entity Framework 為基礎，可讓您輕鬆地與其他 NoSQL 和 SQL 資料提供者整合，包括 [Azure 資料表儲存體]、MongoDB、[DocumentDB] 和 SaaS API 提供者 (如 Office 365 和 Salesforce.com)。
* **離線同步處理** - 我們的用戶端 SDK 可讓您輕鬆地建置強大和回應靈敏的行動應用程式，而這類應用程式利用可與後端資料自動同步處理的離線資料集運作 (包括衝突解決支援)。

  深入探索我們的 [資料功能]。
* **推播通知** - 我們的用戶端 SDK 與 Azure 通知中樞的註冊功能緊密整合，可讓您將推播通知同時傳送給數百萬個使用者。

  深入探索我們的 [推播通知功能]。
* **用戶端 SDK** - 我們提供一組完整的用戶端 SDK，涵蓋原生開發 ([iOS]、[Android] 和 [Windows])、跨平台開發 ([適用於 iOS 和 Android 的 Xamarin]、[Xamarin Forms]) 和混合式應用程式開發 ([Apache Cordova])。  每個用戶端 SDK 都是透過 MIT 授權提供並為開放原始碼。

## <a name="azure-app-service-features"></a>Azure App Service 功能。
下列平台功能適合用於行動生產網站。

* **自動調整** - App Service 可讓您快速地相應增加或相應放大，以處理任何傳入的客戶負載。 手動選取 VM 的數目和大小，或設定自動調整以根據負載或排程調整行動應用程式後端。

  深入探索 [自動調整]。
* **預備環境** - App Service 可以執行多個版本的網站，讓您能夠執行 A / B 測試、在較大型 DevOps 方案中進行生產環境測試，以及就地預備新的後端。

  深入探索 [預備環境]。
* **連續部署** - App Service 可與常見 SCM 系統整合，可讓您將新版的後端發送至 SCM 系統的分支，以便自動部署。

  深入探索 [部署選項]。
* **虛擬網路** - App Service 可以使用虛擬網路、ExpressRoute 或混合式連線，連接到內部部署資源。

  深入探索[混合式連線]、[虛擬網路]和 [ExpressRoute]。
* **隔離 / 專用的環境** - App Service 可以在一個完全隔離且專用的環境中執行，以高規格的方式安全地執行 Azure App Service 應用程式。  這很適合於需要高規格、隔離或安全網路存取的應用程式工作負載。

  深入探索 [App Service 環境]。

## <a name="getting-started"></a>開始使用
若要開始使用行動應用程式，請遵循 [快速入門] 教學課程。  這將涵蓋產生您所選的行動後端和用戶端，然後整合驗證、離線同步處理和推播通知的基本概念。  您可以依照 [快速入門] 教學課程執行數次 - 每個用戶端應用程式執行一次。

如需 Azure Mobile Apps 的詳細資訊，請檢閱我們的 [學習地圖]。
如需 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service]。

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[快速入門]: app-service-mobile-ios-get-started.md
[Azure 資料表儲存體]: ../storage/storage-dotnet-how-to-use-tables.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[驗證功能]: ./app-service-mobile-auth.md
[資料功能]: ./app-service-mobile-offline-data-sync.md
[推播通知功能]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[適用於 iOS 和 Android 的 Xamarin]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[自動調整]: ../app-service-web/web-sites-scale.md
[預備環境]: ../app-service-web/web-sites-staged-publishing.md
[部署選項]: ../app-service-web/web-sites-deploy.md
[混合式連線]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[虛擬網路]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[App Service 環境]: ../app-service-web/app-service-app-service-environment-intro.md
[學習地圖]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/

