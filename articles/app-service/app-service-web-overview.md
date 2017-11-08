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
ms.openlocfilehash: f8510bb6b412e9af8aad30ba32bc74206c22042f
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2017
---
# <a name="web-apps-overview"></a>Web 應用程式概觀

「Azure App Service Web Apps」(或簡稱 Web Apps) 是用來裝載 Web 應用程式、REST API 和行動後端的服務。 您可以使用您慣用的語言進行開發，不管是 .NET、.NET Core、Java、Ruby、Node.js、PHP 還是 Python 都可以。 您可以輕鬆地在 Windows 或 Linux VM 上執行和調整應用程式 (請參閱 [Linux 上的 App Service](containers/app-service-linux-intro.md))。 

Web Apps 不只能將 Microsoft Azure 的功能新增到您的應用程式，例如安全性、負載平衡、自動調整和自動化管理。 您也可以利用它的 DevOps 功能，例如從 VSTS、GitHub、Docker Hub 和其他來源進行持續部署、套件管理、預備環境、自訂網域和 SSL 憑證。 

在使用 App Service 時，您只需就您所使用的 Azure 計算資源支付費用。 您所使用的計算資源取決於您用來執行 Web Apps 的 _App Service 方案_。 如需詳細資訊，請參閱 [Azure Web Apps 中的 App Service 方案](azure-web-sites-web-hosting-plans-in-depth-overview.md)。

下列的 5 分鐘影片將介紹 Azure App Service Web Apps。

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

## <a name="why-use-web-apps"></a>為何要使用 Web Apps？
以下是 App Service Web Apps 的一些主要功能︰

* **多種語言和架構** - Web Apps 具有 ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP 或 Python 的第一級支援。 您也可以將 [PowerShell 和其他指令碼或可執行檔](web-sites-create-web-jobs.md) 做為背景服務來執行。
* **DevOps 最佳化** - 使用 Visual Studio Team Services、GitHub、BitBucket、Docker Hub 或 Azure Container Service 設定[持續整合和部署](app-service-continuous-deployment.md)。 透過 [測試和預備環境](web-sites-staged-publishing.md)升級更新。 使用 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 或[跨平台命令列介面 (CLI)](/cli/azure/install-azure-cli)，在 Web Apps 中管理您的應用程式。
* **具高可用性的全域調整** - 以手動或自動方式相應[增加](web-sites-scale.md)或[放大](../monitoring-and-diagnostics/insights-how-to-scale.md)。 在 Microsoft 的通用資料中心基礎結構中隨處裝載您的應用程式，而 App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 會承諾高可用性。
* **SaaS 平台和內部部署資料的連線** - 有超過 50 種適用於企業系統 (例如 SAP)、SaaS 服務 (例如 Salesforce) 和網際網路服務 (例如 Facebook) 的[連接器](../connectors/apis-list.md)可供選擇。 使用[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)和 [Azure 虛擬網路](web-sites-integrate-with-vnet.md)存取內部部署資料。
* **安全性和法規遵循** - App Service 為 [ISO、SOC 和 PCI 相容](https://www.microsoft.com/TrustCenter/)。 可使用 [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) 或社交登入 ([Google](app-service-mobile-how-to-configure-google-authentication.md)、[Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)、[Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) 和 [Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)) 驗證使用者。 建立 [IP 位址限制](app-service-ip-restrictions.md)和[管理服務身分識別](app-service-managed-service-identity.md)。
* **應用程式範本** - 從 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 中的廣泛應用程式範本清單中進行選擇，例如 WordPress、Joomla 和 Drupal。
* **Visual Studio 整合** - Visual Studio 中的專用工具可簡化建立、部署和偵錯的工作。
* **API 和行動功能** - Web Apps 可提供適用於 RESTful API 案例的現成 CORS 支援，並可藉由啟用驗證、離線資料同步和推播通知等功能，簡化行動應用程式案例。
* **無伺服器程式碼** - 可隨需執行程式碼片段或指令碼，而不必明確佈建或管理基礎結構，而且只須就程式碼實際使用的計算時間支付費用 (請參閱 [Azure Functions](/azure/azure-functions/))。

除了 App Service 中的 Web Apps，Azure 還提供可用來裝載網站和 Web 應用程式的其他服務。 大部分的情況下，Web Apps 是最佳選擇。  若是微服務架構，請考慮使用 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。 如果您需要能更加充分地掌控程式碼執行所在的 VM，則請考慮使用 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。 如需如何在這些 Azure 服務之間做選擇的詳細資訊，請參閱 [Azure App Service、虛擬機器、Service Fabric 及雲端服務的比較](choose-web-site-cloud-service-vm.md)。

## <a name="next-steps"></a>後續步驟

建立您的第一個 Web 應用程式。

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)

