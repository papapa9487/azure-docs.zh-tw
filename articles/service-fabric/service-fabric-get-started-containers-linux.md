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
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2017
ms.author: ryanwi
ms.openlocfilehash: 3649cc2800e774f8dca1b88a1704744b4663a68d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>在 Linux 建立第一個 Service Fabric 容器應用程式
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

在 Service Fabric 叢集上的 Linux 容器中執行現有的應用程式，無需變更您的應用程式。 本文會逐步引導您建立包含 Python [Flask](http://flask.pocoo.org/) Web 應用程式的 Docker 映像，並將它部署到 Service Fabric 叢集。  您也將透過 [Azure Container Registry](/azure/container-registry/) 共用容器化應用程式。  本文假設您對 Docker 有基本認識。 您可藉由閱讀 [Docker 概觀](https://docs.docker.com/engine/understanding-docker/)來了解 Docker。

## <a name="prerequisites"></a>必要條件
* 執行下列項目的開發電腦︰
  * [Service Fabric SDK 和工具](service-fabric-get-started-linux.md)。
  * [Docker CE for Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Azure Container Registry 中的登錄 - 在 Azure 訂用帳戶中[建立容器登錄](../container-registry/container-registry-get-started-portal.md)。 

## <a name="define-the-docker-container"></a>定義 Docker 容器
根據位於 Docker 中樞的 [Python 映像](https://hub.docker.com/_/python/)建立映像。 

在 Dockerfile 中定義您的 Docker 容器。 Dockerfile 包含下列相關指示：設定您容器內的環境、載入您要執行的應用程式，以及對應連接埠。 Dockerfile 是 `docker build` 命令的輸入，該命令可建立映像。 

建立空的目錄並建立 Dockerfile 檔案 (沒有副檔名)。 將下列內容新增至 Dockerfile 並儲存變更：

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

如需詳細資訊，請參閱 [Dockerfile 參考](https://docs.docker.com/engine/reference/builder/)。

## <a name="create-a-simple-web-application"></a>建立簡單 Web 應用程式
建立 Flask Web 應用程式，其會在連接埠 80 上接聽並傳回 "Hello World!"。  在相同的目錄中，建立 requirements.txt 檔案。  新增下列內容並儲存變更：
```
Flask
```

此外，建立 app.py 檔案並新增下列內容：

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>建立映像
執行 `docker build` 命令來建立可執行 Web 應用程式的映像。 開啟 PowerShell 視窗並瀏覽至 *c:\temp\helloworldapp*。 執行以下命令：

```bash
docker build -t helloworldapp .
```

此命令會使用 Dockerfile 中的指示建立新映像，將此映像命名 (-t 標記) 為 "helloworldapp"。 建置映像時，會從 Docker 中樞向下提取基底映像，並根據基底映像建立可新增應用程式的新映像。  

建置命令完成後，執行 `docker images` 命令以查看新映像的資訊︰

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>在本機執行應用程式
先確認您的容器化應用程式在本機執行，再將它推送至容器登錄。  

執行此應用程式，並將您電腦的通訊埠 4000 對應至容器的已公開連接埠 80：

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

name - 提供執行中容器的名稱 (而不是容器識別碼)。

連線到執行中的容器。  開啟 Web 瀏覽器並指向連接埠 4000 上傳回的 IP 位址，例如 http://localhost:4000 。 您應該會看到 "Hello World!" 標題 顯示在瀏覽器中。

![Hello World!][hello-world]

若要停止您的容器，請執行︰

```bash
docker stop my-web-site
```

從您的開發電腦刪除容器：

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>將映像推送至容器登錄
確認應用程式在 Docker 中執行後，將映像推送至 Azure Container Registry 中您的登錄。

使用您的[登錄認證](../container-registry/container-registry-authentication.md)執行 `docker login` 登入容器登錄庫。

下列範例會傳遞 Azure Active Directory [service principal](../active-directory/active-directory-application-objects.md) 的識別碼和密碼。 例如，您可能基於自動化案例已指派服務主體到您的登錄庫。  或者，您可以使用登錄使用者名稱和密碼進行登入。

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

下列命令會建立映像的標籤或別名，包含登錄的完整路徑。 這個範例會指定 `samples` 命名空間中的映像，以避免登錄根目錄雜亂。

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

將映像推送至容器登錄：

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>使用 Yeoman 封裝 Docker 映像
適用於 Linux 的 Service Fabric SDK 包含 [Yeoman](http://yeoman.io/) 產生器，可讓您輕鬆建立應用程式並新增容器映像。 讓我們使用 Yeoman 來建立具有單一 Docker 容器，名為 *SimpleContainerApp* 的應用程式。

若要建立 Service Fabric 容器應用程式，請開啟終端機視窗並執行 `yo azuresfcontainer`。  

將應用程式命名 (例如，"mycontainer")，並將應用程式服務命名 (例如，"myservice")。

針對映像名稱，在容器登錄中提供容器映像的 URL (例如，"myregistry.azurecr.io/samples/helloworldapp")。 

因為此映像已定義工作負載進入點，您不需要明確指定輸入命令 (命令會在容器內執行，這會讓容器在啟動後繼續執行)。 

指定執行個體計數為 "1"。

![容器的 Service Fabric Yeoman 產生器][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>設定連接埠對應和容器存放庫驗證
您的容器化服務需要端點進行通訊。  現在將通訊協定、連接埠和類型新增至 ServiceManifest.xml 檔案中 'Resources' 標籤之下的 `Endpoint`。 在本文中，容器化服務會接聽連接埠 4000： 

```xml

<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
 ```
 
提供 `UriScheme`，就會自動向「Service Fabric 命名」服務註冊容器端點以供搜尋。 本文結尾會提供完整的 ServiceManifest.xml 範例檔案。 

在 ApplicationManifest.xml 檔案的 `ContainerHostPolicies` 中指定 `PortBinding`，以設定容器連接埠與主機連接埠的對應。  在本文中，`ContainerPort` 為 80 (如 Dockerfile 所指定，容器會公開連接埠 80)，而 `EndpointRef` 為 "myServiceTypeEndpoint" (服務資訊清單中定義的端點)。  通訊埠 4000 上服務的連入要求會對應到容器上的連接埠 80。  如果您的容器需要向私人存放庫進行驗證，則新增 `RepositoryCredentials`。  在本文中，新增 myregistry.azurecr.io 容器登錄的帳戶名稱和密碼。 確保該原則已新增在對應至正確服務套件的 'ServiceManifestImport' 標籤之下。

```xml
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0" />
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
   </ServiceManifestImport>
``` 

## <a name="build-and-package-the-service-fabric-application"></a>建置及封裝 Service Fabric 應用程式
Service Fabric Yeoman 範本包含 [Gradle](https://gradle.org/) 的建置指令碼，可用來從終端機建置應用程式。 若要建置和封裝應用程式，請執行下列指令碼：

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>部署應用程式
建置應用程式後，可以使用 Service Fabric CLI 將它部署到本機叢集。

連接到本機 Service Fabric 叢集。

```bash
sfctl cluster select --endpoint http://localhost:19080
```

使用範本中所提供的安裝指令碼，將應用程式套件複製到叢集的映像存放區、註冊應用程式類型，以及建立應用程式的執行個體。

```bash
./install.sh
```

開啟瀏覽器並瀏覽至位於 http://localhost:19080/Explorer 的 Service Fabric Explorer (如果在 Mac OS X 上使用 Vagrant，請以 VM 的私人 IP 取代 localhost)。 展開 [應用程式] 節點，請注意，您的應用程式類型現在有一個項目，而另一個則是該類型的第一個執行個體。

連線到執行中的容器。  開啟 Web 瀏覽器並指向連接埠 4000 上傳回的 IP 位址，例如 http://localhost:4000 。 您應該會看到 "Hello World!" 標題 顯示在瀏覽器中。

![Hello World!][hello-world]

## <a name="clean-up"></a>清除
使用範本中提供的解除安裝指令碼，刪除本機開發叢集中的應用程式執行個體並取消註冊應用程式類型。

```bash
./uninstall.sh
```

將映像推送至容器登錄之後，您可以從開發電腦刪除本機映像︰

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>完整範例 Service Fabric 應用程式和服務資訊清單
以下是本文中使用的完整服務和應用程式資訊清單。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>將更多服務新增至現有的應用程式

若要將其他容器服務新增至已使用 yeoman 建立的應用程式，請執行下列步驟︰

1. 將目錄變更為現有應用程式的根目錄。  例如，如果 `MyApplication` 是 Yeoman 所建立的應用程式，則為 `cd ~/YeomanSamples/MyApplication`。
2. 執行 `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>在容器被迫終止前設定時間間隔

您可以在啟動服務刪除 (或移至另一個節點) 之後，設定執行階段在容器移除前所要等候的間隔時間。 設定時間間隔可將 `docker stop <time in seconds>` 命令傳送至容器。   如需更多詳細資訊，請參閱 [docker stop](https://docs.docker.com/engine/reference/commandline/stop/)。 所要等候的時間間隔指定於 `Hosting` 區段之下。 下列叢集資訊清單程式碼片段示範如何設定等候間隔：

```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "ContainerDeactivationTimeout": "10",
          ...
          }
        ]
}
```
預設時間間隔會設定為 10 秒。 因為此組態是動態的，所以叢集的僅限組態升級會更新逾時。 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>將執行階段設定為移除未使用的容器映像

您可以將 Service Fabric 叢集設定為從節點移除未使用的容器映像。 如果節點上存在太多容器映像，此組態允許重新擷取磁碟空間。  若要啟用此功能，請更新叢集資訊清單中的 `Hosting` 區段，如下列程式碼片段所示： 


```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "PruneContainerImages": “True”,
            "ContainerImagesToSkip": "microsoft/windowsservercore|microsoft/nanoserver|…",
          ...
          }
        ]
} 
```

對於不應刪除的映像，您可以在 `ContainerImagesToSkip` 參數之下加以指定。 


## <a name="next-steps"></a>後續步驟
* 深入了解如何[在 Service Fabric 上執行容器](service-fabric-containers-overview.md)。
* 閱讀[在容器中部署 .NET 應用程式](service-fabric-host-app-in-a-container.md)教學課程。
* 深入了解 Service Fabric [應用程式生命週期](service-fabric-application-lifecycle.md)。
* 請查看 GitHub 上的[ Service Fabric 容器程式碼範例](https://github.com/Azure-Samples/service-fabric-containers)。

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png
