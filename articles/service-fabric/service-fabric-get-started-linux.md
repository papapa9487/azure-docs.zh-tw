---
title: "在 Linux 上設定開發環境 | Microsoft Docs"
description: "在 Linux 上安裝執行階段和 SDK，並建立本機開發叢集。 完成此設定之後，您就可以開始建置應用程式。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/23/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 4f51030446d2d2a5a11018b1fce7d7e9193f3dfc
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---
# <a name="prepare-your-development-environment-on-linux"></a>在 Linux 上準備您的開發環境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

若要在 Linux 開發機器上部署和執行 [Azure Service Fabric 應用程式](service-fabric-application-model.md) ，請安裝執行階段和通用 SDK。 您也可以安裝 Java 和 .NET Core 的選擇性 SDK。

## <a name="prerequisites"></a>必要條件

下列為支援開發的作業系統版本：

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>更新 APT 來源
若要透過 apt-get 命令列工具安裝 SDK 和相關聯的執行階段套件，您必須先更新 Advanced Packaging Tool (APT) 來源。

1. 開啟終端機。
2. 將 Service Fabric 存放庫新增至來源清單。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. 將 `dotnet` 存放庫新增至來源清單。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. 將新的 Gnu Privacy Guard (GnuPG 或 GPG) 金鑰新增至 APT keyring。

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. 將官方的 Docker GPG 金鑰新增至 APT keyring。

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. 設定 Docker 存放庫。

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. 根據新增的存放庫重新整理套件清單。

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk-for-local-cluster-setup"></a>針對本機叢集設定安裝和設定 SDK

在您已更新來源後，就可以安裝 SDK。 安裝 Service Fabric SDK 套件、確認安裝，並同意授權合約。

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   下列命令會自動接受 Service Fabric 套件的授權︰
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>設定本機叢集
  如果安裝順利，您應該能夠啟動本機叢集。

  1. 執行叢集安裝指令碼。

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. 開啟瀏覽器，前往 [Service Fabric Explorer](http://localhost:19080/Explorer)。 如果叢集已經啟動，您應會看見 Service Fabric Explorer 儀表板。

      ![Linux 上的 Service Fabric Explorer][sfx-linux]

  此時，您可以根據客體容器或來賓可執行檔，部署預先建置的 Service Fabric 應用程式套件或新的套件。 若要使用 Java 或 .NET Core SDK 建置新的服務，請遵循後面幾節所提供的選擇性設定步驟。


  > [!NOTE]
  > Linux 不支援獨立叢集。 預覽只支援一整體和 Azure Linux 多電腦叢集。
  >

## <a name="set-up-the-service-fabric-cli"></a>設定 Service Fabric CLI

[Service Fabric CLI](service-fabric-cli.md) 包含可供與 Service Fabric 實體 (包括叢集和應用程式) 進行互動的命令。 它是以 python, 為基礎，所以先確定您已安裝 python 和 pip，再繼續執行下列命令：

```bash
pip install sfctl
```

## <a name="install-and-set-up-the-generators-for-containers-and-guest-executables"></a>安裝並設定容器和來賓可執行檔的產生器
Service Fabric 提供的 Scaffolding 工具可協助您從終端機使用 Yeoman 範本產生器建立 Service Fabric 應用程式。 請遵循下列步驟來確保您有 Service Fabric yeoman 範本產生器可在電腦上運作。

1. 在電腦上安裝 nodejs 和 NPM

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. 在電腦上從 NPM 安裝 [Yeoman](http://yeoman.io/) 範本產生器

  ```bash
  sudo npm install -g yo
  ```
3. 從 NPM 安裝 Service Fabric Yeo 容器產生器和來賓可執行檔產生器

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

在您安裝上述產生器後，就能分別執行 `yo azuresfguest` 或 `yo azuresfcontainer`，以使用來賓可執行檔或容器服務建立應用程式。

## <a name="install-the-necessary-java-artifacts-optional-if-you-want-to-use-the-java-programming-models"></a>安裝所需的 Java 構件 (選擇性，如果您想要使用 Java 程式設計模型)

若要使用 Java 建置 Service Fabric 服務，確定您已隨著用來執行建置工作的 Gradle 一起安裝 JDK 1.8。 下列程式碼片段會隨著 Gradle 安裝 Open JDK 1.8。 系統會從 Maven 提取 Service Fabric Java 程式庫。

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>安裝 Eclipse Neon 外掛程式 (選擇性)

您可以從**適用於 Java 開發人員的 Eclipse 整合式開發環境 (IDE)** 安裝適用於 Service Fabric 的 Eclipse 外掛程式。 除了 Service Fabric Java 應用程式之外，您可以使用 Eclipse 來建立 Service Fabric 來賓可執行檔應用程式和容器應用程式。

1. 在 Eclipse 中，確定已安裝最新版 Eclipse Neon 和最新的 Buildship 版本 (1.0.17 或更新版本)。 您可以選取 [說明]  >  [安裝詳細資料]，檢查已安裝的元件版本。 您可以使用 [Eclipse Buildship：適用於 Gradle 的 Eclipse 外掛程式][buildship-update]的指示來更新 Buildship。

2. 若要安裝 Service Fabric 外掛程式，請選取 [說明]  >  [安裝新軟體]。

3. 在 [使用] 文字方塊中，輸入 **http://dl.microsoft.com/eclipse**。

4. 按一下 [新增] 。

    ![可用的軟體頁面][sf-eclipse-plugin]

5. 選取 ServiceFabric 外掛程式，然後按 [下一步]。

6. 完成安裝步驟，然後接受使用者授權合約。

如果您已安裝 Service Fabric Eclipse 外掛程式，請確定您擁有的是最新版本。 您可以藉由選取 [說明]  >  [安裝詳細資料]，然後在已安裝外掛程式清單中搜尋 Service Fabric 來檢查。如果有可用的較新版本，請選取 [更新]。

如需詳細資訊，請參閱[適用於 Eclipse Java 應用程式開發的 Service Fabric 外掛程式](service-fabric-get-started-eclipse.md)。


## <a name="install-the-net-core-sdk-optional-if-you-want-to-use-the-net-core-programming-models"></a>安裝 .NET Core SDK (選擇性，如果您想要使用 .NET Core 程式設計模型)
.NET Core SDK 提供了使用 .NET Core 建置 Service Fabric 服務所需的程式庫和範本。 執行下列命令來安裝 .NET Core SDK 套件 -

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

## <a name="update-the-sdk-and-runtime"></a>更新 SDK 和執行階段

若要更新為最新版的 SDK 和執行階段，請執行下列命令 (取消選取您不想要的 SDK)︰

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp
```
若要更新 Maven 提供的 Java SDK 二進位檔，您必須在 ``build.gradle`` 檔案中更新對應二進位檔的版本詳細資料，以指向最新版本。 若想知道您需要更新版本的確切位置，您可以[在此](https://github.com/Azure-Samples/service-fabric-java-getting-started)參考 Service Fabric 快速入門範例中的任何``build.gradle`` 檔案。

> [!NOTE]
> 更新套件可能會導致本機開發叢集停止執行。 請依照本頁上的指示，在升級之後重新啟動本機叢集。

## <a name="next-steps"></a>後續步驟

* [使用 Yeoman 在 Linux 上建立和部署第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
* [在 Linux 上使用適用於 Eclipse 的 Service Fabric 外掛程式建立和部署第一個 Service Fabric Java 應用程式](service-fabric-get-started-eclipse.md)
* [在 Linux 上建立第一個 CSharp 應用程式](service-fabric-create-your-first-linux-application-with-csharp.md)
* [在 OSX 上準備您的開發環境](service-fabric-get-started-mac.md)
* [使用 Service Fabric CLI 管理應用程式](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric Windows/Linux 的差異](service-fabric-linux-windows-differences.md)
* [開始使用 Service Fabric CLI](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

