---
title: "在 Mac OS X 上設定開發環境以搭配 Azure Service Fabric 運作 | Microsoft Docs"
description: "安裝執行階段、SDK 和工具，並建立本機開發叢集。 完成此設定之後，您就可以開始在 Mac OS X 上建置應用程式。"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: saysa
ms.openlocfilehash: cf67c377cd5c17f7b540fb23af48a333a9cddf69
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>在 Mac OS X 上設定開發環境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

您可以建置 Service Fabric 應用程式以在使用 Mac OS X 的 Linux 叢集上執行。本文涵蓋如何設定您的 Mac 進行開發。

## <a name="prerequisites"></a>必要條件
Service Fabric 不會在 OS X 上以原生方式執行。若要執行本機 Service Fabric 叢集，我們提供預先設定的 Docker 容器映像。 開始之前，您需要：

* 至少 4 GB 的 RAM
* 最新版 [Docker](https://www.docker.com/)
* 存取 Service Fabric One-box Docker 容器[映像](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/)

>[!TIP]
> * 您可以遵循官方 Docker [文件](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install)中所述的步驟，在 Mac 上安裝 Docker。 
> * 一旦您完成安裝後，如果遵循[這裡](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine)所述的步驟正確安裝，就進行驗證


## <a name="create-a-local-container-and-setup-service-fabric"></a>建立本機容器並設定 Service Fabric
若要設定本機的 Docker 容器，並在容器上執行 Service Fabric 叢集，請執行下列步驟：

1. 從 Docker 中樞存放庫中提取映像：

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. 使用映像啟動 Service Fabric One-box 容器執行個體：

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    >您可以藉由指定名稱給容器執行個體，以更易於閱讀的方式加以處理。 

3. 在互動式 SSH 模式中登入 Docker 容器：

    ```bash
    docker exec -it sfonebox bash
    ```

4. 執行可擷取必要相依性的設定指令碼，然後在容器上啟動叢集。

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

5. 順利完成步驟 4 之後，您可以從 Mac 移至 ``http://localhoist:19080``，就能看到 Service Fabric 總管。


## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>設定 Mac 上的 Service Fabric CLI (sfctl)

請遵循 [Service Fabric CLI](service-fabric-cli.md#cli-mac) 的指示在您的 Mac 安裝 Service Fabric CLI (`sfctl`)。
可供與 Service Fabric 實體 (包括叢集、應用程式和服務) 進行互動的 CLI 命令。

## <a name="create-application-on-your-mac-using-yeoman"></a>在 Mac 上使用 Yeoman 建立應用程式

Service Fabric 提供的 Scaffolding 工具可協助您從終端機使用 Yeoman 範本產生器建立 Service Fabric 應用程式。 請遵循下列步驟，確保您有 Service Fabric Yeoman 範本產生器可在電腦上運作。

1. 您必須在 Mac 上安裝 Node.js 和 NPM。 若未這麼做，您可以利用下列步驟，使用 Homebrew 安裝 Node.js 和 NPM。 若要檢查 Mac 上安裝的 Node.js 和 NPM 版本，您可以使用 ``-v`` 選項。

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. 從 NPM 在您的電腦上安裝 [Yeoman](http://yeoman.io/) 範本產生器。

    ```bash
    npm install -g yo
    ```
3. 遵循快速入門[文件](service-fabric-get-started-linux.md)中的步驟，安裝您要使用的 Yeoman 產生器。 若要使用 Yeoman 建立 Service Fabric 應用程式，請遵循下列步驟：

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. 若要在 Mac 上建置 Service Fabric Java 應用程式，您必須在主機電腦上安裝 JDK 1.8 和 Gradle。 如果尚不存在，您可以使用 [HomeBrew](https://brew.sh/) 來安裝。 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>在 Mac 上從終端機部署應用程式

一旦您建立並建置 Service Fabric 應用程式後，就可以遵循下列步驟，使用 [Service Fabric CLI](service-fabric-cli.md#cli-mac) 部署應用程式：

1. 在 Mac 上連線到容器執行個體內執行的 Service Fabric 叢集。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 進入您的專案目錄，並執行安裝指令碼。

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>設定 .NET Core 2.0 開發

安裝 [.NET Core 2.0 SDK for Mac](https://www.microsoft.com/net/core#macos) 以開始[建立 C# Service Fabric 應用程式](service-fabric-create-your-first-linux-application-with-csharp.md)。 .NET Core 2.0 Service Fabric 應用程式的套件裝載於 NuGet.org (目前處於預覽狀態)。

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>在 Mac 上安裝適用於 Eclipse Neon 的 Service Fabric 外掛程式

Service Fabric 為**適用於 Java IDE 的 Eclipse Neon** 提供了外掛程式，可簡化建立、建置和部署 Java 服務的程序。 您可以遵循這個有關安裝或將 Service Fabric Eclipse 外掛程式更新到最新版本的一般[文件](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)中所述的安裝步驟。

[Service Fabric Eclipse 文件](service-fabric-get-started-eclipse.md)中所述的所有其他步驟，包含建置應用程式、將服務新增至應用程式、安裝/解除安裝應用程式等，也都可以套用在這裡。

除了上述的步驟之外，若要讓 Service Fabric Eclipse 外掛程式在 Mac 上與 Docker 容器搭配使用，您應該使用與主機共用的路徑將容器具現化，如下所示：
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
其中 ``/Users/sayantan/work/workspaces/mySFWorkspace`` 是 Mac 上的工作區完整路徑，而 ``/tmp/mySFWorkspace`` 是它在容器內所對應到的路徑。

> [!NOTE]
>1. 如果您的工作區名稱/路徑不同，請在上述 ``docker run`` 命令中據此更新為相同。
>2. 如果您使用 ``sfonebox`` 以外的不同名稱來啟動容器，請在您 Service Fabric 執行者 Java 應用程式中的 ``testclient.sh`` 檔案更新為相同。

## <a name="next-steps"></a>後續步驟
<!-- Links -->
* [使用 Yeoman 在 Linux 上建立和部署第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
* [在 Linux 上使用適用於 Eclipse 的 Service Fabric 外掛程式建立和部署第一個 Service Fabric Java 應用程式](service-fabric-get-started-eclipse.md)
* [在 Azure 入口網站中建立 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)
* [使用 Azure Resource Manager 建立 Service Fabric 叢集](service-fabric-cluster-creation-via-arm.md)
* [了解 Service Fabric 應用程式模型](service-fabric-application-model.md)
* [使用 Service Fabric CLI 管理應用程式](service-fabric-application-lifecycle-sfctl.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
