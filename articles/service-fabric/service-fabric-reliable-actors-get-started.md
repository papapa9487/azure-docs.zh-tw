---
title: "使用 C# 建立您的第一個動作項目型 Azure 微服務 | Microsoft Docs"
description: "本教學課程將引導您使用 Service Fabric Reliable Actors，建立、偵錯及部署簡易動作項目型服務的步驟。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 3f447e049ccd33c77f422e8aa703ad6646f9ffa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-reliable-actors"></a>開始使用 Reliable Actors
> [!div class="op_single_selector"]
> * [Windows 上的 C# ](service-fabric-reliable-actors-get-started.md)
> * [在 Linux 上使用 Java](service-fabric-reliable-actors-get-started-java.md)
> 
> 

本文說明 Azure Service Fabric Reliable Actors 的基本概念，並將逐步引導您在 Visual Studio 中建立、偵錯及部署簡單的 Reliable Actor 應用程式。

## <a name="installation-and-setup"></a>安裝與設定
開始之前，確定機器上已設定 Service Fabric 開發環境。
如果需要加以設定，請參閱 [如何設定開發環境](service-fabric-get-started.md)的詳細指示。

## <a name="basic-concepts"></a>基本概念
若要開始使用 Reliable Actors，您只需要了解幾個基本概念：

* **動作項目服務**。 Reliable Actors 封裝在可在 Service Fabric 基礎結構內部署的 Reliable Services 中。 動作項目執行個體會在指定的服務執行個體中啟動。
* **動作項目註冊**。 和 Reliable Services 一樣，Reliable Actor 服務必須向 Service Fabric 執行階段註冊。 此外，動作項目類型必須向 Actor 執行階段註冊。
* **動作項目介面**。 動作項目介面用於定義動作項目的強型別公用介面。 在 Reliable Actor 模型術語中，動作項目介面定義動作項目可以了解並處理的訊息類型。 其他的動作項目或用戶端應用程式會使用動作項目介面將訊息「傳送」(非同步) 給動作項目。 Reliable Actors 可實作多個介面。
* **ActorProxy 類別**。 用戶端應用程式會使用 ActorProxy 類別來叫用透過動作項目介面公開的方法。 ActorProxy 類別提供兩個重要的功能：
  
  * 名稱解析︰它能夠在叢集中找到動作項目 (尋找裝載動作項目的叢集節點)。
  * 處理失敗：它可以重試方法叫用並重新解析動作項目位置，例如在需要動作項目重新定位至叢集中另一個節點失敗後進行。

值得一提的是下列與動作項目介面相關的規則︰

* 動作項目介面方法無法多載。
* 動作項目介面方法不能有 out、ref 或選擇性參數。
* 不支援泛型介面。

## <a name="create-a-new-project-in-visual-studio"></a>在 Visual Studio 中建立新專案
以管理員身分啟動 Visual Studio 2015 或 Visual Studio 2017，並建立新的 Service Fabric 應用程式專案：

![適用於 Visual Studio 的 Service Fabric 工具 - 新專案][1]

在下一個對話方塊中，您可選擇您要建立的專案類型。

![Service Fabric 專案範本][5]

讓我們為 HelloWorld 專案使用 Service Fabric Reliable Actors 服務。

建立方案之後，您應該會看到下列結構：

![Service Fabric 專案結構][2]

## <a name="reliable-actors-basic-building-blocks"></a>Reliable Actors 項目基本建置組塊
典型的 Reliable Actors 方案是由 3 個專案組成：

* **應用程式專案 (MyActorApplication)**。 此專案會將所有的服務封裝在一起部署。 其包含了用於管理應用程式的 ApplicationManifest.xml  與 PowerShell 指令碼。
* **介面專案 (MyActor.Interfaces)**。 此專案包含動作項目的介面定義。 在 MyActor.Interfaces 專案中，您可以定義將由方案中的動作項目使用者介面。 可以在任何專案中使用任何名稱定義動作項目介面，不過該介面會定義由動作項目實作與呼叫動作項目的用戶端所共用的動作項目合約，因此通常適合在不同於動作項目實作的組件中定義該合約，並可由多個其他專案共用。

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **動作項目服務專案 (MyActor)**。 此專案用於定義即將裝載動作項目的 Service Fabric 服務。 它包含動作項目的實作。 動作項目實作是衍生自基底類型 `Actor` 的類別，可實作 MyActor.Interfaces 專案中所定義的介面。 動作項目類別也必須實作建構函式來接受 `ActorService` 執行個體和 `ActorId`，並將它們傳遞至基底 `Actor` 類別。 這可允許平台相依性的建構函式相依性插入。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

必須在 Service Fabric 執行階段中以某個服務類型註冊動作項目服務。 為了讓動作項目服務執行您的動作項目執行個體，也必須向動作項目服務註冊動作項目類型。 `ActorRuntime` 註冊方法會替動作項目執行這項工作。

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

如果您從在 Visual Studio 中建立新專案開始，且您只有一個動作項目定義，則該註冊預設會包含在 Visual Studio 產生的程式碼中。 如果您在服務中定義其他的動作，您必須使用下列項目新增動作項目註冊：

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> Service Fabric Actors 執行階段會發出某些 [事件和與動作項目方法相關的效能計數器](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)。 這些項目對於診斷與效能監視很有幫助。
> 
> 

## <a name="debugging"></a>Debugging
Visual Studio 專用的 Service Fabric 工具支援在本機機器上偵錯。 您可以點擊 F5 鍵開始偵錯工作階段。 Visual Studio 會建置封裝 (如有必要)。 它也會在本機 Service Fabric 叢集上部署應用程式並附加偵錯工具。

在部署的過程中，您可在 [輸出]  視窗中查看進度。

![Service Fabric 偵錯輸出視窗][3]

## <a name="next-steps"></a>後續步驟
深入了解 [Reliable Actor 如何使用 Service Fabric 平台](service-fabric-reliable-actors-platform.md)。

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
