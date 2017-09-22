---
title: "適用於容器和服務的 Azure Service Fabric 資源管理 | Microsoft Docs"
description: "Azure Service Fabric 可讓您針對在容器內部或外部執行的服務指定資源限制。"
services: service-fabric
documentationcenter: .net
author: masnider
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
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 62ce17533f8c195b873431089e1f1f47cb0bcbec
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---

# <a name="resource-governance"></a>資源管理 

在相同的節點或叢集上執行多個服務時，有可能發生一個服務取用較多資源而導致其他服務無資源可用的情況。 此問題稱為擾鄰問題。 Service Fabric 可讓開發人員指定每一服務的保留和限制，以確保有資源可用並且也限制其資源使用量。

>
> 繼續本文之前，您應該取得熟悉 [Service Fabric 應用程式模型](service-fabric-application-model.md)和[Service Fabric 裝載模型](service-fabric-hosting-model.md)。
>

## <a name="resource-governance-metrics"></a>資源管理計量 

Service Fabric 可依每一[服務套件](service-fabric-application-model.md)支援資源管理。 指派給「服務套件」的資源可以進一步在程式碼套件之間分配。 指定的資源限制也意謂著資源的保留。 Service Fabric 支援使用兩個內建的[計量](service-fabric-cluster-resource-manager-metrics.md)來指定每一服務套件的 CPU 和記憶體：

* CPU (計量名稱 `servicefabric:/_CpuCores`)：核心係指主機電腦上可用的邏輯核心，所有節點上所有核心的權數都相同。
* 記憶體 (計量名稱 `servicefabric:/_MemoryInMB`)：記憶體的單位為 MB，它會與電腦上可用的實體記憶體對應。

針對這兩個計量，[叢集資源管理員](service-fabric-cluster-resource-manager-cluster-description.md)會追蹤叢集總容量、叢集中每個節點上的負載，以及叢集中剩餘的資源數。 這兩個計量等同於任何其他使用者或自訂計量，而且所有現有的功能都可以與它們搭配使用：
* 叢集可以根據這兩個計量 (預設行為) 來進行[平衡](service-fabric-cluster-resource-manager-balancing.md)。
* 叢集可以根據這兩個計量來進行[重組](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 在[描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)時，可以為這兩個計量設定緩衝處理的容量。

這些計量不支援[動態負載報告](service-fabric-cluster-resource-manager-metrics.md)，而這些計量的負載則是在建立時定義。

## <a name="resource-governance-mechanism"></a>資源管理機制

Service Fabric 執行階段目前不提供資源的保留。 當開啟處理序或容器時，執行階段會將資源限制設定為在建立時定義的負載。 而且，如果超出可用的資源數，執行階段就會拒絕開啟新的服務套件。 為了進一步了解處理序的運作方式，讓我們來看具有 2 個 CPU 核心的節點範例 (記憶體管理的機制都相同)：

1. 首先，容器會放在節點上，要求 1 個 CPU 核心。 執行階段會開啟容器，並將 CPU 限制設定為 1 個核心。 容器將無法使用 1 個以上的核心。
2. 然後，服務的複本會放在節點上，對應的服務套件會指定 1 個 CPU 核心的限制。 執行階段會開啟程式碼套件，並將其 CPU 限制設定為 1 個核心。

此時，限制的總和等於節點的容量，有 1 個核心在執行一個處理序和一個容器，兩者不會互相干擾。 Service Fabric 會再放更多容器或複本，以防它們指定 CPU 限制。 不過，有兩種情況其他處理序可能會爭奪 CPU，而我們範例中的處理序和容器可能會遇到吵雜芳鄰的問題：

* 混合有管理和無管理的服務和容器：如果使用者建立服務時沒有指定任何資源管理，執行階段會當作它不會耗費任何資源，且能夠將它放在我們的範例中的節點上。 在此情況下，這個新處理序會不客氣地耗用一些 CPU，犧牲已在節點上執行的服務。 要解決這個問題，是不要在相同叢集中混合有管理和無管理的服務，或使用[位置條件約束](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)讓它們不會放在同一組節點上。
* 在 Service Fabric 外 (例如，部分 OS 服務) 的節點上有其他處理序啟動，此處理序也會與現有的服務爭奪 CPU。 這個問題的解決方法是設定節點容量時正確計入 OS 額外負荷，如下一節所述。

## <a name="cluster-set-up-for-enabling-resource-governance"></a>設定叢集以啟用資源管理

當節點啟動並加入叢集時，Service Fabric 會偵測可用的記憶體數量及可用的核心數目，並設定這兩個資源的節點容量。 為了要保留一些緩衝空間給作業系統和其他可能在節點上執行的處理序，Service Fabric 只會使用節點可用資源的 80%。 這個百分比可加以設定，並可在叢集資訊清單中變更。 以下範例指示 Service Fabric 使用可用 CPU 的 50% 以及可用記憶體的 70%： 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

如果需要完全手動設定節點容量，也可以使用一般機制描述叢集中的節點。 以下是具有 4 個核心 2 GB 記憶體的節點設定範例: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

當您啟用自動偵測可用資源，並手動定義叢集資訊清單中的節點容量，Service Fabric 會檢查節點是否有足夠資源來支援使用者已定義的容量：
* 如果資訊清單中定義的節點容量小於或等於節點上的可用資源，Service Fabric 會使用資訊清單中指定的容量。
* 如果資訊清單中定義的節點容量大於可用資源，Service Fabric 會使用可用資源作為節點容量。

如果不需要自動偵測可用資源的功能，可以變更下列設定將其完全關閉：

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

為了獲得最佳效能，在叢集資訊清單中應該也開啟下列設定： 

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

## <a name="other-resources-for-containers"></a>容器的其他資源
除了 CPU 和記憶體，也可以為容器指定其他資源限制。 這些限制是在程式碼套件層級指定，並會在容器啟動時套用。 不同於 CPU 和記憶體，叢集資源管理員不會知道這些資源，也不會進行任何容量檢查或負載平衡。 

* MemorySwapInMB - 容器可使用的交換記憶體數量。
* MemoryReservationInMB - 記憶體管理的軟限制，只有在節點上偵測到記憶體競爭的情況時才會強制執行。
* CpuPercent - 容器可使用的 CPU 百分比。 若有指定服務套件的 CPU 限制，則會實際忽略此參數。
* MaximumIOps - 容器可使用 (讀取和寫入) 的 IOPS 上限 (讀取和寫入)。
* MaximumIOBytesps - 容器可使用 (讀取和寫入) 的 IO 上限。
* BlockIOWeight - 區塊相對於其他容器的 IO 權數。

這些資源可以與 CPU 和記憶體結合。 以下是如何指定容器的其他資源的範例：

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>後續步驟
* 若要深入了解「叢集資源管理員」，請參閱這篇[文章](service-fabric-cluster-resource-manager-introduction.md)。
* 若要深入了解應用程式模型、服務套件、程式碼套件，以及複本如何與它們對應，請參閱這篇[文章](service-fabric-application-model.md)。

