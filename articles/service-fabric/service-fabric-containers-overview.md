---
title: "Service Fabric 和容器的概觀 | Microsoft Docs"
description: "Service Fabric 及使用容器來部署微服務應用程式的概觀。 本文提供如何使用容器及 Service Fabric 所提供之功能的概觀。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/16/2017
ms.author: msfussell
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: f770b6181a99d24ea6a6e945d505da914e1b6128
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="service-fabric-and-containers"></a>Service Fabric 和容器
> [!NOTE]
> Linux 的這項功能處於預覽狀態。  尚未支援將容器部署到 Windows 10 中的 Service Fabric 叢集 (即將推出)。 
>   

## <a name="introduction"></a>簡介
Azure Service Fabric 是跨機器叢集的服務的 [Orchestrator](service-fabric-cluster-resource-manager-introduction.md)，在 Microsoft 的大規模服務中已使用多年且經過最佳化。 開發服務有許多的方式，從 [Service Fabric 程式設計模型](service-fabric-choose-framework.md)，到部署[來賓可執行檔](service-fabric-deploy-existing-app.md)。 根據預設，Service Fabric 會以處理序形式部署和啟動這些服務。 處理序能以最快速度和最密集的程度使用叢集的資源。 Service Fabric 也可以在容器映像中部署服務。 重要的是，您可以在相同應用程式中混合容器中的服務和處理序中的服務。 

## <a name="containers-and-service-fabric-roadmap"></a>容器和 Service Fabric 藍圖
在未來推出的版本中，針對容器協調流程和 Service Fabric 有許多改進計劃。 包含增強的網路功能以支援應用程式內的虛擬網路、安全性功能、改良的診斷功能和工具支援。 Service Fabric 可讓您建立混合了容器與服務的應用程式；這裡是指封裝現有程式碼 (如 IIS MVC 應用程式) 的容器，以及使用 Service Fabric 程式設計模型開發的服務。  您可以在單一叢集上執行數個這類應用程式。 

## <a name="what-are-containers"></a>什麼是容器？
容器是封裝成可個別部署的元件，在相同核心上以隔離的執行個體執行，以利用作業系統提供的虛擬化。 因此，每個應用程式與其執行階段、相依性及系統程式庫在容器內執行時，在其各自於作業系統建構中的獨立範圍內，都具有容器的完整專屬存取權。 再加上可攜性，這種程度的安全性和資源隔離是容器與 Service Fabric 一起使用的主要優點 (否則是在處理序中執行服務)。

容器是從應用程式中將基礎作業系統虛擬化的一種虛擬化技術。 容器提供不變的環境讓應用程式以不同的隔離程度執行。 容器直接執行於核心之上，而且在檔案系統和其他資源中有其各自隔離的範圍。 相較於虛擬機器，容器的優點如下︰

* **小**︰容器使用單一儲存空間和層級版本與更新，以增加效率。
* **快**︰容器不需要啟動整個作業系統，因此可以更快啟動，通常差個幾秒。
* **可攜性**︰容器化應用程式映像可以移植到雲端或內部部署中，而在虛擬機器內或直接在實體機器上執行。
* **資源管理**：容器可以限制在其主機上可取用的實體資源。

## <a name="container-types"></a>容器類型
Service Fabric 支援 Linux 和 Windows 上的容器，同時也支援後者的 Hyper-V 隔離模式。 

### <a name="docker-containers-on-linux"></a>Linux 上的 Docker 容器
Docker 提供高階 API 來建立和管理 Linux 核心容器頂端的容器。 Docker 中樞是儲存和擷取容器映像的中央儲存機制。
如需教學課程，請參閱[將 Docker 容器部署至 Service Fabric](service-fabric-get-started-containers-linux.md)。

### <a name="windows-server-containers"></a>Windows Server 容器
Windows Server 2016 提供兩種不同的容器，所提供的隔離程度有所不同。 Windows Server 容器類似於 Docker 容器，因為都有命名空間和檔案系統隔離，但與它們執行所在的主機共用核心。 在 Linux 上，一向是由 `cgroups` 和 `namespaces` 提供這種隔離，而 Windows Server 容器具有類似的行為。

Windows Hyper-V 容器提供更高程度的隔離和安全性，因為每個容器彼此之間或與主機之間並不共用作業系統核心。 由於有如此高程度的安全性隔離，Hyper-V 容器在惡意多租用戶的情況下是重點部分。
如需教學課程，請參閱[將 Windows 容器部署至 Service Fabric](service-fabric-get-started-containers.md)。

下圖顯示作業系統中可用的各種不同的虛擬化及隔離等級。
![Service Fabric 平台][Image1]

## <a name="scenarios-for-using-containers"></a>容器使用案例
以下是典型範例，容器是很好的選擇︰

* **IIS 提起然後平移**︰如果您有想要繼續使用的現有 [ASP.NET MVC](https://www.asp.net/mvc) 應用程式，將它們放在一個容器，而不是移轉到 ASP.NET 核心。 這些 ASP.NET MVC 應用程式相依於網際網路資訊服務 (IIS)。 您可以從預先建立的 IIS 映像將這些應用程式封裝成容器映像，然後與 Service Fabric 一起部署。 請參閱 [Windows Server 上的容器映像](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images)，以取得如何建立 IIS 映像的詳細資訊。
* **混合容器和 Service Fabric 微服務**︰針對您的應用程式的一部分使用現有的容器映像。 例如，對於應用程式的 Web 前端系統，您可以使用 [NGINX 容器](https://hub.docker.com/_/nginx/)，而對於更密集的後端運算，則可以使用具狀態服務。
* **減少「壟斷」服務的影響**︰您可以使用容器的資源控管能力來限制服務在主機上使用的資源。 如果服務可能會耗用大量資源，因而影響其他服務的效能 (例如，像作業一樣長時間執行的查詢)，請考慮將這些服務放到可控管資源的容器中。

## <a name="service-fabric-support-for-containers"></a>Service Fabric 的容器支援
目前，Service Fabric 支援將 Docker 容器部署在 Linux，也支援將 Windows Server 容器部署在 Windows Server 2016 以及支援 Hyper-V 隔離模式。 

在 Service Fabric [應用程式模型](service-fabric-application-model.md)中，容器代表多個服務複本所在的應用程式主機。 Service Fabric 可以執行任何容器，其案例類似於[來賓可執行檔案例](service-fabric-deploy-existing-app.md)，可讓您封裝容器中的現有應用程式。 這是容器常見的使用案例，範例包括執行使用任何語言或架構但不使用內建的 Service Fabric 程式設計模型所撰寫的應用程式。

此外，您也可以執行容器內的 Service Fabric 服務。 目前，執行容器內 Service Fabric 服務的支援有限，但在未來推出的版本中會增強。

* **容器內的無狀態可靠服務**︰使用 Reliable Services 程式設計模型的無狀態可靠服務，僅在 Linux 上支援。 未來版本計劃將增加支援 Windows 容器中的無狀態可靠服務。
* **容器內的具狀態服務**︰這些服務使用 Reliable Actors 或 Reliable Services 程式設計模型。 未來版本中將增加支援容器中執行的具狀態服務。

Service Fabric 有數個容器功能可協助您建置由容器化微服務組成的應用程式。 Service Fabric 為容器化的服務提供下列功能：

* 容器映像部署和啟用。
* 資源管理。
* 儲存機制驗證。
* 容器連接埠至主機連接埠的對應。
* 容器至容器的探索及通訊。
* 能夠設定環境變數。

## <a name="next-steps"></a>後續步驟
在本文中，您已了解容器、Service Fabric 是容器協調者，以及 Service Fabric 具有支援容器的功能。 接下來，我們將示範每一項功能來展示其用法。

[將 Windows 容器部署至 Windows Server 2016 上的 Service Fabric](service-fabric-get-started-containers.md)

[將 Docker 容器部署至 Linux 上的 Service Fabric](service-fabric-get-started-containers-linux.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

