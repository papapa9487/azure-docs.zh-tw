---
title: "在 Linux 上建立 Azure Service Fabric 容器應用程式 | Microsoft Docs"
description: "在 Azure Service Fabric 上建立第一個 Linux 容器應用程式。  使用您的應用程式建置 Docker 映像、將映像推送到容器登錄，建置和部署 Service Fabric 容器應用程式。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>在 Azure 上部署 Service Fabric Linux 容器應用程式
Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理可調整和可信賴的微服務與容器。 

在 Service Fabric 叢集上的 Linux 容器中執行現有的應用程式，無需變更您的應用程式。 本快速入門示範如何在 Service Fabric 應用程式中部署預先建立的 Docker 容器映像。 當您完成時，您會有執行中的 nginx 容器。  本快速入門說明如何部署 Linux 容器，請閱讀[本快速入門](service-fabric-quickstart-containers.md)以部署 Windows 容器。

![Nginx][nginx]

在此快速入門中，您可了解如何：
> [!div class="checklist"]
> * 封裝 Docker 映像容器
> * 設定通訊
> * 建置及封裝 Service Fabric 應用程式
> * 將容器應用程式部署至 Azure

## <a name="prerequisites"></a>必要條件
安裝 [Service Fabric SDK、Service Fabric CLI 和 Service Fabric yeoman 範本產生器](service-fabric-get-started-linux.md)。
  
## <a name="package-a-docker-image-container-with-yeoman"></a>使用 Yeoman 封裝 Docker 映像容器
適用於 Linux 的 Service Fabric SDK 包含 [Yeoman](http://yeoman.io/) 產生器，可讓您輕鬆建立應用程式並新增容器映像。 

若要建立 Service Fabric 容器應用程式，請開啟終端機視窗並執行 `yo azuresfcontainer`。  

將應用程式命名為 "MyFirstContainer"，並將應用程式服務命名為 "MyContainerService"。

提供容器映像名稱 "nginx:latest" (Docker 中書上的 [nginx 容器映像](https://hub.docker.com/r/_/nginx/))。 

此映像已定義工作負載進入點，因此您必須明確地指定輸入命令。 

指定執行個體計數為 "1"。

![容器的 Service Fabric Yeoman 產生器][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>設定通訊和容器連接埠對主機連接埠的對應
設定 HTTP 端點，讓用戶端可以與您的服務通訊。  開啟 ./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml 檔案並在 **ServiceManifest** 元素中宣告端點資源。  新增通訊協定、連接埠和名稱。 在此快速入門中，服務會接聽連接埠 80： 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
提供 `UriScheme`，就會自動向「Service Fabric 命名」服務註冊容器端點以供搜尋。 本文結尾會提供完整的 ServiceManifest.xml 範例檔案。 

在 ApplicationManifest.xml 檔案的 `ContainerHostPolicies` 中使用 `PortBinding` 原則，將容器連接埠對應至服務 `Endpoint`。  在本快速入門中，`ContainerPort` 為 80 (容器會公開連接埠 80)，而 `EndpointRef` 為 "myserviceTypeEndpoint" (先前在服務資訊清單中定義的端點)。  通訊埠 80 上服務的連入要求會對應到容器上的連接埠 80。  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>建置及封裝 Service Fabric 應用程式
Service Fabric Yeoman 範本包含 [Gradle](https://gradle.org/) 的建置指令碼，可用來從終端機建置應用程式。 儲存您的所有變更。  若要建置和封裝應用程式，請執行下列指令碼：

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>建立叢集
若要將應用程式部署到 Azure 中的叢集，您可以選擇建立自己的叢集，或使用合作對象叢集。

合作對象的叢集是免費的限時 Service Fabric 叢集，裝載於 Azure 上，並且由任何人都可以部署應用程式並了解平台的 Service Fabric 小組執行。 若要存取合作對象叢集，請[遵循指示](http://aka.ms/tryservicefabric)。  

如需建立您自己叢集的資訊，請參閱[在 Azure 上建立您的第一個 Service Fabric 叢集](service-fabric-get-started-azure-cluster.md)。

記下您在下面步驟中使用的連線端點。

## <a name="deploy-the-application-to-azure"></a>將應用程式部署至 Azure
建置應用程式後，可以使用 Service Fabric CLI 將它部署到 Azure 叢集。

連線到 Azure 中的 Service Fabric 叢集。

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

使用範本中所提供的安裝指令碼，將應用程式套件複製到叢集的映像存放區、註冊應用程式類型，以及建立應用程式的執行個體。

```bash
./install.sh
```

開啟瀏覽器並瀏覽至 Service Fabric Explorer (http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer)。 展開 [應用程式] 節點，請注意，您的應用程式類型現在有一個項目，而另一個則是該類型的第一個執行個體。

![Service Fabric Explorer][sfx]

連線到執行中的容器。  開啟 Web 瀏覽器並指向連接埠 80 上傳回的 IP 位址，例如 "lnxt10vkfz6.westus.cloudapp.azure.com:80"。 您應會看到瀏覽器中顯示 nginx 歡迎使用頁面。

![Nginx][nginx]

## <a name="clean-up"></a>清除
使用範本中提供的解除安裝指令碼，刪除叢集中的應用程式執行個體並取消註冊應用程式類型。

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>完整範例 Service Fabric 應用程式和服務資訊清單
以下是本快速入門中使用的完整服務和應用程式資訊清單。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>後續步驟
在此快速入門中，您可了解如何：
> [!div class="checklist"]
> * 封裝 Docker 映像容器
> * 設定通訊
> * 建置及封裝 Service Fabric 應用程式
> * 將容器應用程式部署至 Azure

* 深入了解如何[在 Service Fabric 上執行容器](service-fabric-containers-overview.md)。
* 閱讀[在容器中部署 .NET 應用程式](service-fabric-host-app-in-a-container.md)教學課程。
* 深入了解 Service Fabric [應用程式生命週期](service-fabric-application-lifecycle.md)。
* 請查看 GitHub 上的[ Service Fabric 容器程式碼範例](https://github.com/Azure-Samples/service-fabric-dotnet-containers)。

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

