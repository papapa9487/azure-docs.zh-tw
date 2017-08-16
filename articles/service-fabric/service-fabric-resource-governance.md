---
title: "適用於容器和服務的 Azure Service Fabric 資源管理 | Microsoft Docs"
description: "Azure Service Fabric 可讓您針對在容器內部或外部執行的服務指定資源限制。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e490dfba28b8a270bf0e6022333f148c965bc6dc
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="resource-governance"></a>資源管理 

在相同的節點或叢集上執行多個服務時，有可能發生一個服務取用較多資源而導致其他服務無資源可用的情況。 此問題稱為擾鄰問題。 Service Fabric 可讓開發人員指定每一服務的保留和限制，以確保有資源可用並且也限制其資源使用量。 

## <a name="resource-governance-metrics"></a>資源管理計量 

Service Fabric 可依每一[服務套件](service-fabric-application-model.md)支援資源管理。 指派給「服務套件」的資源可以進一步在程式碼套件之間分配。 指定的資源限制也意謂著資源的保留。 Service Fabric 支援使用兩個內建的[計量](service-fabric-cluster-resource-manager-metrics.md)來指定每一服務套件的 CPU 和記憶體：

* CPU (計量名稱 `ServiceFabric:/_CpuCores`)：核心係指主機電腦上可用的邏輯核心，所有節點上所有核心的權數都相同。
* 記憶體 (計量名稱 `ServiceFabric:/_MemoryInMB`)：記憶體的單位為 MB，它會與電腦上可用的實體記憶體對應。

僅提供彈性保留保證 - 如果超出可用的資源數，執行階段就會拒絕開啟新的服務套件。 不過，如在節點上放置另一個可執行檔或容器，可能就會違反原先的保留保證。

針對這兩個計量，[叢集資源管理員](service-fabric-cluster-resource-manager-cluster-description.md)會追蹤叢集總容量、叢集中每個節點上的負載，以及叢集中剩餘的資源數。 這兩個計量等同於任何其他使用者或自訂計量，而且所有現有的功能都可以與它們搭配使用：
* 叢集可以根據這兩個計量 (預設行為) 來進行[平衡](service-fabric-cluster-resource-manager-balancing.md)。
* 叢集可以根據這兩個計量來進行[重組](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 在[描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)時，可以為這兩個計量設定緩衝處理的容量。

這些計量不支援[動態負載報告](service-fabric-cluster-resource-manager-metrics.md)，而這些計量的負載則是在建立時定義。

## <a name="cluster-set-up-for-enabling-resource-governance"></a>設定叢集以啟用資源管理

您應該依下列方式，在叢集的每個節點中手動定義容量：

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="ServiceFabric:/_CpuCores" Value="4"/>
        <Capacity Name="ServiceFabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```
 
只有在使用者服務上才允許進行資源管理，在任何系統服務上則都不允許。 指定容量時，必須保留部分核心和記憶體不進行配置，以供系統服務使用。 為了獲得最佳效能，在叢集資訊清單中應該也開啟下列設定： 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specifying-resource-governance"></a>指定資源管理 

指定資源管理限制時，是在應用程式資訊清單 (ServiceManifestImport 區段) 中指定，如下列範例所示：

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric will sum the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
在此範例中，服務套件 ServicePackageA 在其所在的節點上獲得一個核心。 此服務套件包含兩個程式碼套件 (CodeA1 和 CodeA2)，且兩者皆指定 `CpuShares` 參數。 CpuShares 的比例 512:256 會將核心在這兩個程式碼套件做分配。 因此，在此範例中，CodeA1 會獲得 2/3 的核心，CodeA2 則獲得 1/3 的核心 (並具有同樣的彈性保證保留)。 如果未針對程式碼套件指定 CpuShares，Service Fabric 就會在它們之間平均分配核心。

記憶體限制是絕對的，因此這兩個程式碼套件都限制為 1024 MB 的記憶體 (並具有同樣的彈性保證保留)。 程式碼套件 (容器或處理序) 無法配置超過此限制的記憶體，如果嘗試這麼做，將會導致發生記憶體不足的例外狀況。 若要讓資源限制強制能夠運作，應該為服務套件內的所有程式碼套件都指定記憶體限制。


## <a name="next-steps"></a>後續步驟
* 若要深入了解「叢集資源管理員」，請參閱這篇[文章](service-fabric-cluster-resource-manager-introduction.md)。
* 若要深入了解應用程式模型、服務套件、程式碼套件，以及複本如何與它們對應，請參閱這篇[文章](service-fabric-application-model.md)。

