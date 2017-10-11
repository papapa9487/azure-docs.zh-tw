---
title: "協助保護 Azure Service Fabric 中服務的通訊安全 | Microsoft Docs"
description: "如何協助保護於 Azure Service Fabric 叢集中所執行之可靠服務的通訊安全概觀。"
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: c4634e3d8efb1745fffcfe3e647e43d867038716
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>協助保護 Azure Service Fabric 中服務的通訊安全
> [!div class="op_single_selector"]
> * [Windows 上的 C# ](service-fabric-reliable-services-secure-communication.md)
> * [在 Linux 上使用 Java](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>協助保護使用服務遠端處理時的服務安全
我們將使用現有 [範例](service-fabric-reliable-services-communication-remoting-java.md) 以說明如何設定可靠服務的遠端處理功能。 若要協助保護使用服務遠端處理時的服務安全，請遵循下列步驟︰

1. 建立 `HelloWorldStateless`介面，這個介面會定義將在您的服務上用於遠端程序呼叫的方法。 您的服務將使用在 `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` 封裝中宣告的 `FabricTransportServiceRemotingListener`。 這是提供遠端功能的 `CommunicationListener` 實作。

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. 新增接聽程式設定和安全性認證。

    確定您想要用來協助保護服務通訊安全的憑證已安裝在叢集的所有節點上。 有兩種方式可提供接聽程式設定和安全性認證：

   1. 使用 [組態封裝](service-fabric-application-model.md)提供它們：

       在 settings.xml 檔案中新增 `TransportSettings` 區段。

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       在此情況下， `createServiceInstanceListeners` 方法看起來像這樣：

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        如果您在 settings.xml 檔案中新增 `TransportSettings` 區段，而沒有任何前置詞，則 `FabricTransportListenerSettings` 預設會載入此區段中的所有設定。

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        在此情況下， `CreateServiceInstanceListeners` 方法看起來像這樣：

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. 如果在安全服務上使用遠端堆疊來呼叫方法，而不是使用 `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` 類別來建立服務 Proxy，請使用 `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`。

    如果用戶端程式碼正在當作服務一部分執行，則可以從 settings.xml 檔案中載入 `FabricTransportSettings` 。 建立與服務程式碼類似的 TransportSettings 區段，如前所示。 對用戶端程式碼進行下列變更：

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
