---
title: "使用系統健康狀態報告進行疑難排解 | Microsoft 疑難排解"
description: "描述針對 Azure Service Fabric 元件及其使用量所傳送的健康狀態報告，以便對叢集或應用程式問題進行疑難排解。"
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: oanapl
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 54e20146b2f1e0ca6153b66319be70c6f7c2fb59
ms.contentlocale: zh-tw
ms.lasthandoff: 08/19/2017

---
# <a name="use-system-health-reports-to-troubleshoot"></a>使用系統健康狀態報告進行疑難排解
Azure Service Fabric 元件會針對叢集中的所有實體提供現成的報告。 [健康狀態資料存放區](service-fabric-health-introduction.md#health-store) 會根據系統報告來建立和刪除實體。 它也會將這些實體組織為階層以擷取實體的互動。

> [!NOTE]
> 若要了解健康狀態相關概念，請詳細閱讀 [Service Fabric 健康狀態模型](service-fabric-health-introduction.md)。
> 
> 

系統健康狀態報告可讓您全盤掌握叢集和應用程式功能，並透過健康狀態標記問題。 系統健康狀態報告會針對應用程式和服務來確認實體是否已實作，以及從 Service Fabric 的角度來確認其是行為是否正確。 報告並不會監控任何服務商務邏輯的健康情況，也不會偵測是否有無回應的處理程序。 使用者服務可使用其邏輯的特定資訊讓健康情況資料更豐富。

> [!NOTE]
> 監視程式健康狀態報告只有在系統元件建立實體「之後」才會顯示。 刪除實體時，健康狀態資料存放區會自動刪除所有與其相關聯的健康狀態報告。 建立實體之新執行個體時的處理方式也一樣 (例如，建立新的具狀態持續性服務複本執行個體)。 所有與舊執行個體相關聯的報告都會從存放區刪除及清除。
> 
> 

系統元件報告將由來源識別，它會以 **System.** 前置詞做為開頭 。 看門狗不能對來源使用相同的前置詞，因為含有無效參數的報告會遭到拒絕。
讓我們來看看部分系統報告，了解何者觸發了它們，以及如何修正它們所代表的可能問題。

> [!NOTE]
> Service Fabric 會繼續以感興趣的條件來新增報告，藉此改善叢集和應用程式中狀況的可見性。 更多詳細資料也可以增強現有的報告，有助於更快排解疑難問題。
> 
> 

## <a name="cluster-system-health-reports"></a>叢集系統健康狀態報告
健康狀態資料存放區中會自動建立叢集健康狀態實體。 如果一切正常運作，則不會有系統報告。

### <a name="neighborhood-loss"></a>網路上的芳鄰遺失
**System.Federation** 偵測到網路上的芳鄰遺失時，即會回報錯誤。 報告來自各別的節點，且節點識別碼會包含於屬性名稱中。 如果整個 Service Fabric 環形中有一個網路上的芳鄰遺失，您通常可預期會產生兩個事件 (間距的兩端都會報告)。 如果有多個網路上的芳鄰遺失，將會產生更多的事件。

報告會將全域租用逾時指定為存留時間。 只要條件仍在作用中，就會在每半個 TTL 期間重新傳送一次報告。 事件到期時會自動移除。 到期時移除的行為可確保正確地從健康狀態資料存放區清除報告，即使報告節點已關閉也不例外。

* **SourceId**：System.Federation
* **Property**：以 **Neighborhood** 為開頭且包含節點資訊
* **後續步驟**：調查網路上的芳鄰遺失的原因 (例如，檢查叢集節點之間的通訊)。

## <a name="node-system-health-reports"></a>節點系統健康狀態報告
**System.FM**(代表容錯移轉管理員服務) 是管理叢集節點相關資訊的授權單位。 每個節點都應該有一份來自 System.FM 的報告，以顯示其狀態。 移除節點狀態時會移除節點實體 (請參閱 [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync))。

### <a name="node-updown"></a>節點運作中/關閉
當節點加入環形時，System.FM 會回報為 OK (節點已啟動且正在運作中)。 當節點離開環形時，則會回報錯誤 (節點已關閉進行升級，或只是發生故障)。 由健康狀態資料存放區建置的健康狀態階層會根據 System.FM 節點報告，對部署的實體採取行動。 它會將節點視為所有已部署實體的虛擬父系。 如果 System.FM 回報指出該節點已啟動，且其執行個體與實體相關聯的執行個體相同，則該節點上已部署的實體將會透過查詢公開。 當 System.FM 回報節點已關閉或重新啟動 (新執行個體) 時，健康狀態資料存放區會自動清除僅能存在於已關閉節點或先前的節點執行個體上的已部署實體。

* **SourceId**：System.FM
* **Property**：State
* **後續步驟**：如果節點已關閉來進行升級，應該會在升級後重新啟動。 在這種情況下，健康情況應切換回 OK。 如果節點沒有重新啟動或故障，就需要進一步調查問題。

以下範例說明帶有 OK (代表節點已啟動) 健全狀況狀態的 System.FM 事件：

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>憑證到期
**System.FabricNode** 會回報警告。 每個節點有三個憑證：**Certificate_cluster**、**Certificate_server** 及 **Certificate_default_client**。 如果過期時間至少超過兩週，報告健康狀態就是 OK。 如果過期時間是在兩週內，則報告類型會是 Warning。 這些事件的 TTL 是無限制的，只有節點離開叢集時才會被移除。

* **SourceId**：System.FabricNode
* **Property**：以 **Certificate** 為開頭，且包含關於憑證類型的詳細資訊
* **後續步驟**：如果憑證即將到期，請更新憑證。

### <a name="load-capacity-violation"></a>負載容量違規
當 Service Fabric Load Balancer 偵測到節點容量違規時，就會回報警告。

* **SourceId**：System.PLB
* **Property**：以 **Capacity** 為開頭
* **後續步驟**：檢查提供的計量，並在節點上檢視目前容量。

## <a name="application-system-health-reports"></a>應用程式系統健康狀態報告
**System.CM**(代表叢集管理員服務) 是管理應用程式相關資訊的授權單位。

### <a name="state"></a>State
已建立或更新應用程式時，System.CM 會回報為 OK。 已刪除應用程式時，它會通知健康狀態資料存放區，以便從存放區將它移除。

* **SourceId**：System.CM
* **Property**：State
* **後續步驟**：如果已建立或更新應用程式，它就應該包含叢集管理員健康情況報告。 否則，請發出查詢 (例如 PowerShell Cmdlet **Get-ServiceFabricApplication -ApplicationName *applicationName***) 以檢查應用程式狀態。

以下範例說明 **fabric:/WordCount** 應用程式上的狀態事件：

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>服務系統健康狀態報告
**System.FM**(代表容錯移轉管理員服務) 是管理服務相關資訊的授權單位。

### <a name="state"></a>State
已建立服務時，System.FM 會回報為 OK。 已刪除服務時，它會從健康狀態資料存放區刪除實體。

* **SourceId**：System.FM
* **Property**：State

以下範例說明 **fabric:/WordCount/WordCountWebService** 服務上的狀態事件：

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>服務相互關聯錯誤
**System.PLB** 會在偵測到更新要與其他服務相互關聯的服務建立同質鏈結時，就會回報錯誤。 更新成功時，就會清除報告。

* **SourceId**：System.PLB
* **屬性**︰ServiceDescription
* **後續步驟**：檢查相互關聯的服務說明。

## <a name="partition-system-health-reports"></a>分割區系統健康狀態報告
**System.FM**(代表容錯移轉管理員服務) 是管理服務分割區相關資訊的授權單位。

### <a name="state"></a>State
已建立分割區且其狀況良好時，System.FM 會回報為 OK。 刪除分割區時，它會從健康狀態資料存放區刪除實體。

如果分割區低於最小複本計數，它會回報錯誤。 如果分割區高於最小複本計數，但低於目標複本計數，則會回報警告。 如果分割區處於仲裁遺失狀態，System.FM 會回報錯誤。

其他重要事件包括：當重新設定花費的時間比預期長，或者建置的時間比預期長，則會回報警告。 建置和重新設定的預計時間可根據服務案來例設定。 例如，如果服務擁有 1 TB 的狀態 (例如 SQL Database)，則建置的時間會比只有少量狀態的服務更長。

* **SourceId**：System.FM
* **Property**：State
* **後續步驟**：如果健康狀態不是 OK，有可能是因為部分複本並沒有正確建立、開啟、提升為主要或次要的複本。 在許多情況下，根本原因是在開啟或變更角色實作時發生服務錯誤。

以下範例顯示狀況良好的分割區：

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

以下範例顯示分割區的健全狀況低於目標複本計數。 後續步驟是取得分割區說明，它將說明設定方式：**MinReplicaSetSize** 為三，**TargetReplicaSetSize** 為七。 接著取得叢集中的節點數：五。 因此，在此情況下，無法放置兩個複本，因為複本的目標數目大於可用節點的數目。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/P RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>複本條件約束違規
**System.PLB** 偵測到複本條件約束違規，且無法安置所有磁碟分割複本時，就會回報警告。 報告詳細資料會顯示哪些條件約束和屬性導致無法安置複本。

* **SourceId**：System.PLB
* **Property**：以 **ReplicaConstraintViolation** 為開頭

## <a name="replica-system-health-reports"></a>複本系統健康狀態報告
**System.RA**(代表重新設定代理程式元件) 是複本狀態的授權單位。

### <a name="state"></a>State
**System.RA** 會在複本建立後回報 OK。

* **SourceId**：System.RA
* **Property**：State

以下範例顯示狀況良好的複本：

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replica-open-status"></a>複本開啟狀態
叫用 API 呼叫時，此健康情況報告的說明會包含開始時間 (國際標準時間)。

**System.RA** 會回報警告。 如果 API 影響服務可用性，則報告發出速度就會快上許多 (可設定的間隔，預設值 30 秒)。 測量的時間包含複寫器開啟及服務開啟所花費的時間。 如果開啟完成，則屬性會變更為 OK。

* **SourceId**：System.RA
* **Property**服務上的狀態事件： **ReplicaOpenStatus**
* **後續步驟**：如果健康狀態不是 OK，請調查複本開啟所花費的時間超過預期的原因。

### <a name="slow-service-api-call"></a>緩慢服務 API 呼叫
如果呼叫使用者服務程式碼所花費的時間超過設定的時間，**System.RAP** 和 **System.Replicator** 就會回報警告。 當呼叫完成時，警告就會被清除。

* **SourceId**：System.RAP 或 System.Replicator
* **Property**：緩慢 API 的名稱。 該說明會提供有關 API 擱置時間的詳細資訊。
* **後續步驟**：調查呼叫所花費時間超過預期的原因。

下列範例說明處於仲裁遺失狀態的分割區，以及調查原因時需進行的步驟。 若其中一個複本的健康狀態為 Warning，您就會取得其健康狀態。 它會顯示服務作業所花費的時間超過預期 (由 System.RAP 回報的事件)。 接收到此資訊之後，下一個步驟就是查看服務程式碼並詳加調查。 在此情況下，具狀態服務的 **RunAsync** 實作會擲回未處理的例外狀況。 因為複本會進行回收，所以您可能不會看到任何處於警告狀態的複本。 您可以嘗試重新取得健康狀態，然後查看複本識別碼中是否有任何差異。 在某些情況下，重試可讓您得到一些線索。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 3
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

當您在偵錯工具中啟動錯誤的應用程式時，[診斷事件] 視窗會顯示從 RunAsync 所擲回的例外狀況：

![Visual Studio 2015 診斷事件：RunAsync 在 fabric:/HelloWorldStatefulApplication 中失敗。][1]

Visual Studio 2015 診斷事件：RunAsync 在 **fabric:/HelloWorldStatefulApplication**中失敗。

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>複寫佇列已滿
**System.Replicator** 會在複寫佇列已滿時回報警告。 在主要資料庫上，複寫佇列通常會因為一或多個次要複本太慢認可作業而排滿。 在次要複本上，這通常是因為服務緩慢而無法套用作業所造成。 當佇列有空間時，警告就會被清除。

* **SourceId**：System.Replicator
* **Property**：**PrimaryReplicationQueueStatus** 或 **SecondaryReplicationQueueStatus** (根據複本角色而定)

### <a name="slow-naming-operations"></a>緩慢的命名作業
**System.NamingService** 會報告其主要複本的健全狀況。 命名作業的範例為 [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) 或 [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync)。 在 FabricClient 下可以找到更多方法，例如在[服務管理方法](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient)或[屬性管理方法](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient)底下。

> [!NOTE]
> 命名服務會將服務名稱解析至叢集中的位置，並可讓使用者能夠管理服務名稱和屬性。 它是 Service Fabric 資料分割保存的服務。 其中一個分割區代表「授權擁有者」，內含所有 Service Fabric 名稱和服務的中繼資料。 Service Fabric 名稱會對應至不同的資料分割 (稱為「名稱擁有者資料分割」)，讓服務可以擴充。 深入了解 [命名服務](service-fabric-architecture.md)。
> 
> 

命名作業所花費的時間超出預期，在 *負責處理該作業的命名服務資料分割的主要複本*上，該作業會標幟警告報表。 如果作業順利完成，就會清除警告。 如果作業完成但發生錯誤，健全狀況報告會包含錯誤的詳細資訊。

* **SourceId**：System.NamingService
* **Property**：以 **Duration_** 前置詞開始，識別緩慢作業和套用該作業的 Service Fabric 名稱。 例如，如果在名稱網狀架構建立 /MyApp/MyService 服務花太多時間，其 Property 就是 Duration_AOCreateService.fabric:/MyApp/MyService。 AO 指向這個名稱和作業的命名資料分割的角色。
* **後續步驟**︰檢查命名作業失敗的原因。 每個作業都可能有不同的根本原因。 例如，由於服務程式碼中的使用者錯誤造成節點上的應用程式主機持續當機，使刪除服務會卡在節點上。

以下範例顯示一個建立服務作業。 作業所花費的時間超過設定的期間。 AO 重試，並將工作傳送到 NO。 NO 逾時完成最後一個作業。 在此情況中，AO 和 NO 角色有相同的主要複本。

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication 系統健康狀態報告
**System.Hosting** 是已部署實體的授權單位。

### <a name="activation"></a>啟用
當應用程式在節點上成功啟用時，System.Hosting 會回報為 OK。 否則，它會報告錯誤。

* **SourceId**：System.Hosting
* **Property**：Activation，包括首度發行版本
* **後續步驟**：如果應用程式的狀況不佳，請調查啟用失敗的原因。

以下範例顯示成功啟用的情況：

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>下載
**System.Hosting** 會回報錯誤。

* **SourceId**：System.Hosting
* **Property**：**Download:*RolloutVersion***
* **後續步驟**：調查節點上下載失敗的原因。

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage 系統健康狀態報告
**System.Hosting** 是已部署實體的授權單位。

### <a name="service-package-activation"></a>服務封裝啟用
如果節點上的服務封裝成功啟用，System.Hosting 會回報為 OK。 否則，它會報告錯誤。

* **SourceId**：System.Hosting
* **Property**：Activation
* **後續步驟**：調查啟用失敗的原因。

### <a name="code-package-activation"></a>程式碼封裝啟用
**System.Hosting** 會針對每個程式碼封裝回報 OK。 如果啟用失敗，它會依設定回報警告。 如果 **CodePackage** 無法啟用，或者因為錯誤數超過 **CodePackageHealthErrorThreshold** 的設定而結束，則 Hosting 會回報錯誤。 如果服務封裝包含多個程式碼封裝，就會針對每個封裝產生啟用報告。

* **SourceId**：System.Hosting
* **Property**：使用前置詞 **CodePackageActivation**，並以 **CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint*** 形式 (例如 **CodePackageActivation:Code:SetupEntryPoint**) 包含程式碼封裝的名稱和進入點

### <a name="service-type-registration"></a>服務類型註冊
**System.Hosting** 會回報為 OK。 如果註冊未及時完成 (使用 **ServiceTypeRegistrationTimeout**來設定)，則會回報錯誤。 如果執行階段已關閉，則會從節點取消註冊服務類型，且主機會回報警告。

* **SourceId**：System.Hosting
* **Property**：使用前置詞 **ServiceTypeRegistration**，並包含服務類型名稱 (例如，**ServiceTypeRegistration:FileStoreServiceType**)

以下顯示顯示狀況良好的已部署服務封裝：

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>下載
**System.Hosting** 會回報錯誤。

* **SourceId**：System.Hosting
* **Property**：**Download:*RolloutVersion***
* **後續步驟**：調查節點上下載失敗的原因。

### <a name="upgrade-validation"></a>升級驗證
**System.Hosting** 會回報錯誤。

* **SourceId**：System.Hosting
* **Property**：使用 **FabricUpgradeValidation** 前置詞，並包含升級版本
* **Description**：指出發生的錯誤

## <a name="next-steps"></a>後續步驟
[檢視 Service Fabric 健康狀態報告](service-fabric-view-entities-aggregated-health.md)

[如何回報和檢查服務健全狀況](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[在本機上監視及診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric 應用程式升級](service-fabric-application-upgrade.md)


