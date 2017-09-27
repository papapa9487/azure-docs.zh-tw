---
title: "建立 Azure Service Fabric Windows 容器應用程式 | Microsoft Docs"
description: "在 Azure Service Fabric 上建立第一個 Windows 容器應用程式。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: c4f8c94e23a165b22533ffd74e04c9a7310f2d22
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---

# <a name="deploy-a-service-fabric-windows-container-application-on-azure"></a>在 Azure 上部署 Service Fabric Windows 容器應用程式
Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理可調整和可信賴的微服務與容器。 

在 Service Fabric 叢集上的 Windows 容器中執行現有的應用程式，不需要變更您的應用程式。 本快速入門示範如何在 Service Fabric 應用程式中部署預先建立的 Docker 容器映像。 當您完成時，您會有執行中的 Windows Server 2016 Nano Server 和 IIS 容器。 本快速入門說明如何部署 Windows 容器，請閱讀[本快速入門](service-fabric-quickstart-containers-linux.md)以部署 Linux 容器。

![IIS 預設網頁][iis-default]

使用此快速入門，您可了解如何：
> [!div class="checklist"]
> * 封裝 Docker 映像容器
> * 設定通訊
> * 建置及封裝 Service Fabric 應用程式
> * 將容器應用程式部署至 Azure

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶 (您可以建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))。
* 執行下列項目的開發電腦︰
  * Visual Studio 2015 或 Visual Studio 2017。
  * [Service Fabric SDK 和工具](service-fabric-get-started.md)。

## <a name="package-a-docker-image-container-with-visual-studio"></a>使用 Visual Studio 封裝 Docker 映像容器
Service Fabric SDK 和工具會提供一個服務範本，協助您將容器部署到 Service Fabric 叢集。

以「系統管理員」身分啟動 Visual Studio。  選取 [檔案] > [新增] > [專案]。

選取 [Service Fabric 應用程式]，將它命名為 "MyFirstContainer"，然後按一下 [確定]。

從 [服務範本] 的清單中選取 [容器]。

在 [映像名稱] 中，輸入 "nanoserver/iis"、[Windows Server 2016 Nano Server 和 IIS 基底映像](https://hub.docker.com/r/nanoserver/iis/)。 

將您的服務命名為 "MyContainerService"，然後按一下 [確定]。

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>設定通訊和容器連接埠對主機連接埠的對應
此服務需要端點進行通訊。  您現在可將通訊協定、連接埠和類型新增至 ServiceManifest.xml 檔案中的 `Endpoint`。 在此快速入門中，容器化服務會接聽連接埠 80： 

```xml
<Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
```
提供 `UriScheme`，就會自動向「Service Fabric 命名」服務註冊容器端點以供搜尋。 本文結尾會提供完整的 ServiceManifest.xml 範例檔案。 

在 ApplicationManifest.xml 檔案的 `ContainerHostPolicies` 中指定 `PortBinding`，以設定容器連接埠與主機連接埠的對應。  在本快速入門中，`ContainerPort` 為 80，而 `EndpointRef` 是 "MyContainerServiceTypeEndpoint" (服務資訊清單中所定義的端點)。  通訊埠 80 上服務的連入要求會對應到容器上的連接埠 80。  

```xml
<ServiceManifestImport>
...
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

本文結尾會提供完整的 ApplicationManifest.xml 範例檔案。

## <a name="create-a-cluster"></a>建立叢集
若要將應用程式部署到 Azure 中的叢集，您可以選擇建立自己的叢集，或使用合作對象叢集。

合作對象的叢集是免費的限時 Service Fabric 叢集，裝載於 Azure 上，並且由任何人都可以部署應用程式並了解平台的 Service Fabric 小組執行。 若要存取合作對象叢集，請[遵循指示](http://aka.ms/tryservicefabric)。  

如需建立您自己叢集的資訊，請參閱[在 Azure 上建立您的第一個 Service Fabric 叢集](service-fabric-get-started-azure-cluster.md)。

記下您在下面步驟中使用的連線端點。  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>使用 Visual Studio 將應用程式部署至 Azure
應用程式備妥後，即可直接從 Visual Studio 將其部署到叢集。

以滑鼠右鍵按一下 [方案總管] 中的 **MyFirstContainer**，並選擇 [發佈]。 [發行] 對話方塊隨即出現。

![[發行] 對話方塊](./media/service-fabric-quickstart-dotnet/publish-app.png)

在 [連接端點] 欄位中鍵入叢集的連接端點，然後按一下 [發行]。 註冊合作對象叢集時，會在瀏覽器中會提供連線端點，例如 `winh1x87d1d.westus.cloudapp.azure.com:19000`。

開啟瀏覽器並瀏覽至 http://winh1x87d1d.westus.cloudapp.azure.com:80 。 您應會看到 IIS 預設網頁：![IIS 預設網頁][iis-default]

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>完整範例 Service Fabric 應用程式和服務資訊清單
以下是本快速入門中使用的完整服務和應用程式資訊清單。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>nanoserver/iis</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>後續步驟
在此快速入門中，您已了解如何：
> [!div class="checklist"]
> * 封裝 Docker 映像容器
> * 設定通訊
> * 建置及封裝 Service Fabric 應用程式
> * 將容器應用程式部署至 Azure

* 深入了解如何[在 Service Fabric 上執行容器](service-fabric-containers-overview.md)。
* 閱讀[在容器中部署 .NET 應用程式](service-fabric-host-app-in-a-container.md)教學課程。
* 深入了解 Service Fabric [應用程式生命週期](service-fabric-application-lifecycle.md)。
* 請查看 GitHub 上的[ Service Fabric 容器程式碼範例](https://github.com/Azure-Samples/service-fabric-dotnet-containers)。

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png

