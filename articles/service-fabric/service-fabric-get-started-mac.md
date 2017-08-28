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
ms.date: 08/21/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 8b4fc0ab9034263418cac42ced203035e0a8fcad
ms.contentlocale: zh-tw
ms.lasthandoff: 08/23/2017

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
Service Fabric 不會在 OS X 上以原生方式執行。若要執行本機 Service Fabric 叢集，我們提供使用 Vagrant 和 VirtualBox 的預先設定 Ubuntu 虛擬機器。 開始之前，您需要：

* [Vagrant (v1.8.4 或更新版本)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> 您需要使用互相支援的 Vagrant 和 VirtualBox 版本。 Vagrant 在不支援的 VirtualBox 版本上的行為可能不穩定。
>

## <a name="create-the-local-vm"></a>建立本機 VM
若要建立包含 5 個節點 Service Fabric 叢集的本機 VM，請執行下列步驟︰

1. 複製 `Vagrantfile` 存放庫

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    此步驟會帶來包含 VM 組態的 `Vagrantfile` 檔案，以及下載 VM 的來源位置。

2. 瀏覽至儲存機制的本機複本

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (選擇性) 修改預設 VM 設定

    根據預設，本機 VM 的設定如下所示︰

   * 配置 3 GB 的記憶體
   * 在 IP 192.168.50.50 設定且能夠傳遞 Mac 主機流量的私用主機網路

     您可以變更上述任何一項設定或將其他組態新增至 `Vagrantfile` 中的 VM。 如需設定選項的完整清單，請參閱 [Vagrant 文件](http://www.vagrantup.com/docs) 。
4. 建立 VM

    ```bash
    vagrant up
    ```

   這個步驟可下載預先設定的 VM 映像、讓它在本機開機，然後在其中設定一個本機 Service Fabric 叢集。 您預計需花幾分鐘的時間。 如果安裝程式順利完成，您會在輸出中看到一則訊息，表示叢集正在啟動中。

    ![在 VM 佈建後啟動的叢集安裝程式][cluster-setup-script]

    >[!TIP]
    > 如果 VM 下載花費很長的時間，您可以使用 wget 或 curl 來下載它，或者透過瀏覽器瀏覽至 `Vagrantfile` 檔案中 **config.vm.box_url** 所指定的連結。 在本機下載之後，編輯 `Vagrantfile` 以指向已下載映像的本機路徑。 例如，如果您將映像下載至 /home/users/test/azureservicefabric.tp8.box，則將 **config.vm.box_url** 設定為該路徑。
    >

5. 瀏覽至位於 http://192.168.50.50:19080/Explorer 的 Service Fabric Explorer (假設您保留預設的私人網路 IP)，測試是否已正確設定叢集。

    ![從主機 Mac 檢視的 Service Fabric Explorer][sfx-mac]


## <a name="create-application-on-mac-using-yeoman"></a>在 Mac 上使用 Yeoman 建立應用程式
Service Fabric 提供的 Scaffolding 工具可協助您從終端機使用 Yeoman 範本產生器建立 Service Fabric 應用程式。 請遵循下列步驟來確保您有 Service Fabric yeoman 範本產生器可在電腦上運作。

1. 您必須在 Mac 上安裝 Node.js 和 NPM。 若未這麼做，您可以使用 Homebrew 安裝 Node.js 和 NPM。 若要檢查 Mac 上安裝的 Node.js 和 NPM 版本，您可以使用 ``-v`` 選項。

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. 在電腦上從 NPM 安裝 [Yeoman](http://yeoman.io/) 範本產生器

  ```bash
  npm install -g yo
  ```
3. 遵循快速入門[文件](service-fabric-get-started-linux.md)中的步驟，安裝您要使用的 Yeoman 產生器。 若要使用 Yeoman 建立 Service Fabric 應用程式，請遵循下列步驟 -

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. 若要在 Mac 上建置 Service Fabric Java 應用程式，您必須在電腦上安裝 JDK 1.8 和 Gradle。


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>安裝適用於 Eclipse Neon 的 Service Fabric 外掛程式

Service Fabric 為**適用於 Java IDE 的 Eclipse Neon** 提供了外掛程式，可簡化建立、建置和部署 Java 服務的程序。 您可以遵循這個有關安裝或更新 Service Fabric Eclipse 外掛程式的一般[文件](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)中所述的安裝步驟。

>[!TIP]
> 根據預設，我們支援如所產生應用程式之 ``Local.json`` 中的 ``Vagrantfile`` 所提交之預設 IP。 如果您進行變更並使用不同的 IP 來部署 Vagrant，請在您應用程式的 ``Local.json`` 中更新對應的 IP。

## <a name="next-steps"></a>後續步驟
<!-- Links -->
* [使用 Yeoman 在 Linux 上建立和部署第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
* [在 Linux 上使用適用於 Eclipse 的 Service Fabric 外掛程式建立和部署第一個 Service Fabric Java 應用程式](service-fabric-get-started-eclipse.md)
* [在 Azure 入口網站中建立 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)
* [使用 Azure Resource Manager 建立 Service Fabric 叢集](service-fabric-cluster-creation-via-arm.md)
* [了解 Service Fabric 應用程式模型](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

