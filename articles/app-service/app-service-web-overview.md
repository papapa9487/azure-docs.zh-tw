---
title: "Web Apps 概觀 | Microsoft Docs"
description: "了解 Azure App Service 如何協助您開發和裝載 Web 應用程式"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: c1b3feb79328269f4692d744e7a473397d0328c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="web-apps-overview"></a>Web 應用程式概觀
 是受到完整管理的計算平台，非常適合用來裝載網站和 Web 應用程式。 Microsoft Azure 的這個 [平台即服務](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) 產品可讓您專注於商務邏輯，而讓 Azure 負責處理用來執行及調整應用程式的基礎結構。

下列的 5 分鐘影片將介紹 Azure App Service Web Apps。

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="what-is-a-web-app-in-app-service"></a>什麼是 App Service 中的 Web 應用程式？
在 App Service 中，「Web 應用程式」  是 Azure 提供用來裝載網站或 Web 應用程式的計算資源。  

計算資源可能位於共用或專用的虛擬機器 (VM)，一切視您選擇的定價層而定。 應用程式的程式碼會在與其他客戶隔離開來的受管理 VM 中執行。

程式碼可以使用 Azure App Service 所支援的任何語言或架構，例如 ASP.NET、Node.js、Java、PHP 或 Python。 您也可以在 Web 應用程式中，執行使用 [PowerShell 和其他指令碼語言](web-sites-create-web-jobs.md#acceptablefiles) 的指令碼。

如需可使用 Web Apps 的典型應用程式案例的範例，請參閱 [Web 應用程式案例](https://azure.microsoft.com/documentation/scenarios/web-app/)和 [Azure App Service、雲端服務、虛擬機器及 Service Fabric 的比較](choose-web-site-cloud-service-vm.md#scenarios)的**案例和建議**一節。

## <a name="why-use-web-apps"></a>為何要使用 Web Apps？
以下是 App Service 適用於 Web Apps 的一些主要功能︰

* **多種語言和架構** - App Service有 ASP.NET、Node.js、Java、PHP 和 Python 的頂級支援。 您也可以在 App Service VM 上執行 [PowerShell 和其他指令碼或可執行檔](web-sites-create-web-jobs.md) 。
* **DevOps 最佳化** - 使用 Visual Studio Team Services、GitHub 或 BitBucket 設定 [持續整合和部署](app-service-continuous-deployment.md) 。 透過 [測試和預備環境](web-sites-staged-publishing.md)升級更新。 使用 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 或[跨平台命令列介面 (CLI)](../cli-install-nodejs.md)，在 App Service 中管理您的應用程式。
* **具高可用性的全域調整** - 以手動或自動方式相應[增加](web-sites-scale.md)或[放大](../monitoring-and-diagnostics/insights-how-to-scale.md)。 在 Microsoft 的通用資料中心基礎結構中隨處裝載您的應用程式，而 App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 會承諾高可用性。
* **SaaS 平台和內部部署資料的連線** - 有超過 50 種適用於企業系統 (例如 SAP、Siebel 和 Oracle)、SaaS 服務 (例如 Salesforce 和 Office 365)，以及網際網路服務 (例如 Facebook 和 Twitter) 的 [連接器](../connectors/apis-list.md) 可供選擇。 使用[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)和 [Azure 虛擬網路](web-sites-integrate-with-vnet.md)存取內部部署資料。
* **安全性和法規遵循** - App Service 為 [ISO、SOC 和 PCI 相容](https://www.microsoft.com/TrustCenter/)。
* **應用程式範本** - 從 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 中的廣泛應用程式範本清單中進行選擇，以便使用精靈來安裝受歡迎的開放原始碼軟體，例如 WordPress、Joomla 和 Drupal。
* **Visual Studio 整合** - Visual Studio 中的專用工具可簡化建立、部署和偵錯的工作。

此外，Web 應用程式可以利用 [API Apps](app-service-web-tutorial-rest-api.md) (例如 CORS 支援) 和 [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) (例如推播通知) 所提供的功能。 

除了 App Service 中的 Web Apps，Azure 還提供可用來裝載網站和 Web 應用程式的其他服務。 大部分的情況下，Web Apps 是最佳選擇。  若是微服務架構，請考慮使用 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)，如果您需要能更加充分地掌控程式碼執行所在的 VM，則請考慮使用 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。 如需如何在這些 Azure 服務之間做選擇的詳細資訊，請參閱 [Azure App Service、虛擬機器、Service Fabric 及雲端服務的比較](choose-web-site-cloud-service-vm.md)。

## <a name="getting-started"></a>開始使用
若要開始在 App Service 中的新 Web 應用程式部署範例程式碼，請遵循下列下拉式方塊中的其中一個教學課程。 您將需要免費的 Azure 帳戶。

> [!div class="op_single_selector"]
> * [在 5 分鐘內將您的第一個 ASP.NET Web 應用程式部署到 Azure](app-service-web-get-started-dotnet.md)
> * [在 5 分鐘內將您的第一個 PHP Web 應用程式部署到 Azure](app-service-web-get-started-php.md)
> * [在 5 分鐘內將您的第一個 Node.js Web 應用程式部署到 Azure](app-service-web-get-started-nodejs.md)
> * [在 5 分鐘內將您的第一個 Java Web 應用程式部署到 Azure](app-service-web-get-started-java.md)
> * [在 5 分鐘內將您的第一個 Python Web 應用程式部署到 Azure](app-service-web-get-started-python.md)
> * [在 5 分鐘內將您的第一個 HTML 網站部署到 Azure](app-service-web-get-started-html.md)
> 
> 

> [!NOTE]
> 您可以[試用 App Service](https://azure.microsoft.com/try/app-service/)，而不需要 Azure 帳戶。 建立入門 App，並試用長達一小時。不需要信用卡，也不需簽定合約。
> 
> 
