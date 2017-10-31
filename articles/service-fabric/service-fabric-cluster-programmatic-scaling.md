---
title: "Azure Service Fabric 程式設計調整 | Microsoft Docs"
description: "以程式設計方式根據自訂觸發程序相應縮小或放大 Azure Service Fabric 叢集"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: mikerou
ms.openlocfilehash: 3d123a3d06420194d2918b71c98152cd2ea03457
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2017
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>以程式設計方式調整 Service Fabric 叢集 

在關於[叢集調整](./service-fabric-cluster-scale-up-down.md)的文件中探討了 Azure Service Fabric 叢集調整的基本概念。 該文章探討如何以虛擬機器擴展集為基礎來建置 Service Fabric 叢集，以及如何以手動方式或自動調整規則來加以調整。 本文件則探討如何以程式設計方法讓 Azure 調整作業能夠配合更進階的案例。 

## <a name="reasons-for-programmatic-scaling"></a>以程式設計方式調整的原因
在許多案例中，以手動方式或透過自動調整規則來調整是不錯的解決方案。 但在其他案例中，可能就不合適。 這些方法的可能缺點包括︰

- 手動調整需要登入系統，並明確地要求調整作業。 如果調整作業需要經常進行或難以預料會在何時進行，這個方法可能就不是合適的解決方案。
- 自動調整規則在從虛擬機器擴展集內移除執行個體時，並不會自動從相關聯的 Service Fabric 叢集移除對於該節點的認識，除非該節點類型的持久性等級為銀級或金級。 自動調整規則會作用在擴展集層級 (而非 Service Fabric 層級)，所以自動調整規則會直接移除 Service Fabric 節點，而未將其正常關閉。 以這種方式粗糙地移除節點，會在相應縮小作業完成後留下「準刪除」的 Service Fabric 節點狀態。 個人 (或服務) 必須定期清除 Service Fabric 叢集中的已移除節點狀態。
  - 持久性等級為金級或銀級的節點類型會自動清除已移除的節點，因此不需要額外的清除動作。
- 雖然自動調整規則支援[許多計量](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)，但這組計量的數量仍然有限。 如果您的案例所需要的調整是以該組計量所未涵蓋的一些計量為基礎，則自動調整規則可能不是很好的選擇。

根據這些限制，您可能會想要實作自訂能力更大的自動調整模型。 

## <a name="scaling-apis"></a>調整 API
Azure API 的存在可讓應用程式以程式設計方式使用虛擬機器擴展集和 Service Fabric 叢集。 如果現有自動調整選項不適用於您的案例，這些 API 可讓您實作自訂調整邏輯。 

若要實作此「自製」自動調整功能，有一個方法是將新的無狀態服務新增至 Service Fabric 應用程式以管理調整作業。 在服務的 `RunAsync` 方法內，有一組觸發程序可以判斷是否需要調整 (包括檢查最大叢集大小和調整冷卻等參數)。   

用於虛擬機器擴展集互動 (檢查目前的虛擬機器執行個體數目並加以修改) 的 API 是 [Fluent Azure 管理計算程式庫](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/)。 Fluent 計算程式庫可提供方便使用的 API 來與虛擬機器擴展集互動。

若要與 Service Fabric 叢集本身互動，請使用 [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient)。

當然，調整程式碼不需要以服務的形式在想要調整的叢集中執行。 `IAzure` 和 `FabricClient` 都能遠端連線到其相關聯的 Azure 資源，因此，調整服務可以輕鬆地成為主控台應用程式或從 Service Fabric 應用程式外部執行的 Windows 服務。 

## <a name="credential-management"></a>認證管理
撰寫服務來處理調整的其中一項挑戰是，服務必須能夠不經互動式登入程序就存取虛擬機器擴展集資源。 如果調整服務是要修改自己的 Service Fabric 應用程式，存取 Service Fabric 叢集是很容易的事，但需要有認證才能存取擴展集。 若要登入，您可以使用以 [Azure CLI 2.0](https://github.com/azure/azure-cli) 建立的[服務主體](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)。

您可以透過下列步驟來建立服務主體︰

1. 以具有虛擬機器擴展集存取權的使用者身分登入 Azure CLI (`az login`)
2. 使用 `az ad sp create-for-rbac` 建立服務主體
    1. 記下 appId (在其他地方稱為「用戶端識別碼」)、名稱、密碼及租用戶，以供稍後使用。
    2. 您還需要訂用帳戶識別碼，若要檢視此識別碼，請使用 `az account list`

Fluent 計算程式庫可以使用這些認證來登入 (請注意，`IAzure` 這類核心 Fluent Azure 類型是在 [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) 套件中)，如下所示：

```C#
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

登入之後，可以透過 `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` 來查詢擴展集的執行個體計數。

## <a name="scaling-out"></a>相應放大
使用 Fluent Azure 計算 SDK 時，只需要幾個呼叫就能將執行個體新增至虛擬機器擴展集

```C#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

或者，也可以使用 PowerShell Cmdlet 來管理虛擬機器擴展集大小。 [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) 可以擷取虛擬機器擴展集物件。 目前的容量可透過 `.sku.capacity` 屬性來取得。 將容量變更為所需的值之後，便可以使用 [`Update-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) 命令來更新 Azure 中的虛擬機器擴展集。

和手動新增節點時一樣，若要啟動新的 Service Fabric 節點，您只需要新增擴展集執行個體，因為擴展集範本所含有的擴充功能可自動將新的執行個體加入 Service Fabric 叢集。 

## <a name="scaling-in"></a>相應縮小

相應縮小與相應放大類似。實際的虛擬機器擴展集變更幾乎完全相同。 但就如先前所討論的，Service Fabric 只會自動清除持久性為金級或銀級的已移除節點。 因此，在銅級持久性的相應縮小案例中，就必須與 Service Fabric 叢集互動，以關閉要移除的節點，然後移除其狀態。

關閉節點的準備工作涉及尋找要移除的節點 (最近期新增的節點) 並加以停用。 對於非種子節點，可以藉由比較 `NodeInstanceId` 來找到較新的節點。 

```C#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n => n.NodeInstanceId)
        .FirstOrDefault();
```

種子節點則不同，不一定要遵循較高的執行個體識別碼優先移除的慣例。

在找到要移除的節點後，即可使用和稍早相同的 `FabricClient` 執行個體和 `IAzure` 執行個體來加以停用並移除。

```C#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

與相應放大規模時相同，如果偏好使用指令碼處理方法，在這裡也可以使用修改虛擬機器擴展集容量的 PowerShell Cmdlet。 移除虛擬機器執行個體後，就可以移除 Service Fabric 節點狀態。

```C#
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="potential-drawbacks"></a>可能的缺點

如前面的程式碼片段所示，建立自己的調整服務就能對應用程式的調整行為握有最高程度的控制力與自訂能力。 這很適合用於需要精確控制應用程式相應縮小或放大之時機或方式的案例。不過，伴隨此控制能力而來的是程式碼會變得複雜。 使用這種方式就表示您必須擁有調整程式碼 (此程式碼很複雜)。

處理 Service Fabric 調整的方式取決於您的案例。 如果是不常見的調整，以手動方式新增或移除節點的能力或許就綽綽有餘。 若是更複雜的案例，能以程式設計方式進行調整的自動調整規則和 SDK 則可提供更強大的替代方案。

## <a name="next-steps"></a>後續步驟

若要開始實作您自己的自動調整邏輯，請先熟悉下列概念和實用的 API：

- [以手動方式或透過自動調整規則進行調整](./service-fabric-cluster-scale-up-down.md)
- [適用於 .NET 的 Fluent Azure 管理程式庫](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (適用於與 Service Fabric 叢集的基礎虛擬機器擴展集互動)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (適用於與 Service Fabric 叢集及其節點互動)
