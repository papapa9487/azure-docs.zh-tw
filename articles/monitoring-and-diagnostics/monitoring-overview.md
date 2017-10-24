---
title: "Microsoft Azure 中的監視 | Microsoft Docs"
description: "您想要監視 Microsoft Azure 中的任何項目時的選項。 Azure 監視器、Application Insights 及 Log Analytics"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: e164cbd910ccc38610c7aef37d25ff1b4413038d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Microsoft Azure 中的監視概觀
本文提供一個概觀，說明全面監視 Microsoft Azure 時所涉及的工具和服務。 適用範圍：
- 使用 Azure 服務來監視 Azure 基礎結構和應用程式
- 使用 Azure 服務來監視混合式及非 Azure 基礎結構和應用程式
- 使用非 Azure 服務來監視 Azure 基礎結構和應用程式

其會討論各種可用的產品與服務，以及它們如何一起運作。 本文可協助您判斷哪些工具在哪些情況下最適合您。  

## <a name="why-use-azures-monitoring-services"></a>為什麼要使用 Azure 的監視服務？

您雲端應用程式中的效能問題可能會對企業產生影響。 透過多個互連的元件和頻繁的發行，隨時都可能導致效能降低。 此外，如果您正在開發應用程式，您的使用者通常會探索到您在測試時未發現的問題。 您應該立即知道這些問題，並具備可用以診斷和修復問題的工具。 此外，您應用程式中的問題通常是由這些應用程式執行所在的基礎結構所造成的，因此全面檢視您的應用程式和基礎結構是監視您 Azure 環境的關鍵。 Microsoft Azure 提供一系列的工具來識別和解決這類問題。

## <a name="how-do-i-monitor-my-azure-environment"></a>如何監視我的 Azure 環境？

有一系列工具可監視您的 Azure 環境，從在 Azure 上執行的應用程式程式碼，到執行該程式碼的服務和基礎結構，一應俱全。 這些工具會一起運作來提供全面性的雲端監視功能，其中包括：

-   **Azure 監視器** - 這是一項 Azure 服務，其運作方式是作為 Azure 服務之所有監視資料的合併管線。 它可讓您存取效能計量和事件，這些計量和事件可說明 Azure 基礎結構及您所使用之任何 Azure 服務的作業。 「Azure 監視器」是您 Azure 環境的監視資料管線，並且會將該資料直接提供給 Log Analytics 及協力廠商工具，您可以從中深入了解該資料，以及將其與來自內部部署或其他雲端資源的資料結合。

-   **Application Insights** - 這是一項 Azure 服務，可提供應用程式效能監視功能和使用者分析。 它會監視您已撰寫的程式碼，以及您已在 Azure 或內部環境/其他雲端上部署的應用程式。 藉由使用 Application Insights SDK 來檢測您的應用程式，您將可以存取一系列資料，包括相依項目的回應時間、例外狀況追蹤、偵錯快照，以及執行設定檔。 它提供功能強大的工具來分析此應用程式遙測，同時還能開發和操作您的應用程式。 它與 Visual Studio 深入整合，可讓您直接移至有問題的程式碼行來修正它，並且也為產品經理提供使用量分析，以分析客戶對您應用程式的使用量。

-   **Log Analytics** - 以前稱為 OMS Log Analytics，是一項 Azure 服務，此服務會從 Azure 服務 (透過「Azure 監視器」)、Azure VM 及內部部署或其他雲端基礎結構內嵌記錄和計量資料，除了此資料之外，還提供彈性的記錄搜尋和現成分析。 它提供豐富的工具來跨來源分析資料、允許跨所有記錄進行複雜的查詢，而且可以依據指定的條件主動發出警示。  您甚至可以將自訂資料收集到其中央存放庫，如此便能查詢並將其視覺化。 您也可以利用 Log Analytic 的內建解決方案，來立即深入了解您基礎結構的安全性和功能。

## <a name="accessing-monitoring-in-the-azure-portal"></a>在 Azure 入口網站中存取監視
所有的 Azure 監視服務現在都可在單一 UI 窗格中取得。 如需如何存取此區域的詳細資訊，請參閱[開始使用 Azure 監視器](monitoring-get-started.md)。 

您也可以存取特定資源的監視功能，方法是將那些資源反白顯示，然後向下切入至它們的監視選項。 

## <a name="examples-of-when-to-use-which-tool"></a>何時應使用哪個工具的範例 

以下各節將說明一些基本案例，以及應該一起使用哪些工具。 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>案例 1：在開發期間，修正 Azure 應用程式中的錯誤   

**最佳選項是一起使用 Application Insights、Azure 監視器和 Visual Studio**

Azure 目前在雲端中提供 Visual Studio Debugger 的完整功能。 設定 Azure 監視器，以將遙測傳送至 Application Insights。 讓 Visual Studio 可將 Application Insights SDK 納入您的應用程式。 一旦位於 Application Insights 之後，您就能使用應用程式對應，以視覺化方式探索您執行中應用程式的哪些部分是否狀況不良。 針對那些狀況不良的部分，已經有錯誤和例外狀況可供探索。 您可以使用 Application Insights 中的各種分析來深入了解。 如果您不確定錯誤，可以使用 Visual Studio 偵錯工具來追蹤程式碼，並進一步準確地確認問題。 

如需詳細資訊，請參閱[監視 Web Apps](../application-insights/app-insights-azure-web-apps.md)，並參考左邊的目錄以取得各種類型之應用程式和語言的相關指示。  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>案例 2：對 Azure.NET Web 應用程式進行偵錯，以取得只會在生產環境中顯示的錯誤 

> [!NOTE]
> 這些功能目前為預覽狀態。 

**最佳選項是使用 Application Insights，而且，如果可能，請使用 Visual Studio 以取得完整的偵錯體驗。**

使用 Application Insights 快照集偵錯工具來偵錯您的應用程式。 當生產元件發生特定的錯誤臨界值時，系統會在稱為「快照集」的時段中自動擷取遙測。 擷取的數量對生產雲端而言是安全的，因為夠小而不足以影響效能，但顯然足以允許追蹤。  系統可以擷取多個快照集。 您可以查看 Azure 入口網站中的時間點，或使用 Visual Studio 取得完整體驗。 使用 Visual Studio，開發人員就能逐步執行該快照集，如同他們正在進行即時偵錯。 本機變數、參數、記憶體及框架全都可供使用。 開發人員必須透過 RBAC 角色，被授與存取此生產資料的權限。  

如需詳細資訊，請參閱[快照集偵錯](../application-insights/app-insights-snapshot-debugger.md)。 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>案例 3：對使用容器或微服務的 Azure 應用程式進行偵錯 

**與案例 1 相同。一起使用 Application Insights、Azure 監視器和 Visual Studio** Application Insights 也支援從在容器內部執行的程序以及從微服務 (Kubernetes、Docker、Azure Service Fabric) 蒐集遙測。 如需詳細資訊，[請參閱這段對容器和微服務進行偵錯的相關影片](https://go.microsoft.com/fwlink/?linkid=848184)。 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>案例 4：修正 Azure 應用程式中的效能問題

[Application Insights Profiler](../application-insights/app-insights-profiler.md) 的設計目的是協助對這些類型的問題進行疑難排解。 您可以針對在應用程式服務 (Web Apps、Logic Apps、Mobile Apps、API Apps) 中執行的應用程式及其他元件 (例如，虛擬機器、虛擬機器擴展集 (VMSS)、雲端服務及 Service Fabric)，識別效能問題並進行疑難排解。 

> [!NOTE]
> 分析虛擬機器、虛擬機器擴展集 (VMSS)、雲端服務及 Service Fabric 的功能目前為預覽狀態。   

此外，智慧型偵測工具會透過電子郵件主動通知您特定錯誤類型的相關資訊，例如頁面載入時間變慢。  您不需要在此工具上進行任何設定。 如需詳細資訊，請參閱[智慧型偵測 - 效能異常](../application-insights/app-insights-proactive-performance-diagnostics.md)。



## <a name="next-steps"></a>後續步驟
深入了解

* [Ignite 2016 上的 Azure 監視器影片](https://myignite.microsoft.com/videos/4977)
* [開始使用 Azure 監視器](monitoring-get-started.md)
* [Azure 診斷](../azure-diagnostics.md)，如果您正在嘗試診斷雲端服務、虛擬機器、虛擬機器擴展集或 Service Fabric 應用程式中的問題。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) ，如果您正在嘗試診斷 App Service Web 應用程式中的問題。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 及 [Operations Management Suite](https://www.microsoft.com/oms/)，監視生產環境的解決方案
