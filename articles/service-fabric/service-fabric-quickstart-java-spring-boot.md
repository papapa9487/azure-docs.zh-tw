---
title: "將 Spring Boot 應用程式部署到 Azure Service Fabric | Microsoft Docs"
description: "使用 Spring Boot Getting Started 部署適用於 Azure Service Fabric 的 Spring Boot 應用程式。"
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: d34862d96744e038d7c1890f703ead79c416ddfa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-a-spring-boot-application"></a>部署 Spring Boot 應用程式
Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理微服務與容器。 

本快速入門示範如何將 Spring Boot 應用程式部署到 Service Fabric。 本快速入門使用 Spring 網站上的 [Getting Started](https://spring.io/guides/gs/spring-boot/) 範例。 本快速入門使用熟悉的命令列工具，引導您將 Spring Boot 範例部署為 Service Fabric 應用程式。 完成後，您就可以在 Service Fabric 上使用 Spring Boot Getting Started 範例。 

![應用程式螢幕擷取畫面](./media/service-fabric-quickstart-java-spring-boot/springbootsf.png)

在此快速入門中，您可了解如何：

> [!div class="checklist"]
> * 將 Spring Boot 應用程式部署到 Service Fabric
> * 將應用程式部署到本機叢集 
> * 將應用程式部署到 Azure 中的叢集
> * 跨多個節點相應放大應用程式
> * 無法使用時，執行服務的容錯移轉

## <a name="prerequisites"></a>必要條件
若要完成本快速入門：
1. [安裝 Service Fabric SDK 和 Service Fabric 命令列介面 (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [安裝 Git](https://git-scm.com/)
3. [安裝 Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [設定 Java 環境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>下載範例
在命令視窗中執行下列命令，將 Spring Boot Getting Started 範例應用程式複製到本機電腦。
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>封裝 Spring Boot 應用程式 
1. 在剛複製的 `gs-spring-boot` 目錄內部，執行 `yo azuresfguest` 命令。 

2. 針對每個提示，輸入下列詳細資料。 

    ![Yeoman 項目](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. 在 `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` 資料夾中，建立名為 `entryPoint.sh` 的檔案。 將下列項目新增至檔案。 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

在這個階段，您已經針對可以部署到 Service Fabric 的 Spring Boot Getting Started 範例，建立 Service Fabric 應用程式。

## <a name="run-the-application-locally"></a>在本機執行應用程式
1. 執行下列命令來啟動本機叢集：

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    啟動本機叢集需要一些時間。 若要確認該叢集完全啟動，請存取位於 **http://localhost:19080** 的 Service Fabric Explorer。 五個狀況良好的節點表示本機叢集已啟動並執行。 
    
    ![本機叢集狀況良好](./media/service-fabric-quickstart-java/localclusterup.png)

2. 瀏覽到 `gs-spring-boot/SpringServiceFabric` 資料夾。
3. 執行下列命令，以連接到本機叢集。 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. 執行 `install.sh` 指令碼。 

    ```bash
    ./install.sh
    ```

5. 開啟您最愛的網頁瀏覽器，並存取 **http://localhost:8080** 來存取應用程式。 

    ![本機應用程式前端](./media/service-fabric-quickstart-java-spring-boot/springbootsf.png)
    
您現在可以存取已部署至 Service Fabric 叢集的 Spring Boot 應用程式。  

## <a name="deploy-the-application-to-azure"></a>將應用程式部署至 Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>設定 Azure Service Fabric 叢集
若要將應用程式部署到 Azure 中的叢集，請建立您自己的叢集。

合作對象叢集是 Azure 上裝載的免費、限時 Service Fabric 叢集。 這類叢集是由任何人皆可部署應用程式並了解平台的 Service Fabric 小組所執行。 若要存取合作對象叢集，請[遵循指示](http://aka.ms/tryservicefabric)。 

如需建立您自己叢集的資訊，請參閱[在 Azure 上建立您的 Service Fabric 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)。

> [!Note]
> Spring Boot 服務設定為在連接埠 8080 上接聽傳入流量。 請確定您的叢集中已開啟該連接埠。 如果您使用合作對象叢集，此連接埠已開啟。
>

### <a name="deploy-the-application-using-cli"></a>使用 CLI 部署應用程式
既然應用程式和叢集已經就緒，您可以將其直接從命令列部署到叢集。

1. 瀏覽到 `gs-spring-boot/SpringServiceFabric` 資料夾。
2. 執行下列命令，以連接到 Azure 叢集。 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    如果叢集使用自我簽署的憑證保護，則您執行的命令將會是： 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. 執行 `install.sh` 指令碼。 

    ```bash
    ./install.sh
    ```

4. 開啟您最愛的網頁瀏覽器，並存取 **http://\<ConnectionIPOrUrl>:8080** 來存取應用程式。 

    ![本機應用程式前端](./media/service-fabric-quickstart-java-spring-boot/springsfazure.png)
    
您現在可以存取已部署至 Service Fabric 叢集的 Spring Boot 應用程式。  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>調整叢集中的應用程式和服務
您可以在整個叢集內調整服務，以符合服務上的負載變更。 您可以藉由變更叢集中執行的執行個體數目來調整服務。 您有多種方法來調整您的服務，您可以使用 Service Fabric CLI (sfctl) 中的指令碼或命令。 在此範例中，我們使用 Service Fabric Explorer。

Service Fabric Explorer 會在所有 Service Fabric 叢集中執行，並可從瀏覽器瀏覽至叢集 HTTP 管理連接埠 (19080) 來存取，例如 `http://demolinuxsecure.westus.cloudapp.azure.com:19080`。

若要調整 Web 前端服務，請執行下列步驟：

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `http://demolinuxsecure.westus.cloudapp.azure.com:19080`。
2. 按一下樹狀檢視中 **fabric:/SpringServiceFabric/SpringGettingStarted** 節點旁邊的省略符號 (三個點)，然後選擇 [調整服務]。

    ![Service Fabric Explorer 的 [調整服務]](./media/service-fabric-quickstart-java-spring-boot/springbootsfhowtoscale.png)

    您現在可以選擇調整服務的執行個體數目。

3. 將數字變更為 **5**，然後按一下 [調整服務]。

    使用命令列調整服務的替代方式如下所示。

    ```bash 
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 5 --stateless 
    ``` 

4. 按一下樹狀檢視中的 **fabric:/SpringServiceFabric/SpringGettingStarted** 節點，然後展開資料分割節點 (以 GUID 表示)。

    ![Service Fabric Explorer 調整服務完成](./media/service-fabric-quickstart-java-spring-boot/springsfscaled.png)

    您現在可以看到此服務具有五個執行個體，而且在樹狀檢視中，您會看到執行個體執行所在的節點。

藉由這項簡單的管理工作，我們會增加 Spring 服務可用來處理使用者負載的資源。 請務必了解，您不需要多個服務執行個體，就能夠可靠地執行。 如果服務失敗，Service Fabric 可確保新的服務執行個體在叢集中執行。

## <a name="failover-services-in-a-cluster"></a>叢集中的容錯移轉服務 
若要示範服務容錯移轉，我們可以使用 Service Fabric Explorer 來模擬節點重新啟動。 請確定您的服務只有 1 個執行個體正在執行。 

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `http://demolinuxsecure.westus.cloudapp.azure.com:19080`。
2. 按一下執行您服務執行個體之節點旁邊的省略符號 (三個點)，然後重新啟動節點。 

    ![Service Fabric Explorer 重新啟動節點](./media/service-fabric-quickstart-java-spring-boot/springbootsfrestart.png)
3. 您服務的執行個體現在將會移至其他節點，而且您的應用程式將不會有任何停機時間。 

    ![Service Fabric Explorer 重新啟動節點成功](./media/service-fabric-quickstart-java-spring-boot/springbootsfrestartsucceed.png)

## <a name="next-steps"></a>後續步驟
在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 將 Spring Boot 應用程式部署到 Service Fabric
> * 將應用程式部署到本機叢集 
> * 將應用程式部署到 Azure 中的叢集
> * 跨多個節點相應放大應用程式
> * 無法使用時，執行服務的容錯移轉

* 深入了解[使用 Service Fabric 程式設計模型建置 Java 微服務](service-fabric-quickstart-java-reliable-services.md)
* 深入了解[使用 Jenkins 設定連續整合及部署](service-fabric-cicd-your-linux-applications-with-jenkins.md)
* 簽出其他 [Java 範例](https://github.com/Azure-Samples/service-fabric-java-getting-started)