---
title: "Azure Service Fabric 主控模型 | Microsoft Docs"
description: "說明已部署之 Servic Fabric 的複本 (或執行個體) 與服務主機處理序之間的關聯性。"
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: ca7092a06a9ffce8383ca8bc9f70ce312cdf9de4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="service-fabric-hosting-model"></a>Service Fabric 主控模型
本文提供 Service Fabric 所提供之應用程式主控模型的概觀，並說明「共用處理程序」與「專屬處理序」模型之間的差異。 本文說明已部署之應用程式在 Service Fabric 節點上看起來的樣子，以及服務的複本 (或執行個體) 與服務主機處理序之間的關聯性。

在進一步進行之前，請確定您熟悉 [Service Fabric 應用程式模型][a1]，並了解它們之間的各種概念與關聯性。 

> [!NOTE]
> 在本文中，為了簡單起見，除非明確指出，否則：
>
> - 所有用到「複本」一字的地方都是指具狀態服務的複本或靜態服務執行個體的複本。
> - *CodePackage* 會被視為等同於註冊 *ServiceType* 並裝載該 *ServiceType* 之服務複本的 *ServiceHost* 處理序。
>

為了了解主控模型，我們將逐步解說範例。 假設我們有一個 *ApplicationType* 'MyAppType'，其 *ServiceType* 為 'MyServiceType' 並且是由 *ServicePackage* 'MyServicePackage' 所提供，而此 ServicePackage 具有一個會在執行時註冊 *ServiceType* 'MyServiceType' 的 *CodePackage* 'MyCodePackage'。

假設我們有一個含有 3 個節點的叢集，並且建立一個類型為 'MyAppType' 的「應用程式」**fabric:/App1**。 在這個「應用程式」**fabric:/App1** 中，我們會建立一個類型為 'MyServiceType' 的服務 **fabric:/App1/ServiceA**，此服務具有 2 個分割區 (即 **P1** & **P2**) 且每一分割區有 3 個複本。 下圖顯示此應用程式最後部署在節點上時的檢視。

<center>
![已部署之應用程式的節點檢視][node-view-one]
</center>

Service Fabric 啟用了 'MyServicePackage'，而 'MyServicePackage' 則啟動了裝載來自兩個分割區 (即 **P1** & **P2**) 之複本的 'MyCodePackage'。 請注意，此叢集內的所有節點都具有相同檢視，因為我們所選擇的每一分割區複本數目等於此叢集內的節點數目。 讓我們在應用程式 **fabric:/App1** 中建立另一個服務 **fabric:/App1/ServiceB**，此服務具有 1 個分割區 (即 **P3**) 且每一分割區有 3 個複本。 下圖顯示節點上的新檢視：

<center>
![已部署之應用程式的節點檢視][node-view-two]
</center>

如我們所見，Service Fabric 在現有的 'MyServicePackage' 啟用項中，放置了服務 **fabric:/App1/ServiceB** 之分割區 **P3** 的新複本。 現在，讓我們建立另一個 'MyAppType' 類型的「應用程式」**fabric:/App2**，並在 **fabric:/App2** 中建立服務 **fabric:/App2/ServiceA**，此服務具有 2 個分割區 (即 **P4** & **P5**) 且每一分割區有 3 個複本。 下圖顯示新的節點檢視：

<center>
![已部署之應用程式的節點檢視][node-view-three]
</center>

這次 Service Fabric 啟用了一份新的 'MyServicePackage'，這個 'MyServicePackage' 會啟動一份新的 'MyCodePackage'，而來自服務 **fabric:/App2/ServiceA** 之兩個分割區 (即 **P4** & **P5**) 的複本則會放在這份新的 'MyCodePackage' 中。

## <a name="shared-process-model"></a>共用處理序模型
上面所見即為 Service Fabric 提供的預設主控模型，並且稱為「共用處理序」模型。 在此模型中，針對指定的「應用程式」，在一個「節點」上只會啟用一份指定的 *ServicePackage* (這會啟動其包含的所有 *CodePackage*)，而指定的 *ServiceType* 之所有服務的所有複本則會放在註冊該 *ServiceType* 的 *CodePackage* 中。 換句話說，指定的 *ServiceType* 之所有服務的所有複本會共用相同的處理序。

## <a name="exclusive-process-model"></a>專屬處理序模型
Service Fabric 所提供的另一個主控模型是「專屬處理序」模型。 在此模型中，在指定的「節點」上，為了放置每個複本，Service Fabric 會啟用一份新的 *ServicePackage* (這會啟動其包含的所有 *CodePackage*)，而複本則會放在註冊複本所屬服務之 *ServiceType* 的 *CodePackage* 中。 換句話說，每個複本都存在於自己的專屬處理序中。 

從 Service Fabric 5.6 版開始即支援此模型。 您可以在建立服務時 (使用 [PowerShell][p1]、[REST][r1] 或 [FabricClient][c1]) 將 **ServicePackageActivationMode** 指定為 'ExclusiveProcess' 來選擇「專屬處理序」模型。

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

如果您的應用程式資訊清單中有預設的服務，您可以依以下所示的方式指定 **ServicePackageActivationMode** 屬性，來選擇「專屬處理序」模型：

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
繼續進行上述範例，讓我們在應用程式 **fabric:/App1** 中建立另一個服務 **fabric:/App1/ServiceC**，此服務有 2 個分割區 (即 **P6** & **P7**) 且每一分割區有 3 個複本，並且 **ServicePackageActivationMode** 已設定為 'ExclusiveProcess'。 下圖顯示節點上的新檢視：

<center>
![已部署之應用程式的節點檢視][node-view-four]
</center>

如您所見，Service Fabric 啟用了兩份新的 'MyServicePackage' (針對來自分割區 **P6** & **P7** 的每個複本各啟用一份)，並將每個複本放在其專用的一份 *CodePackage* 中。 這裡有另一個需要注意的事項，就是使用「專屬處理序」模型時，就指定的「應用程式」而言，一個「節點」 上可以有多份作用中的指定 *ServicePackage*。 在上述範例中，我們看到 **fabric:/App1** 有三份作用中的 'MyServicePackage'。 這每一份作用中的 'MyServicePackage' 都有相關的 **ServicePackageAtivationId**，用來作為它們在「應用程式」**fabric:/App1** 中的身分識別。

如果針對「應用程式」(例如上述範例中的 **fabric:/App2**) 只使用「共用處理序」模型，則一個「節點」上只會有一份作用中 *ServicePackage*，而這個 *ServicePackage* 啟用項的 **ServicePackageAtivationId** 則會是「空字串」。

> [!NOTE]
>- 「共用處理序」主控模型會與等於 **SharedProcess** 的 **ServicePackageAtivationMode** 對應。 這是預設的主控模型，在建立服務時不須指定 **ServicePackageAtivationMode**。
>
>- 「專屬處理序」主控模型會與等於 **ExclusiveProcess** 的 **ServicePackageAtivationMode** 對應，在建立服務時必須明確指定。 
>
>- 您可以透過查詢[服務描述][p2]並查看 **ServicePackageAtivationMode** 的值，來得知服務的主控模型。
>
>

## <a name="working-with-deployed-service-package"></a>使用已部署的服務套件
節點上一份作用中的 *ServicePackage* 稱為[已部署的服務套件][p3]。 如先前所述，使用「專屬處理序」模型來建立服務時，就指定的「應用程式」而言，同一個 *ServicePackage* 可能會有多個已部署的服務套件。 執行已部署之服務套件的特定作業 (例如[回報已部署之服務套件的健康情況][p4]或[重新啟動已部署之服務套件的程式碼套件][p5]等) 時，必須提供 **ServicePackageActivationId** 來識別特定的已部署服務套件。

 您可以查詢節點上[已部署的服務套件][p3]清單，來取得已部署之服務套件的 **ServicePackageActivationId**。 查詢節點上的[已部署的服務類型][p6]、[已部署的複本][p7]及[已部署的程式碼套件][p8]時，查詢結果也會包含父代已部署服務套件的 **ServicePackageActivationId**。

> [!NOTE]
>- 在「共用處理序」主控模型下，於指定的「節點」上，針對指定的「應用程式」，只會啟用一份 *ServicePackage*。 其 **ServicePackageActivationId** 會等於「空字串」，在執行已部署之服務套件的相關作業時不須指定。 
>
> - 在「專屬處理序」主控模型下，於指定的「節點」上，針對指定的「應用程式」，會有一或多份作用中的 *ServicePackage*。 每個啟動項都有一個「非空白」的 **ServicePackageActivationId**，在執行已部署之服務套件的相關作業時必須加以指定。 
>
> - 如果省略 **ServicePackageActivationId**，它會預設為「空字串」。 如果有在「共用處理序」模型下啟用的已部署服務套件存在，系統就會在該套件上執行作業，否則作業將會失敗。
>
> - 不建議執行一次查詢並快取 **ServicePackageActivationId**，因為這是動態產生的，可能因各種原因而有所變更。 在執行需要 **ServicePackageActivationId** 的作業之前，您應該先查詢節點上[已部署的服務套件][p3]清單，然後再使用來自查詢結果的 *ServicePackageActivationId** 來執行原先的作業。
>
>

## <a name="guest-executable-and-container-applications"></a>客體可執行檔和容器應用程式
Service Fabric 會將[客體可執行檔][a2]和[容器][a3]應用程式視為獨立的無狀態服務，亦即 *ServiceHost* (一個處理序或容器) 中沒有任何 Service Fabric 執行階段。 由於這些服務是獨立的，因此每一 *ServiceHost* 的複本數並不適用於這些服務。 與這些服務搭配使用的最常見組態是 [InstanceCount][c2] 等於 -1 的單一分割區 (亦即在叢集的每個節點上都會執行一份服務程式碼)。 

這些服務的預設 **ServicePackageActivationMode** 是 **SharedProcess**，在此情況下，Service Fabric 會針對指定的「應用程式」在一個「節點」上啟用一份 *ServicePackage*，這意謂著一個「節點」上只會執行一份服務程式碼。 當您建立多個 *ServiceType* 類型 (在 *ServiceManifest* 中指定) 的服務 (*Service1* 到 *ServiceN*) 時，或當您的服務具有多個分割區時，如果您想要讓多份服務程式碼在「節點」上執行，您應該在建立服務時，將 **ServicePackageActivationMode** 指定為 **ExclusiveProcess**。

## <a name="changing-hosting-model-of-an-existing-service"></a>變更現有服務的主控模型
目前不支援透過升級或更新機制 (或在應用程式資訊清單的預設服務規格中)，將現有服務的主控模型從「共用處理序」變更為「專屬處理序」，反之亦然。 在未來的版本中將會新增此功能。

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>在共用處理序與專屬處理序模型之間做選擇
這兩個主控模型各有其優缺點，使用者必須評估哪一個模型最符合其需求。 「共用處理序」模型可提升 OS 資源的使用率，因為產生的處理序較少、同一個處理序中的多個複本可以共用連接埠等。不過，如果其中一個複本發生錯誤而必須關閉服務主機，就會影響到該相同處理序中的所有其他複本。

 「專屬處理序」模型會讓每個複本處於自己的處理序中，而可提供較佳的隔離，發生問題的複本將不會影響到其他複本。 這適用於通訊協定不支援連接埠共用的情況。 它可協助在複本層級套用資源管理。 另一方面，「專屬處理序」會取用較多的 OS 資源，因為它會為節點上的每個複本都產生一個處理序。

## <a name="exclusive-process-model-and-application-model-considerations"></a>專屬處理序眉型和應用程式模型考量
在 Service Fabric 中，建議的應用程式模型建立方式是維持每一 *ServicePackage* 一個 *ServiceType*，這個模型適用於大多數應用程式。 

不過，若要啟用每一 *ServicePackage* 一個 *ServiceType* 可能不符合需求的特殊案例，就功能而言，Service Fabric 允許每一 *ServicePackage* 有多個 *ServiceType* (而且一個 *CodePackage* 可以註冊多個 *ServiceType*)。 以下是這些組態可能有用的一些案例：

- 您想要藉由減少產生處理序並提高每一處理序的複本密度，將 OS 資源使用率最佳化。
- 來自不同 ServiceType 的複本需要共用一些具有高初始化或記憶體成本的通用資料。
- 您有免費的服務方案，而您想要透過將服務的所有複本都放在同一個處理序中，為資源使用量設定限制。

「專屬處理序」主控模型與每一 *ServicePackage* 有多個 *ServiceType* 的應用程式模型並不相同。 這是因為每一 *ServicePackage* 多個 *ServiceType* 的設計是用來提高複本間的資源共用，以及提高每一處理序的複本密度。 這項設計與「專屬處理序」的設計目的相反。

思考一下每一 *ServicePackage* 有多個 *ServiceType* 的案例，其中是由不同的 *CodePackage* 註冊每個 *ServiceType*。 假設我們有一個 *ServicePackage* 'MultiTypeServicePackge'，其中包含兩個 *CodePackage*：

- 註冊 *ServiceType* 'MyServiceTypeA' 的 'MyCodePackageA'。
- 註冊 *ServiceType* 'MyServiceTypeB' 的 'MyCodePackageB'。

現在，假設我們建立一個「應用程式」**fabric:/SpecialApp**，並使用「專屬處理序」模型在 **fabric:/SpecialApp** 中建立下列兩個服務：

- 'MyServiceTypeA' 類型的服務 **fabric:/SpecialApp/ServiceA**，此服務有兩個分割區 (即 **P1** 和 **P2**) 且每一分割區有 3 個複本。
- 'MyServiceTypeB' 類型的服務 **fabric:/SpecialApp/ServiceB**，此服務有兩個分割區 (即 **P3** 和 **P4**) 且每一分割區有 3 個複本。

在指定的節點上，這兩個服務將各有兩個複本。 由於我們已使用「專屬處理序」模型來建立這些服務，因此 Service Fabric 會為每個複本建立一份新的 'MyServicePackge'。 每個 'MultiTypeServicePackge' 啟用項都會啟動一份 'MyCodePackageA' 和 'MyCodePackageB'。 不過，'MyCodePackageA' 或 'MyCodePackageB' 只有其中之一會裝載作為 'MultiTypeServicePackge' 啟用對象的複本。 下圖顯示節點檢視：

<center>
![已部署之應用程式的節點檢視][node-view-five]
</center>

 如我們所見，在服務 **fabric:/SpecialApp/ServiceA** 分割區 **P1** 之複本的 'MultiTypeServicePackge' 啟用項中，是由 'MyCodePackageA' 裝載複本，而 'MyCodePackageB' 則只是已啟動並在執行中。 同樣地，在服務 **fabric:/SpecialApp/ServiceB** 分割區 **P3** 之複本的 'MultiTypeServicePackge' 啟用項中，是由 'MyCodePackageB' 裝載複本，而 'MyCodePackageA' 則只是已啟動並在執行中。 因此，每一 *ServicePackage* 的 *CodePackage* (註冊不同的 *ServiceType*) 數目越多，備援資源的使用量就越高。 
 
 另一方便，如果我們使用「共用處理序」來建立服務 **fabric:/SpecialApp/ServiceA** 和 **fabric:/SpecialApp/ServiceB**，則 Service Fabric 將只會為「應用程式」**fabric:/SpecialApp** 啟用一份 'MultiTypeServicePackge' (如我們先前所見)。 'MyCodePackageA' 將會裝載服務 **fabric:/SpecialApp/ServiceA** (或更精確地說，是 'MyServiceTypeA' 類型的任何服務) 的所有複本，而 'MyCodePackageB' 則會裝載服務 **fabric:/SpecialApp/ServiceB** (或更精確地說，是 'MyServiceTypeB' 類型的任何服務) 的所有複本。 下圖顯示此設定中的節點檢視： 

<center>
![已部署之應用程式的節點檢視][node-view-six]
</center>

在上述範例中，您可能會認為如果 'MyCodePackageA' 既註冊 'MyServiceTypeA' 也註冊 'MyServiceTypeB'，而沒有 'MyCodePackageB' 存在，就不會執行備援的 *CodePackage*。 這個想法是正確的，不過，如先前所述，此應用程式模型與「專屬處理序」主控模型並不相同。 如果目標是要將每個複本放在其自己的專用處理序中，就不需要從同一個 *CodePackage* 註冊兩個 *ServiceType*，將每個 *ServiceType* 放在其自己的 *ServicePacakge* 才是較自然的選擇。

## <a name="next-steps"></a>後續步驟
[對應用程式進行封裝][a4]並使它準備好進行部署。

[部署與移除應用程式][a5]說明如何使用 PowerShell 來管理應用程式執行個體。

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-deploy-existing-app.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage