---
title: "將容器中的 .NET 應用程式部署到 Azure Service Fabric | Microsoft Docs"
description: "教導您如何在 Visual Studio 中以 Docker 容器封裝 .NET 應用程式。 然後，將這個新的「容器」應用程式部署到 Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 484db494e7975df950543d19bf841a4df7cdd139
ms.contentlocale: zh-tw
ms.lasthandoff: 07/20/2017

---

# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>將 Windows 容器中的 .NET 應用程式部署到 Azure Service Fabric

本教學課程示範如何將 Windows 容器中現有的 ASP.NET 應用程式部署到 Azure。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 Visual Studio 中建立 Docker 專案
> * 將現有的應用程式容器化
> * 使用 Visual Studio 和 VSTS 設定持續整合

## <a name="prerequisites"></a>必要條件

1. 安裝 [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)，以便在 Windows 10 上執行容器。
2. 熟悉 [Windows 10 容器快速入門][link-container-quickstart]。
3. 下載 [Fabrikam Fiber CallCenter][link-fabrikam-github] 範例應用程式。
4. 安裝 [Azure PowerShell][link-azure-powershell-install]
5. 安裝 [Visual Studio 2017 的持續傳遞工具延伸模組][link-visualstudio-cd-extension]
6. 建立 [Azure 訂用帳戶][link-azure-subscription]和 [Visual Studio Team Services 帳戶][link-vsts-account]。 
7. [在 Azure 上建立叢集](service-fabric-tutorial-create-cluster-azure-ps.md)

## <a name="containerize-the-application"></a>將應用程式容器化

您現在有 [Service Fabric 叢集正在 Azure 中執行](service-fabric-tutorial-create-cluster-azure-ps.md)，即準備好要建立及部署容器化應用程式。 為了開始執行我們在容器中的應用程式，我們必須在 Visual Studio 的專案中新增「Docker 支援」。 當您在應用程式中新增「Docker 支援」時，會發生兩件事。 首先，會在專案中新增一個 _Dockerfile_。 這個新檔案說明容器映像的建置方式。 其次，會在方案中加入一個新的 _docker-compose_ 專案。 這個新專案包含幾個 docker-compose 檔案， 可用來說明容器的執行方式。

深入了解如何使用 [Visual Studio 容器工具][link-visualstudio-container-tools]。

>[!NOTE]
>如果您第一次在電腦上執行 Windows 容器映像，則 Docker CE 必須下拉適用於您容器的基底映像。 本教學課程中使用的映像為 14 GB。 請繼續並執行下列終端機命令來提取基底映像：
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>新增 Docker 支援

在 Visual Studio 中開啟 [FabrikamFiber.CallCenter.sln][link-fabrikam-github] 檔案。

在 [FabrikamFiber.Web] 專案上按一下滑鼠右鍵 > [加入] > [Docker 支援]。

### <a name="add-support-for-sql"></a>新增對 SQL 的支援

此應用程式使用 SQL 作為資料提供者，因此必須要有 SQL Server，才能執行此應用程式。 請參考 docker-compose.override.yml 檔案中的 SQL Server 容器映像。

在 Visual Studio 中，開啟**方案總管**，尋找 **docker-compose**，然後開啟檔案 **docker-compose.override.yml**。

巡覽至 `services:` 節點，並新增一個名為`db:` 的節點，以定義容器的 SQL Server 項目。

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>您可以使用任何慣用的 SQL Server 來進行本機偵錯，只要能夠從您的主機連線到該 SQL Server 即可。 不過，**localdb** 不支援 `container -> host` 通訊。

>[!WARNING]
>執行容器中的 SQL Server 時，不支援保存資料。 當容器停止時，會清除您的資料。 因此，請不要將這個設定用於生產環境。

巡覽至 `fabrikamfiber.web:` 節點，並新增一個名為 `depends_on:` 的子節點。 這可確保 `db` 服務 (SQL Server 容器) 會在我們的 Web 應用程式 (fabrikamfiber.web) 啟動之前啟動。

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>更新 Web 設定

回到 **FabrikamFiber.Web** 專案，更新 **web.config** 檔案中的連接字串以指向容器中的 SQL Server。

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>在建置 Web 應用程式的發行組建時，如果您想要使用不同的 SQL Server，請在您的 web.release.config 檔案中新增另一個連接字串。

### <a name="test-your-container"></a>測試您的容器

按 **F5** 以執行您容器中的應用程式並進行偵錯。

Edge 會使用容器在內部 NAT 網路上的 IP 位址 (通常是 172.x.x.x) 來開啟您應用程式的已定義啟動頁面。 若要深入了解如何使用 Visual Studio 2017 對容器中的應用程式進行偵錯，請參閱[這篇文章][link-debug-container]。

![容器中 fabrikam 的範例][image-web-preview]

現在準備好在 Service Fabric 應用程式中建置及封裝此容器。 在您的電腦上建置完容器映像之後，您便可以將它推送到任何容器登錄，然後下拉到任何主機來執行。

## <a name="get-the-application-ready-for-the-cloud"></a>準備在雲端中使用應用程式

若要準備在 Azure 的 Service Fabric 中執行應用程式，必須完成下列兩個步驟：

1. 公開連接埠，以便透過該連接埠連接至 Service Fabric 叢集中的 Web 應用程式。
2. 提供適用於應用程式且準備好用於生產環境的 SQL 資料庫。

### <a name="expose-the-port-for-the-app"></a>公開應用程式的連接埠
根據預設，Azure Load Balancer 會為我們設定好的 Service Fabric 叢集開啟連接埠 *80*，以平衡叢集的傳入流量。 透過 docker-compose.yml 檔案，我們可以在此連接埠上公開容器。

在 Visual Studio 中，開啟**方案總管**，尋找 **docker-compose**，然後開啟檔案 **docker-compose.override.yml**。

修改 `fabrikamfiber.web:` 節點，新增一個名為 `ports:` 的子節點。

新增字串項目 `- "80:80"`。

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>使用生產環境 SQL 資料庫
在生產環境中執行時，我們需要將資料保存在資料庫中。 目前無法保證資料能保存在容器中，因此請不要將 SQL Server 生產環境的資料儲存在容器中。

建議您利用 Azure SQL Database。 若要在 Azure 中設定並執行受管理的 SQL Server，請瀏覽 [Azure SQL Database 快速入門][link-azure-sql]一文。

>[!NOTE]
>記得將 **FabrikamFiber.Web** 專案中 **web.release.config** 檔案的連接字串變更為 SQL Server。
>
>如果無法連線到任何 SQL 資料庫，此應用程式會依正常程序失敗。 您可以選擇在沒有 SQL Server 的情況下繼續部署應用程式。

## <a name="deploy-with-visual-studio-team-services"></a>使用 Visual Studio Team Services 進行部署

若要使用 Visual Studio Team Services 來設定部署，您必須安裝 [Visual Studio 2017 的持續傳遞工具擴充功能][link-visualstudio-cd-extension]。 此擴充功能可讓您透過設定 Visual Studio Team Services 來輕鬆部署至 Azure，並將您的應用程式部署至 Service Fabric 叢集。

若要開始進行，您的程式碼必須裝載在原始檔控制中。 本節的其餘部分會假設使用 **git**。

### <a name="set-up-a-vsts-repo"></a>設定 VSTS 存放庫
在 Visual Studio 右下角，按一下 [加入至原始檔控制] > [Git] \(或您慣用的任何一個選項)。

![按原始檔控制按鈕][image-source-control]

在 [Team Explorer] 窗格中，按 [發行 Git 儲存機制]。

選取您的 VSTS 存放庫名稱，然後按 [存放庫]。

![將儲存機制發行至 VSTS][image-publish-repo]

既然您的程式碼已與 VSTS 來源存放庫同步，現在您就可以設定持續整合和持續傳遞。

### <a name="setup-continuous-delivery"></a>設定持續傳遞

在 [方案總管] 中，於 [方案] 上按一下滑鼠右鍵 > [設定持續傳遞]。

選取 Azure 訂用帳戶。

將 [主機類型] 設定為 [Service Fabric 叢集]。

將 [目標主機] 設定為您在上一節中建立的 Service Fabric 叢集。

選擇一個作為您容器發行目的地的**容器登錄**。

>[!TIP]
>使用 [編輯] 按鈕來建立容器登錄。

按 [確定]。

![設定 Service Fabric 持續整合][image-setup-ci]
   
   完成設定之後，您的容器即會部署到 Service Fabric。 每當您將更新推送至存放庫時，就會執行新的組建與版本。
   
   >[!NOTE]
   >建立容器映像約需 15 分鐘。
   >Service Fabric 叢集的第一個部署會引發下載基底 Windows Server Core 容器映像的動作。 此下載還需要額外的 5-10 分鐘才能完成。

使用您的叢集 URL 瀏覽至 Fabrikam Call Center 應用程式，例如 *http://mycluster.westeurope.cloudapp.azure.com*。

既然您已將 Fabrikam Call Center 方案容器化並進行部署，現在您就可以開啟 [Azure 入口網站][link-azure-portal]並看到應用程式在 Service Fabric 中執行。 若要試試應用程式，請開啟網頁瀏覽器並移至您 Service Fabric 叢集的 URL。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Visual Studio 中建立 Docker 專案
> * 將現有的應用程式容器化
> * 使用 Visual Studio 和 VSTS 設定持續整合

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

