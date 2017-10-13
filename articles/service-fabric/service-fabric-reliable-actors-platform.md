---
title: "Service Fabric 上的 Reliable Actors | Microsoft Docs"
description: "說明 Reliable Actors 如何在 Reliable Services 上分層，以及如何使用 Service Fabric 平台的功能。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 43b3f758fe7017c0ec949ba6e28b76438cf1bc13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Reliable Acto 如何使用 Service Fabric 平台
本文說明 Reliable Actors 在 Azure Service Fabric 平台上的運作方式。 Reliable Actors 會在名為*動作項目服務*的具狀態可靠服務實作上裝載的架構中執行。 動作項目服務包含管理生命週期和您的動作項目用於發送之訊息所需的所有元件︰

* 動作項目執行階段會管理生命週期、記憶體回收，並強制執行單一執行緒存取。
* 動作項目服務遠端處理接聽程式會接受對動作項目進行的遠端存取呼叫，並將它們傳送到發送器，以路由傳送到適當的動作項目執行個體。
* 動作項目狀態供應器會包裝狀態供應器 (例如「可靠的集合」狀態供應器)，並提供配接器來進行動作項目狀態管理。

這些元件一起構成 Reliable Actor 架構。

## <a name="service-layering"></a>將服務分層
由於動作項目服務本身是一個 Reliable Service，因此 Reliable Services 的所有 [應用程式模型](service-fabric-application-model.md)、生命週期、[封裝](service-fabric-package-apps.md)、[部署](service-fabric-deploy-remove-applications.md)、升級及調整概念都會以相同方式套用到動作項目服務。

![將動作項目服務分層][1]

上圖顯示 Service Fabric 應用程式架構和使用者程式碼之間的關聯性。 藍色項目代表 Reliable Services 應用程式架構、橘色代表 Reliable Actor 架構，而綠色代表使用者程式碼。

在 Reliable Services 中，您的服務會繼承 `StatefulService` 類別。 這個類別本身是衍生自 `StatefulServiceBase` (或無狀態服務的 `StatelessService`)。 在 Reliable Actors 中，您會使用動作項目服務。 動作項目服務是 `StatefulServiceBase` 類別的不同實作，此類別會實作動作項目執行所在的動作項目模式。 由於動作項目服務本身只是 `StatefulServiceBase` 的實作，因此，您可以自行撰寫衍生自 `ActorService` 的服務，並以您在繼承 `StatefulService` 時所使用的相同方式來實作服務層級功能，例如︰

* 服務備份與還原。
* 適用於所有動作項目的共用功能，例如斷路器。
* 遠端處理程序會在動作項目服務本身，以及每個個別動作項目上進行呼叫。

> [!NOTE]
> Java/Linux 上目前不支援具狀態服務。

### <a name="using-the-actor-service"></a>使用動作項目服務
動作項目執行個體具有其執行所在之動作項目服務的存取權。 透過動作項目服務，動作項目執行個體可以程式設計方式取得服務內容。 服務內容包含分割區識別碼、服務名稱、應用程式名稱及其他 Service Fabric 平台特定的資訊：

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```


就像所有 Reliable Services，動作項目服務必須在 Service Fabric 執行階段，利用某個服務類型來註冊。 為了讓動作項目服務執行您的動作項目執行個體，也必須向動作項目服務註冊動作項目類型。 `ActorRuntime` 註冊方法會替動作項目執行這項工作。 在最簡單的情況下，您只需註冊動作項目類型，並隱含地使用具備預設設定的動作項目服務︰

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

或者，您可以使用註冊方法提供的 Lambda，來建構自己的動作項目服務。 您可以設定動作項目服務，以及明確地建構您的動作項目執行個體，您可以透過其建構函式，將相依性插入動作項目︰

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

### <a name="actor-service-methods"></a>動作項目服務方法
動作項目服務會實作 `IActorService` (C#) 或 `ActorService` (Java)，而它會接著實作 `IService` (C#) 或 `Service` (Java)。 這是 Reliable Services 遠端處理功能所使用的介面，能夠在服務方法上進行遠端程序呼叫。 它包含的服務層級方法可使用服務遠端處理功能從遠端呼叫。

#### <a name="enumerating-actors"></a>列舉動作項目
動作項目服務允許用戶端列舉服務所裝載之動作項目的相關中繼資料。 因為動作項目服務是已資料分割的具狀態服務，所以會針對每個分割區執行列舉。 由於每個分割區可能包含許多動作項目，因此，列舉會以一組分頁式結果形式傳回。 頁面會以迴圈方式讀取，直到讀取所有頁面為止。 下列範例示範如何在動作項目服務的其中一個分割區中，建立所有作用中動作項目的清單︰

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>刪除動作項目
動作項目服務也會提供用來刪除動作項目的函式︰

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

如需刪除動作項目及其狀態的詳細資訊，請參閱[動作項目生命週期文件](service-fabric-reliable-actors-lifecycle.md)。

### <a name="custom-actor-service"></a>自訂動作項目服務
您可以使用動作項目註冊 lambda，註冊自 `ActorService` (C#) 和 `FabricActorService` (Java) 延伸的自訂動作項目服務。 在此自訂動作項目服務中，您可以撰寫繼承 `ActorService` (C#) 或 `FabricActorService` (Java) 的服務，以實作自己的服務層級功能。 自訂動作項目服務會繼承來自 `ActorService` (C#) 或 `FabricActorService` (Java) 的所有動作項目執行階段功能，並可用來實作您自己的服務方法。

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

#### <a name="implementing-actor-backup-and-restore"></a>實作動作項目備份與還原
 在下列範例中，自訂動作項目服務會利用已經存在於 `ActorService`中的遠端處理接聽程式，藉以公開用來備份動作項目資料的方法：

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```


在此範例中，`IMyActorService` 是遠端處理協定，它會實作 `IService` (C#) 與 `Service` (Java)，然後透過 `MyActorService` 來實作。 藉由新增這個遠端處理協定，透過使用 `ActorServiceProxy` 建立遠端 Proxy，`IMyActorService` 上的方法現在也可供用戶端使用：

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

## <a name="application-model"></a>應用程式模型
動作項目服務是 Reliable Services，因此應用程式模型是一樣的。 不過，動作項目架構建置工具可為您產生某些應用程式模型檔案。

### <a name="service-manifest"></a>服務資訊清單
動作項目架構建置工具會自動產生動作項目服務的 ServiceManifest.xml 檔案內容。 此檔案包括：

* 動作項目服務類型。 類型名稱是根據您的動作項目專案名稱來產生。 根據動作項目上的持續性屬性，也會據以設定 HasPersistedState 旗標。
* 程式碼封裝。
* 組態封裝。
* 資源和端點。

### <a name="application-manifest"></a>應用程式資訊清單
動作項目架構建置工具會自動建立適用於您動作項目服務的預設服務定義。 建置工具會填入預設服務屬性︰

* 複本集數量取決於動作項目上的持續性屬性。 每當動作項目上的持續性屬性變更時，預設服務定義中的複本集數量會據以重設。
* 分割區配置和範圍會利用完整的 Int64 索引鍵範圍設定為「平均的 Int64 」。

## <a name="service-fabric-partition-concepts-for-actors"></a>動作項目的 Service Fabric 資料分割概念
動作項目服務是已資料分割的具狀態服務。 動作項目服務的每個分割區都包含一組動作項目。 服務分割區會自動散佈於 Service Fabric 中的多個節點上。 結果就是散佈動作項目執行個體。

![動作項目的資料分割和散佈][5]

您可以使用不同的分割區配置和分割區索引鍵範圍來建立 Reliable Services。 動作項目服務會搭配完整的 Int64 索引鍵範圍使用 Int64 資料分割配置，來將動作項目對應到分割區。

### <a name="actor-id"></a>動作項目識別碼
服務中建立的每個動作項目都具有與它相關聯的唯一識別碼，由 `ActorId` 類別來表示。 `ActorId` 是不透明的識別碼值，可藉由產生隨機識別碼，在服務分割區上平均散佈動作項目。

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


每個 `ActorId` 雜湊為 int64。 這就是為什麼動作項目服務必須搭配完整的 Int64 索引鍵範圍使用 Int64 資料分割配置的原因。 不過，自訂識別碼值可用於 `ActorID`，包括 GUID/UUID、字串和 Int64。

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

您使用 GUID/UUID 和字串時，已將值雜湊為 Int64。 不過，在您將 Int64 明確提供給 `ActorId`之前，Int64 將會直接對應到分割區，而不需進一步雜湊。 您可以使用這項技術控制要將動作項目放置於哪個分割區。

## <a name="actor-using-remoting-v2-stack"></a>使用 Remoting V2 堆疊的動作項目
透過 2.8 NuGet 套件，使用者現在可以使用 Remoting V2 堆疊，它的效能更好，並提供像是自訂序列化等功能。 Remoting V2 與現有的 Remoting 堆疊 (我們稱現在的是 V1 Remoting 堆疊) 不相容。

若要使用 Remoting V2 堆疊，必須進行下列變更。
 1. 在動作項目介面上新增下列組件屬性。
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. 建置和升級 ActorService 和動作項目用戶端專案來開始使用 V2 堆疊。

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>動作項目服務升級至 Remoting V2 堆疊，不會影響服務可用性。
此項變更是 2 個步驟的升級作業。 依照列出的步驟順序進行。

1.  在動作項目介面上新增下列組件屬性。 此屬性將會為 ActorService 啟動兩個接聽程式：V1 (現有) 與 V2 接聽程式。 透過下列變更升級 ActorService。

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. 完成上述升級之後，升級 ActorClients。
此步驟可確保動作項目 Proxy 使用 Remoting V2 堆疊。

3. 此為選用步驟。 變更上述屬性以移除 V1 接聽程式。

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>後續步驟
* [動作項目狀態管理](service-fabric-reliable-actors-state-management.md)
* [動作項目生命週期與記憶體回收](service-fabric-reliable-actors-lifecycle.md)
* [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET 範例程式碼 (英文)](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 範例程式碼 (英文)](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
