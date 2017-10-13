---
title: "Azure Service Fabric 應用程式模型 | Microsoft Docs"
description: "如何在 Service Fabric 中建立模型和描述應用程式與服務。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: ryanwi
ms.openlocfilehash: e30482427b88eb3e58d39075c7f0734664b79aa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="model-an-application-in-service-fabric"></a>在 Service Fabric 中模型化應用程式
本文提供 Azure Service Fabric 應用程式模型的概觀，以及如何透過資訊清單檔案定義應用程式和服務。

## <a name="understand-the-application-model"></a>了解應用程式模型
應用程式是組成服務的集合，這些服務會執行特定函數。 服務會執行完整且獨立的函式，並且可獨立於其他服務啟動和執行。  服務是由程式碼、組態和資料所組成。 針對每個服務，程式碼由可執行檔二進位檔組成、組態由可在執行階段載入的服務設定組成，而資料由讓服務使用的任意靜態資料組成。 此階層應用程式模型中的每個元件都可以獨立建立版本和升級。

![Service Fabric 應用程式模型][appmodel-diagram]

應用程式類型是應用程式的分類，由服務類型的組合所組成。 服務類型是一項服務分類。 分類可以有不同的設定和組態，但是核心功能保持不變。 服務的執行個體是相同服務類型的不同服務組態變形。  

應用程式和服務的類別 (或「類型」) 是透過 XML 檔案 (應用程式資訊清單和服務資訊清單) 來說明。  資訊清單是應用程式可以從叢集的映像存放區具現化的範本。 ServiceManifest.xml 和 ApplicationManifest.xml 檔案的結構描述定義是和 Service Fabric SDK 及工具一起安裝在 C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd。

不同應用程式執行個體的程式碼會以個別程序的形式執行，即使是由相同的 Service Fabric 節點所裝載。 此外，每個應用程式執行個體的生命週期可以獨立進行管理 (例如，升級)。 下圖顯示應用程式類型如何由服務類型組成，依序分別為程式碼、組態和資料套件的組成。 為了簡化此圖，只會顯示 `ServiceType4` 的程式碼/組態/資料套件，但每個服務類型都包含這其中部分或所有的套件類型。

![Service Fabric 應用程式類型和服務類型][cluster-imagestore-apptypes]

兩個用來說明應用程式和服務的不同資訊清單檔案：服務資訊清單和應用程式資訊清單。 下列各節會詳細討論資訊清單。

叢集中可以有一或多個使用中的服務類型執行個體。 例如，可設定狀態的服務執行個體或複本，藉由複寫叢集中不同節點上複本之間的狀態達到高可靠性。 即使叢集中有一個節點失敗，複寫基本上會提供備援讓服務可供使用。 [分割服務](service-fabric-concepts-partitioning.md) 進一步在叢集中的節點之間分割其狀態 (並且存取該狀態的模式)。

下圖顯示應用程式和服務執行個體、分割和複本之間的關聯性。

![服務內的分割和複本][cluster-application-instances]

> [!TIP]
> 您可以使用 Service Fabric Explorer 工具，在叢集中檢視應用程式的配置，該工具可以在 http://&lt;yourclusteraddress&gt;:19080/Explorer 上取得。 如需詳細資訊，請參閱[使用 Service Fabric Explorer 視覺化叢集](service-fabric-visualizing-your-cluster.md)。
> 
> 

## <a name="describe-a-service"></a>描述服務
服務資訊清單以宣告方式定義服務類型和版本。 它會指定如服務類型的服務中繼資料、健康狀態屬性、負載平衡度量、服務二進位檔和組態檔。  換句話說，它會描述組成服務封裝的程式碼、組態和資料封裝，以支援一或多個服務類型。 以下是簡單的範例服務資訊清單：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```

**版本** 屬性為非結構化字串，不是由系統剖析。 版本屬性用於設定每個元件的版本以進行升級。

**ServiceTypes** 會宣告此資訊清單中 **CodePackages** 支援哪些服務類型。 當針對其中一種服務類型來具現化服務時，會藉由執行其進入點來啟動此資訊清單中宣告的所有程式碼封裝。 產生的處理程序預期在執行階段註冊支援的服務類型。 服務類型是在資訊清單層級而非程式碼套件層級宣告。 因此如果有多個程式碼封裝，每當系統尋找任何一個宣告的服務類型時，它們都會啟動。

**SetupEntryPoint** 是以與 Service Fabric 相同的認證執行的特殊權限進入點 (通常 *LocalSystem* 帳戶)，優先於任何其他進入點。 **EntryPoint** 指定的可執行檔通常是長時間執行的服務主機。 有個別設定的進入點，就不需要使用較高權限來長時間執行服務主機。 **EntryPoint** 指定的可執行檔是在 **SetupEntryPoint** 成功結束之後執行。 如果程序曾經終止或當機，則產生的程序會受到監視並重新啟動 (以 **SetupEntryPoint**再次開始)。  

使用 **SetupEntryPoint** 的一般案例，是當您在服務啟動之前執行可執行檔，或使用提高的權限來執行作業時。 例如：

* 設定及初始化服務可執行檔需要的環境變數。 這不限於透過 Service Fabric 程式設計模型撰寫的執行檔。 例如，npm.exe 部署 node.js 應用程式，需要設定某些環境變數。
* 透過安裝安全性憑證設定存取控制。

如需有關如何設定 **SetupEntryPoint** 的更多詳細資料，請參閱[設定服務安裝程式進入點的原則](service-fabric-application-runas-security.md)

**EnvironmentVariables** 提供針對此程式碼封裝設定的環境變數清單。 您可以在 `ApplicationManifest.xml` 中覆寫環境變數，以針對不同的服務執行個體提供不同的值。 

**DataPackage** 宣告 **Name** 屬性所命名的資料夾，包含由程序在執行階段使用的任意靜態資料。

**ConfigPackage** 宣告 **Name** 屬性所命名的資料夾，其中包含 *Settings.xml* 檔案。 設定檔案包含程序在執行階段讀回的使用者定義、成對的索引鍵/值設定等區段。 在升級期間，如果只有 **ConfigPackage** **版本**已變更，則不會重新啟動執行中程序。 相反地，回呼會通知程序組態設定已變更，因此它們可以動態方式重新載入。 以下是 *Settings.xml* 檔案的範例：

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

> [!NOTE]
> 服務資訊清單可以包含多個程式碼、組態和資料封裝。 每個皆可獨立建立版本。
> 
> 

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>描述應用程式
應用程式資訊清單以宣告方式描述應用程式類型和版本。 它指定服務組成中繼資料，例如穩定的名稱、分割配置、執行個體計數/複寫因數、安全性/隔離原則、安置限制、組態覆寫和組成服務類型。 也說明要放置應用程式的負載平衡網域。

因此，應用程式資訊清單描述應用程式層級的元素，並參考用來組成應用程式類型的一個或多個服務資訊清單。 以下是簡單的範例應用程式資訊清單：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
    <ConfigOverrides/>
    <EnvironmentOverrides CodePackageRef="MyCode"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
```

如同服務資訊清單， **版本** 屬性為非結構化字串，不是由系統剖析。 版本屬性也用於設定每個元件的版本以進行升級。

**ServiceManifestImport** 包含組成此應用程式類型之服務資訊清單的參考。 匯入的服務資訊清單會決定此應用程式類型中哪些服務類型有效。 在 ServiceManifestImport 內，您可覆寫 Settings.xml 中的組態值和 ServiceManifest.xml 檔案中的環境變數。 


**DefaultServices** 宣告服務執行個體，該執行個體會在每次應用程式針對此應用程式類型具現化時建立。 預設服務只是為了方便起見，在建立之後，其行為在每個方面就像正常的服務。 它們會與應用程式執行個體中的其他任何服務一起升級，也可以一起移除。

> [!NOTE]
> 應用程式資訊清單可以包含多個服務資訊清單匯入和預設服務。 每個服務資訊清單匯入都可以獨立建立版本。
> 
> 

若要了解如何針對個別環境維護不同的應用程式和服務參數，請參閱 [管理多個環境的應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>後續步驟
[封裝應用程式](service-fabric-package-apps.md)並讓它準備好進行部署。

[部署與移除應用程式][10]說明如何使用 PowerShell 來管理應用程式執行個體。

[管理多個環境的應用程式參數][11]說明如何為不同的應用程式執行個體設定參數和環境變數。

[設定應用程式的安全性原則][12]說明如何依據安全性原則執行服務來限制存取。

[應用程式主控模型][13]說明已部署之服務的複本 (或執行個體) 與服務主機處理序之間的關聯性。

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
[13]: service-fabric-hosting-model.md
