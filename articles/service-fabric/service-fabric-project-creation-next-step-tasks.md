---
title: "Service Fabric 專案建立後續步驟 |Microsoft Docs"
description: "本文包含一組用於 Service Fabric 的核心開發工作連結"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: rwike77
ms.openlocfilehash: e04f9e57c65da42da73a5ee6a0b601dcbb318aaa
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>您的 Service Fabric 應用程式和後續步驟
您的 Azure Service Fabric 應用程式已經建立。 本文說明您專案的建立方式和一些潛在後續步驟。

## <a name="your-application"></a>您的應用程式
每個新應用程式都包含一個應用程式專案。 視所選擇的服務類型而定，可能會有一個或兩個額外的專案。

### <a name="the-application-project"></a>應用程式專案
應用程式專案包含：

* 一組對構成應用程式之服務的參考。
* 三個發行設定檔 (1-Node Local、5-Node Local、Cloud)，可用來維護喜好設定，以搭配不同的環境使用，例如叢集端點的相關喜好設定，以及是否預設要執行升級部署。
* 三個應用程式參數檔案 (同上)，可用來維護環境特定應用程式組態，例如為服務建立之分割區的數目。
* 部署指令碼，可用來從命令列部署您的應用程式，或透過自動化連續整合和部署管線中部署您的應用程式。
* 應用程式資訊清單，描述應用程式。 您可以在 ApplicationPackageRoot 資料夾下方找到資訊清單。

### <a name="stateless-service"></a>無狀態服務
當您加入一個新的無狀態服務時，Visual Studio 會將一個服務專案 (包含來自 `StatelessService`的類型) 加入至您的解決方案。 此服務會使計數器中的區域變數遞增。

### <a name="stateful-service"></a>具狀態服務
當您加入一個新的具狀態服務時，Visual Studio 會將一個服務專案 (包含來自 `StatefulService`的類型) 加入至您的解決方案。 此服務會將 `RunAsync` 方法中的計數器遞增，並將結果儲存在 `ReliableDictionary` 中。

### <a name="actor-service"></a>動作項目服務
當您加入一個新的 Reliable Actor 時，Visual Studio 會將兩個專案加入至您的解決方案：動作項目專案和介面專案。

動作項目專案提供了一些方法，以便設定和取得動作項目的狀態中可靠地保存的計數器值。 介面專案提供了其他服務可用來叫用動作項目的介面。

### <a name="stateless-web-api"></a>無狀態 Web API
無狀態 Web API 專案會提供基本一項 Web 服務，您可用來對外部用戶端開放您的應用程式。 如需有關如何建構專案的詳細資訊，請參閱 [Service Fabric Web API 服務與 OWIN 自我裝載](service-fabric-reliable-services-communication-webapi.md)。


### <a name="aspnet-core"></a>ASP.NET 核心
Service Fabric SDK 提供 ASP.NET Core 範本的相同集合，適用於獨立 ASP.NET Core 專案︰空的、[Web API][aspnet-webapi] 和 [Web 應用程式][aspnet-webapp]。

### <a name="guest-executables-and-guest-containers"></a>客體可執行檔和客體容器

Service Fabric「客體」這個服務並非由平台的程式設計模型所建立。 您可以[直接在應用程式套件中](service-fabric-deploy-existing-app.md)或[透過容器映像](service-fabric-deploy-container.md)，將客體的二進位檔案進行封裝。 在這兩種情況下，Visual Studio 會在應用程式專案的 **ApplicationPackageRoot** 資料夾中建立必要的成品。 Visual Studio 不會建立新的服務專案，因為程式碼已經存在於其他地方。 如果您想要同時管理客體專案與 Service Fabric 應用程式專案，您可以將它們新增至相同的 Visual Studio 解決方案。

## <a name="next-steps"></a>後續步驟
### <a name="create-an-azure-cluster"></a>建立 Azure 叢集
Service Fabric SDK 提供一個用於開發和測試的本機叢集。 若要在 Azure 中建立叢集，請參閱[從 Azure 入口網站設定 Service Fabric 叢集][create-cluster-in-portal]。

### <a name="publish-your-application-to-azure"></a>將應用程式發行至 Azure
您可以直接從 Visual Studio 將應用程式發行至 Azure 叢集。 若要瞭解做法，請參閱[將應用程式發佈至 Azure][publish-app-to-azure]。

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>使用 Service Fabric 總管將叢集視覺化
「Service Fabric 總管」提供一個將叢集 (包括已部署的應用程式和實體配置) 視覺化的簡單方法。 若要深入了解，請參閱[使用 Service Fabric Explorer 將叢集視覺化][visualize-with-sfx]。

### <a name="version-and-upgrade-your-services"></a>進行服務版本設定和升級
Service Fabric 可讓您為應用程式中的獨立服務進行獨立的版本設定和升級。 若要深入了解，請參閱[進行服務版本設定和升級][app-upgrade-tutorial]。

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>使用 Visual Studio Team Services 設定持續整合
若要深入了解如何為 Service Fabric 應用程式設定持續整合程序，請參閱[使用 Visual Studio Team Services 設定持續整合][ci-with-vso]。

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-manage-application-in-visual-studio.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
