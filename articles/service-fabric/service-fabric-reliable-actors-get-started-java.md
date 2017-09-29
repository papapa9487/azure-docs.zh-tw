---
title: "使用 Java 建立您的第一個動作項目型 Azure 微服務 | Microsoft Docs"
description: "本教學課程將引導您使用 Service Fabric Reliable Actors，建立、偵錯及部署簡易動作項目型服務的步驟。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: d31dc8ab-9760-4619-a641-facb8324c759
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 288f1ed1016f50031065e66444d2562427194dc7
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---
# <a name="getting-started-with-reliable-actors"></a>開始使用 Reliable Actors
> [!div class="op_single_selector"]
> * [Windows 上的 C# ](service-fabric-reliable-actors-get-started.md)
> * [在 Linux 上使用 Java](service-fabric-reliable-actors-get-started-java.md)
> 
> 

本文說明 Azure Service Fabric Reliable Actors 的基本概念，並將逐步引導您在 Java 中建立及部署簡單的 Reliable Actors 應用程式。

## <a name="installation-and-setup"></a>安裝與設定
開始之前，確定機器上已設定 Service Fabric 開發環境。
如果您需要加以設定，請移至[在 Mac 上開始使用](service-fabric-get-started-mac.md)或[在 Linux 上開始使用](service-fabric-get-started-linux.md)。

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

## <a name="create-an-actor-service"></a>建立動作項目服務
以建立新 Service Fabric 應用程式的方式開始。 適用於 Linux 的 Service Fabric SDK 包含 Yeoman 產生器，可提供具無狀態服務之 Service Fabric 應用程式的樣板。 執行下列 Yeoman 命令開始作業：

```bash
$ yo azuresfjava
```

遵循指示建立 **Reliable Actors 服務**。 本教學課程中，將應用程式命名為 HelloWorldActorApplication，將動作項目命名為 HelloWorldActor。 將會建立下列樣板：

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Reliable Actors 項目基本建置組塊
稍早所述的基本概念轉化為 Reliable Actors 服務的基本建置組塊。

### <a name="actor-interface"></a>動作項目介面
包含動作項目的介面定義。 這個介面定義由動作項目實作與呼叫動作項目的用戶端所共用的動作項目合約，因此通常適合在不同於動作項目實作的地方定義該合約，並可由多個其他服務或用戶端應用程式共用。

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`：

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>動作項目服務
這包含您的動作項目實作和動作項目註冊碼。 動作項目類別會實作動作項目介面。 這是您的動作項目工作之處。

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`：

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>動作項目註冊
必須在 Service Fabric 執行階段中以某個服務類型註冊動作項目服務。 為了讓動作項目服務執行您的動作項目執行個體，也必須向動作項目服務註冊動作項目類型。 `ActorRuntime` 註冊方法會替動作項目執行這項工作。

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`：

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Test client
這是簡單的測試用戶端應用程式，可以和 Service Fabric 應用程式分開執行，以測試您的動作項目服務。 這是 ActorProxy 可用來啟動和與動作項目執行個體通訊的位置範例。 它不會與您的服務一同部署。

### <a name="the-application"></a>應用程式
最後，應用程式會將動作項目服務以及您未來可能會加入的其他任何服務封裝在一起以便部署。 它包含了 ApplicationManifest.xml 以及動作項目服務套件的預留位置。

## <a name="run-the-application"></a>執行應用程式

Yeoman 樣板包含可建置應用程式的 gradle 指令碼，以及可部署和移除應用程式的 bash 指令碼。 若要部署應用程式，請先使用 gradle 來建置應用程式︰

```bash
$ gradle
```

這會產生可以使用 Service Fabric Azure CLI 工具來部署的 Service Fabric 應用程式套件。

### <a name="deploy-service-fabric-cli"></a>部署 Service Fabric CLI

Install.sh 指令碼包含部署應用程式封裝所需的 Service Fabric CLI (sfctl) 命令。
請執行 install.sh 指令碼來部署應用程式。

```bash
$ ./install.sh
```

## <a name="next-steps"></a>後續步驟

* [開始使用 Service Fabric CLI](service-fabric-cli.md)

