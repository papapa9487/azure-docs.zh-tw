---
title: "Linux 上的 Azure Service Fabric | Microsoft Docs"
description: "Service Fabric 叢集支援 Linux 和 Java，這表示您能夠在 Linux 上部署和裝載以 Java 和 C# 撰寫的 Service Fabric 應用程式。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 459afade-145d-4ee6-b72b-ddf380ccd1bf
ms.service: service-fabric
ms.devlang: Java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: SubramaR
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: ad20216374306d8b5035140f3d8e107c595b623e
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017

---
# <a name="service-fabric-on-linux"></a>Linux 上的 Service Fabric
Linux 上的 Service Fabric 預覽版可讓您如同在 Windows 上一樣，在 Linux 上建置、部署和管理高可用性、可高度調整的應用程式。 Service Fabric 架構 (Reliable Services 和 Reliable Actors) 除了可在 C# (.NET Core) 中使用，也能在 Java on Linux 中使用。  您也可以使用任何語言或架構來建置 [來賓可執行檔服務](service-fabric-deploy-existing-app.md) 。 此外，預覽也支援協調 Docker 容器。 Docker 容器可以執行使用 Service Fabric 架構的來賓可執行檔或原生 Service Fabric 服務。

Linux 上的 Service Fabric 在概念上相當於 Windows 上的 Service Fabric (除了作業系統細節和程式設計語言支援)。 因此，我們大部分的 [現有文件](http://aka.ms/servicefabricdocs) 都可協助您熟悉這項技術。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Service-Fabric-Linux-Preview/player]
>
>

## <a name="supported-operating-systems-and-programming-languages"></a>支援的作業系統和程式設計語言
有限預覽版除了支援在 Azure 中執行 Ubuntu Server 16.04 的多部電腦叢集，還支援建立一整體開發叢集。 除了來賓可執行檔和協調 Docker 容器，預覽在 Java 和 C# 中還支援 Reliable Actors 和 Reliable Stateless Services 架構。  

> [!NOTE]
> Linux 尚未支援 Reliable Collections。 也不支援獨立叢集 - 預覽中僅支援單機和 Azure Linux 多電腦叢集。
>
>


## <a name="supported-tooling"></a>支援的工具
預覽支援透過 Azure CLI 來與叢集進行互動。 針對 Java 開發人員，透過 Linux 和 OSX 上支援的 Eclipse，就能與 Eclipse 和 Yeoman 整合。 OSX 整合透過 vagrant 在幕後使用 Linux VM。 針對 C# 開發人員，支援與 Yeoman 整合來產生應用程式範本。

## <a name="next-steps"></a>後續步驟
1. 請熟悉 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 和 [Reliable Services](service-fabric-reliable-services-introduction.md) 程式設計架構。
2. [在 Linux 上準備您的開發環境](service-fabric-get-started-linux.md)
3. [在 OSX 上準備您的開發環境](service-fabric-get-started-mac.md)
4. [在 Linux 上建立第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
5. [使用 Jenkins 和 GitHub 設定 Service Fabric 連續整合和部署](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
6. [Service Fabric Windows/Linux 的差異](service-fabric-linux-windows-differences.md)

