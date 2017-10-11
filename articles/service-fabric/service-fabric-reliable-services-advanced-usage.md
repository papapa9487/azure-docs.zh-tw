---
title: "Reliable Services 的進階用法 | Microsoft Docs"
description: "深入了解 Service Fabric 的 Reliable Services 的進階用法，以在服務中增加彈性。"
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: a87924faaf5c6c43716b06b6d70ab5100c61f097
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>可靠服務程式設計模型的進階用法
Azure Service Fabric 可簡化撰寫和管理可靠的無狀態與具狀態服務。 本指南會討論到 Reliable Services 的進階用法，以在您的服務中取得更多控制權和彈性。 在閱讀這份指南之前，請您先熟悉＜ [可靠的服務程式設計模型](service-fabric-reliable-services-introduction.md)＞。

可設定狀態與無狀態服務有使用者程式碼的兩個主要進入點：

* `RunAsync(C#) / runAsync(Java)` 是服務程式碼的一般用途進入點。
* `CreateServiceReplicaListeners(C#)` 和 `CreateServiceInstanceListeners(C#) / createServiceInstanceListeners(Java)` 適用於開啟用戶端要求的通訊接聽程式。

對於大部分服務而言，這些兩個進入點已足夠。 在少數情況下，需要更充分掌控服務的命週期時，可以使用其他生命週期事件。

## <a name="stateless-service-instance-lifecycle"></a>無狀態服務執行個體生命週期
無狀態服務的生命週期非常簡單。 無狀態服務只能開啟、關閉或中止。 無狀態服務中的 `RunAsync` 會在服務執行個體開啟時執行，並在服務執行個體關閉或中止時取消。

雖然 `RunAsync` 應該足以應付幾乎所有情況，但也可使用無狀態服務中的開啟、關閉和中止事件︰

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken) - C# / CompletableFuture<String> onOpenAsync(CancellationToken) - Java` 即將使用無狀態服務執行個體時，會呼叫 OnOpenAsync。 這個階段可以啟動擴充服務的初始化作業。
* `Task OnCloseAsync(CancellationToken) - C# / CompletableFuture onCloseAsync(CancellationToken) - Java` 無狀態服務執行個體即將正常關閉時，會呼叫 OnCloseAsync。 這可能在升級服務程式碼、因負載平衡而移動服務執行個體或偵測到暫時性失敗的時侯發生。 OnCloseAsync 可以用來安全地關閉任何資源、停止任何背景處理、完成外部狀態儲存或關閉現有的連接。
* `void OnAbort() - C# / void onAbort() - Java` 正要強制關閉無狀態服務執行個體時，會呼叫 OnAbort。 這個一般會在於節點上偵測到永久錯誤，或因內部失敗而 Service Fabric 無法可靠地管理服務執行個體生命週期時呼叫。

## <a name="stateful-service-replica-lifecycle"></a>具狀態服務複本生命週期

> [!NOTE]
> Java 中尚未支援具狀態 Reliable Services。
>
>

具狀態服務複本的生命週期比無狀態服務執行個體更為複雜。 除了開啟、關閉和中止事件，具狀態服務複本會在其生命週期內經歷角色變更。 當具狀態服務複本變更角色時，會觸發 `OnChangeRoleAsync` 事件︰

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync。 主要複本會獲得寫入狀態 (可建立並寫入可靠的集合)。 次要複本則會獲得讀取狀態 (只能從現有的可靠集合讀取)。 具狀態服務中的大部分工作會在主要複本執行。 次要複本可以執行唯讀驗證、產生報表、資料採礦或其他唯讀作業。

在具狀態服務中，只有主要複本具有狀態的寫入權限，因此通常在服務正在執行實際工作時。 只有在具狀態服務複本為主要複本時，才會執行具狀態服務中的 `RunAsync` 方法。 `RunAsync` 方法會在主要複本的角色變成非主要時，以及在關閉和中止事件期間取消。

使用 `OnChangeRoleAsync` 事件可讓您視複本角色執行工作，以及回應角色變更。

具狀態服務也會提供與無狀態服務相同的四個生命週期事件，並具有相同的語意和使用案例：

```csharp
* Task OnOpenAsync(IStatefulServicePartition, CancellationToken)
* Task OnCloseAsync(CancellationToken)
* void OnAbort()
```

## <a name="next-steps"></a>後續步驟
如需更多與 Service Fabric 相關的進階主題，請參閱下列文章：

* [設定具狀態的 Reliable Services](service-fabric-reliable-services-configuration.md)
* [Service Fabric 健康狀態簡介](service-fabric-health-introduction.md)
* [使用系統健康狀態報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [使用 Service Fabric 叢集資源管理員設定服務](service-fabric-cluster-resource-manager-configure-services.md)
