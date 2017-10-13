---
title: "使用 Java 建立您第一個可靠的 Azure 微服務 | Microsoft Docs"
description: "概述使用無狀態與具狀態服務來建立 Microsoft Azure Service Fabric 應用程式。"
services: service-fabric
documentationcenter: java
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 1ebabe4844732412e04bab8c277f7ebbc4a5737c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-reliable-services"></a>開始使用 Reliable Service
> [!div class="op_single_selector"]
> * [Windows 上的 C# ](service-fabric-reliable-services-quick-start.md)
> * [在 Linux 上使用 Java](service-fabric-reliable-services-quick-start-java.md)
>
>

本文說明 Azure Service Fabric Reliable Services 的基本概念，並將逐步引導您建立及部署以 Java 撰寫的簡單 Reliable Services 應用程式。 此 Microsoft Virtual Academy 影片也示範如何建立無狀態的 Reliable Service：<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>安裝與設定
開始之前，確定機器上已設定 Service Fabric 開發環境。
如果您需要加以設定，請移至[在 Mac 上開始使用](service-fabric-get-started-mac.md)或[在 Linux 上開始使用](service-fabric-get-started-linux.md)。

## <a name="basic-concepts"></a>基本概念
若要開始使用 Reliable Services，您只需要了解幾個基本概念：

* **服務類型**：這是您的服務實作。 它是由您撰寫的類別定義，它會擴充 `StatelessService` 與其中使用的任何其他程式碼或相依性，以及名稱與版本號碼。
* **具名服務執行個體**：若要執行您的服務，可以建立您服務類型的具名執行個體，很像您建立類別類型的物件執行個體一樣。 服務執行個體實際上就是您所撰寫服務類型的物件具現化。
* **服務主機**：您建立的具名服務執行個體需要在主機內部執行。 服務主機只是您服務的執行個體可執行所在的程序。
* **服務註冊**：註冊可將所有項目結合在一起。 服務類型必須在服務主機的 Service Fabric 執行階段中註冊，以允許 Service Fabric 建立其執行個體來執行。  

## <a name="create-a-stateless-service"></a>建立無狀態服務
從建立 Service Fabric 應用程式開始著手。 適用於 Linux 的 Service Fabric SDK 包含 Yeoman 產生器，可提供具無狀態服務之 Service Fabric 應用程式的樣板。 執行下列 Yeoman 命令開始作業：

```bash
$ yo azuresfjava
```

遵循指示建立**可靠的無狀態服務**。 本教學課程中，將應用程式命名為 HelloWorldApplication，將服務命名為 HelloWorld。 結果會包含 `HelloWorldApplication` 和 `HelloWorld` 的目錄。

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>實作服務
開啟 **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**。 這個類別會定義服務類型，而且可以執行任何程式碼。 服務 API 為您的程式碼提供兩個進入點：

* 開放式的進入點方法 (稱為 `runAsync()`)，您可以在這裡開始執行任何工作負載，包括長時間執行的計算工作負載。

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* 通訊進入點，您可以在這裡插入選擇的通訊堆疊。 這就是您可以開始接收來自使用者和其他服務要求的地方。

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

在本教學課程中，我們會著重在 `runAsync()` 進入點方法。 這就是您可以立即開始執行程式碼的地方。

### <a name="runasync"></a>RunAsync
當服務的執行個體已放置並且可以執行時，平台會呼叫這個方法。 針對無狀態服務，這僅表示開啟服務執行個體的時間。 會提供取消語彙基元以協調服務執行個體何時需要關閉。 在 Service Fabric 中，服務執行個體的開啟/關閉循環可能會在整個服務存留期中發生許多次。 發生這種情形的原因有很多，包括：

* 系統可能為了資源平衡移動您的服務執行個體。
* 在您的程式碼中發生錯誤。
* 應用程式或系統已升級。
* 基礎硬體發生中斷。

此協調流程是由 Service Fabric 管理，可讓您的服務維持高度可用且正確平衡。

`runAsync()` 不應該同步封鎖。 您的 runAsync 實作應該傳回 CompletableFuture，以允許執行階段繼續執行。 如果您的工作負載需要實作長時間執行的工作，則該工作應該在 CompletableFuture 內完成。

#### <a name="cancellation"></a>取消
取消您的工作負載是由所提供的取消語彙基元協調的協同努力。 系統會先等待您的工作結束 (依據成功完成、取消或錯誤)，然後才繼續執行。 系統要求取消時，務必接受取消權杖、完成任何工作，並儘快結束 `runAsync()`。 下列範例示範如何處理取消事件：

```java
    @Override
    protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

        // TODO: Replace the following sample code with your own logic
        // or remove this runAsync override if it's not needed in your service.

        CompletableFuture.runAsync(() -> {
          long iterations = 0;
          while(true)
          {
            cancellationToken.throwIfCancellationRequested();
            logger.log(Level.INFO, "Working-{0}", ++iterations);

            try
            {
              Thread.sleep(1000);
            }
            catch (IOException ex) {}
          }
        });
    }
```

### <a name="service-registration"></a>服務註冊
會取消必須在 Service Fabric 執行階段註冊。 服務類型是在 `ServiceManifest.xml` 中以及實作 `StatelessService` 的服務類別中定義。 服務註冊是在程序主要進入點中執行。 在此範例中，程序的主要進入點是 `HelloWorldServiceHost.java`：

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="run-the-application"></a>執行應用程式

Yeoman 樣板包含可建置應用程式的 gradle 指令碼，以及可部署和移除應用程式的 bash 指令碼。 若要執行應用程式，先建置含 gradle的應用程式︰

```bash
$ gradle
```

這會產生可使用 Service Fabric CLI 來部署的 Service Fabric 應用程式套件。

### <a name="deploy-with-service-fabric-cli"></a>使用 Service Fabric CLI 部署

Install.sh 指令碼包含部署應用程式套件所需的 Service Fabric CLI 命令。 請執行 install.sh 指令碼來部署應用程式。

```bash
$ ./install.sh
```

## <a name="next-steps"></a>後續步驟

* [開始使用 Service Fabric CLI](service-fabric-cli.md)
