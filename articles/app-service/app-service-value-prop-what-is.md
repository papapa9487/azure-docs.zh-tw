---
title: "適用於 Web、行動和 API 應用程式的 Azure App Service | Microsoft Docs"
description: "了解 Azure App Service 如何協助您開發、部署和管理 Web 和行動應用程式。"
keywords: "App Service, Azure App Service, App Service 成本, 級別, 可調整, 應用程式部署, Azure 應用程式部署, paas, 平台即服務, 網站, web, azure 行動"
services: app-service
documentationcenter: 
author: omarkmsft
manager: erikre
editor: cephalin
ms.assetid: 979cafa8-eeb6-4d3b-87cf-764a821c3e4f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/02/2016
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 94869072a38be61819213fbf4f919764fe1849c0
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="what-is-azure-app-service"></a>什麼是 Azure App Service？
 是 Microsoft Azure 的 [平台即服務](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) 供應項目。 建立適用任何平台或裝置的 Web 與行動應用程式。 整合您的應用程式與 SaaS 解決方案、與內部部署應用程式連接，並使您的商務程序自動化。 Azure 會使用您選擇的共用 VM 資源或專用 VM，在完全受管理的虛擬機器 (VM) 上執行您的應用程式。

App Service 包含先前以 Azure 網站和 Azure 行動服務的形式來獨立提供的 Web 和行動功能。 此外，它也包含可用來自動執行商務程序及裝載雲端 API 的新功能。 App Service 是單一的整合式服務，可讓您將各種元件 (網站、行動應用程式後端、RESTful API 和商務程序) 撰寫成單一解決方案。

下列 4 分鐘影片提供 App Service 與舊版 Azure 產品之間的關聯與新功能的簡短說明。

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/app-service-history-lesson/player]
> 
> 

## <a name="why-use-app-service"></a>為何要使用 App Service？
以下是 App Service 的一些重要功能和能力︰

* **多種語言和架構** - App Service有 ASP.NET、Node.js、Java、PHP 和 Python 的頂級支援。 您也可以在 App Service VM 上執行 [Windows PowerShell 和其他指令碼或可執行檔](../app-service-web/web-sites-create-web-jobs.md) 。
* **DevOps 最佳化** - 使用 Visual Studio Team Services、GitHub 或 BitBucket 設定 [持續整合和部署](../app-service-web/app-service-continuous-deployment.md) 。 透過 [測試和預備環境](../app-service-web/web-sites-staged-publishing.md)升級更新。 使用 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 或[跨平台命令列介面 (CLI)](../cli-install-nodejs.md)，在 App Service 中管理您的應用程式。
* **具高可用性的全域調整** - 以手動或自動方式相應[增加](../app-service-web/web-sites-scale.md)或[放大](../monitoring-and-diagnostics/insights-how-to-scale.md)。 在 Microsoft 的通用資料中心基礎結構中隨處裝載您的應用程式，而 App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 會承諾高可用性。
* **SaaS 平台和內部部署資料的連線** - 有超過 50 種適用於企業系統 (例如 SAP、Siebel 和 Oracle)、SaaS 服務 (例如 Salesforce 和 Office 365)，以及網際網路服務 (例如 Facebook 和 Twitter) 的 [連接器](../connectors/apis-list.md) 可供選擇。 使用[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)和 [Azure 虛擬網路](../app-service-web/web-sites-integrate-with-vnet.md)存取內部部署資料。
* **安全性和法規遵循** - App Service 為 [ISO、SOC 和 PCI 相容](https://www.microsoft.com/TrustCenter/)。
* **應用程式範本** - 從 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 中的廣泛範本清單中進行選擇，以便使用精靈來安裝受歡迎的開放原始碼軟體，例如 WordPress、Joomla 和 Drupal。
* **Visual Studio 整合** - Visual Studio 中的專用工具可簡化建立、部署和偵錯的工作。

## <a name="app-types-in-app-service"></a>App Service 中的應用程式類型
App Service 提供數個「應用程式類型」 ，而每個類型主要裝載特定工作負載︰

* [Web Apps](../app-service-web/app-service-web-overview.md) - 用於裝載網站和 Web 應用程式。
* [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) - 用於裝載行動應用程式後端。
* [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) - 用於裝載 RESTful API。
* [**Logic Apps**](../logic-apps/logic-apps-what-are-logic-apps.md) - 用於自動化商務程序和跨雲端整合系統和資料，而不需要撰寫程式碼。

「應用程式」  一詞在這裡是指專門用來執行工作負載的主機資源。 以「Web 應用程式」為例，您可能已習慣將 Web 應用程式視為可一起提供瀏覽器功能的計算資源和應用程式程式碼。 但是在 App Service 中，「Web 應用程式」  是 Azure 提供用來裝載應用程式程式碼的計算資源。 

您的應用程式可能是由多個各種不同的 App Service 應用程式所組成。 例如，如果您的應用程式包含 Web 前端和RESTful API 後端，您可以︰

- 將兩者 (前端和 API) 部署至單一 Web 應用程式  
- 將前端程式碼部署至 Web 應用程式，而將後端程式碼部暑至 API 應用程式。 



## <a name="app-service-plans"></a>App Service 方案
[App Service 方案](azure-web-sites-web-hosting-plans-in-depth-overview.md)代表用來裝載應用程式的實體資源集合。

App Service 方案可定義：

- **區域** (美國西部、美國東部等)
- **級別計數** (一、二、三個執行個體等)
- **執行個體大小** (小型、中型、大型)
- **SKU** (免費、共用、基本、標準、進階)

所有指派給 **App Service 方案**的應用程式都會共用它所定義的資源，而讓您節省裝載多個應用程式時的成本。

**App Service 方案**可以從 [免費] 和 [共用] SKU 調整為 [基本]、[標準] 和 [進階] SKU，以便存取更多資源和功能。 App Service 方案設為 [基本] 或更高的 SKU 後，您也可以控制 VM 的**大小**和級別計數。

App Service 方案的 **SKU** 和**級別**可決定成本，而不是其中裝載的應用程式數目。 

如果您需要更多的延展性和網路隔離，則可在 [App Service 環境](../app-service-web/app-service-app-service-environment-intro.md)中執行應用程式。

## <a name="pricing"></a>定價
如需 App Service 費用的詳細資訊，請參閱 [App Service 價格](https://azure.microsoft.com/pricing/details/app-service/)。

## <a name="test-drive-app-service"></a>試驗 App Service
[建立範例 Web 應用程式、行動應用程式或邏輯應用程式](https://azure.microsoft.com/try/app-service/)，並試用一小時，無需信用卡，也無需簽定合約，一切都非常簡單。

或申請 [免費 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)，並試試我們的其中一個入門教學課程︰

* [教學課程︰建立 Web 應用程式](../app-service-web/app-service-web-get-started.md)
* [教學課程︰建立行動應用程式](../app-service-mobile/app-service-mobile-android-get-started.md)
* [教學課程︰建立 API 應用程式](../app-service-api/app-service-api-dotnet-get-started.md)
* [教學課程︰建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)


