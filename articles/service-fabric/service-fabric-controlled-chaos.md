---
title: "在 Service Fabric 叢集中引發混亂 | Microsoft Docs"
description: "使用錯誤注射與叢集分析服務的 API 來管理叢集中的混亂。"
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: motanv
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 3b3b93bc9ec5ecdcfc289e5b62e84de6aa4172ed
ms.contentlocale: zh-tw
ms.lasthandoff: 08/12/2017

---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>在 Service Fabric 叢集中引發受控制的混亂
雲端基礎結構之類的大型分散式系統本身並不可靠。 Azure Service Fabric 可讓開發人員在不可靠的基礎結構之上撰寫可靠的分散式服務。 若要在不可靠的基礎結構之上撰寫健全的分散式服務，開發人員需要能夠測試其服務的穩定性，同時不可靠的基礎結構會因錯誤而經歷複雜的狀態轉換。

[錯誤插入與叢集分析服務](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (亦稱為「錯誤分析服務」) 讓開發人員能夠引發錯誤來測試其服務。 這些針對性模擬錯誤，例如[重新啟動分割區](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)，可幫助您練習最常見的狀態轉換。 但針對性模擬錯誤會因為定義而有偏差，因此可能會遺漏只會出現在難以預測、冗長且複雜的狀態轉換順序中的問題。 如需無偏差測試，您可以使用混亂。

混亂會以很長的時間在整個叢集上模擬定期、交錯的錯誤 (包括非失誤性和失誤性錯誤)。 設定混亂的比率和錯誤類型後，即可透過 C# 或 Powershell API 啟動混亂，以開始在叢集和您的服務中產生錯誤。 您可以設定混亂會執行一段指定的時間 (例如 1 小時)、在哪個混亂自動停止後執行，或隨時呼叫 StopChaos API (C# 或 Powershell) 來停止它。

> [!NOTE]
> 目前來說，混亂只會引發安全的錯誤，這表示如果沒有外部錯誤，絕不會發生仲裁遺失或資料遺失。
>

混亂執行時，會產生不同事件來擷取目前執行的狀態。 例如，ExecutingFaultsEvent 包含混亂已決定正在該反覆運算中執行的所有錯誤。 ValidationFailedEvent 包含在驗證叢集期間所發現驗證失敗 (健康情況或穩定性問題) 的詳細資料。 您可以叫用 GetChaosReport API (C# 或 Powershell) 以取得混亂執行的報告。 這些事件保存在[可靠的字典](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections)中，其中有由兩個組態決定的截斷原則：MaxStoredChaosEventCount (預設值為 25000) 及 StoredActionCleanupIntervalInSeconds (預設值為 3600)。 每個 StoredActionCleanupIntervalInSeconds 混亂檢查及最新 MaxStoredChaosEventCount 事件以外的所有事件皆會自可靠字典中清除。

## <a name="faults-induced-in-chaos"></a>混亂中引發的錯誤
混亂會在整個 Service Fabric 叢集中產生錯誤，並將在幾個月或幾年內看到的錯誤壓縮成幾小時。 交錯錯誤和高錯誤率的組合，會尋找可能會在其他情形下遺漏的極端狀況。 這個混亂練習可以大幅提升服務的程式碼品質。

混亂會引發下列類別的錯誤︰

* 重新啟動節點
* 重新啟動已部署的程式碼封裝
* 移除複本
* 重新啟動複本
* 移動主要複本 (可設定)
* 移動次要複本 (可設定)

混亂會多次反覆執行。 每次反覆運算都包含指定期間的錯誤和叢集驗證。 您可以設定讓叢集穩定和驗證成功的所需時間。 如果在叢集驗證中發現失敗，則混亂會產生並保留一個 ValidationFailedEvent，包含 UTC 時間戳記與失敗詳細資料。 例如，考慮一個設為執行 1 小時且最多有 3 個並行錯誤的混亂執行個體。 混亂會引發三個錯誤，然後驗證叢集健康狀態。 它會重複執行上一個步驟，直到透過 StopChaosAsync API 或經過一小時後就會明確停止。 如果任何反覆運算中的叢集變成健康情況不佳 (也就是在傳入的 MaxClusterStabilizationTimeout 內不穩定)，則混亂會產生 ValidationFailedEvent。 此事件表示發生了錯誤，且可能需要進一步調查。

若要取得混亂引發的錯誤，您可以使用 GetChaosReport API (powershell 或 C#)。 API 會根據傳入接續權杖或傳入的時間範圍取得混亂報告的下個區段。 您可以指定 ContinuationToken 取得混亂報告的下個區段，或者您可以透過 StartTimeUtc 與 EndTimeUtc 指定時間範圍，但您無法在同一個呼叫中同時指定 ContinuationToken 與時間範圍。 當混亂事件超過 100 個時，系統會分區段傳回混亂報告，每個區段中包含的混亂事件不超過 100 個。

## <a name="important-configuration-options"></a>重要的組態選項
* **TimeToRun**：混亂在成功完成前的總執行時間。 您可以在混亂執行 TimeToRun 這段時間之前透過 StopChaos API 停止混亂。

* **MaxClusterStabilizationTimeout**：在產生 ValidationFailedEvent 前等候叢集健康情況變為良好的時間上限。 這段等候時間是為了減少叢集在復原時所承擔的負載。 執行的檢查為：
  * 叢集健康狀態是否正常
  * 服務健康狀態是否正常
  * 服務分割區是否達到目標複本集大小
  * 沒有 InBuild 複本存在
* **MaxConcurrentFaults**：每個反覆運算中引發的最大並行錯誤數。 數字愈大，混亂愈積極，叢集經歷的容錯移轉與狀態轉換也更複雜。 

> [!NOTE]
> 無論 *MaxConcurrentFaults* 值多大，混亂都能保證在缺少外部錯誤的狀況下，不會有仲裁遺失或資料遺失。
>

* **EnableMoveReplicaFaults**：啟用或停用造成主要或次要複本移動的錯誤。 預設會停用這些錯誤。
* **WaitTimeBetweenIterations**︰反覆運算之間要等候的時間量。 亦即，在已執行一輪的錯誤且已完成對應的叢集健康情況驗證後，混亂將暫停的時間。 值愈大，平均錯誤插入率愈低。
* **WaitTimeBetweenFaults**︰單一反覆運算中兩個連續錯誤之間的等候時間長度。 值愈大，錯誤的並行程度 (或錯誤之間的重疊度) 愈低。
* **ClusterHealthPolicy**︰叢集健康情況原則用於驗證混亂反覆運算之間的叢集健康情況。 如果叢集健康情況發生錯誤，或如果在錯誤執行期間發生未預期的例外狀況，則混亂會先等待 30 分鐘，再執行下一個健康情況檢查，讓叢集有時間復原。
* **內容**：(string, string) 類型索引鍵-值組的集合。 此對應可用於記錄混亂執行的相關資訊。 此類組合不能超過 100 個，且每個字串 (索引鍵或值) 最多為 4095 個字元長。 此對應由混亂執行的起始者設定，以選擇性地儲存特定執行的相關內容。

## <a name="how-to-run-chaos"></a>如何執行混亂

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```

