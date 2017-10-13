---
title: "協助保護 Azure Service Fabric 中服務的通訊安全 | Microsoft Docs"
description: "如何協助保護於 Azure Service Fabric 叢集中所執行之可靠服務的通訊安全概觀。"
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: 53119244f8f09c0c6c43f43761af1cc074f8d0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>協助保護 Azure Service Fabric 中服務的通訊安全
> [!div class="op_single_selector"]
> * [Windows 上的 C# ](service-fabric-reliable-services-secure-communication.md)
> * [在 Linux 上使用 Java](service-fabric-reliable-services-secure-communication-java.md)
>
>

安全性是通訊最為重視的其中一個部分。 Reliable Services 應用程式架構會提供可用來改善安全性的一些預先建置通訊堆疊和工具。 本文會討論如何在使用服務遠端處理和 Windows Communication Foundation (WCF) 通訊堆疊時改善安全性。

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>協助保護使用服務遠端處理時的服務安全
我們將使用現有[範例](service-fabric-reliable-services-communication-remoting.md)說明如何設定 Reliable Services 的遠端處理功能。 若要協助保護使用服務遠端處理時的服務安全，請遵循下列步驟︰

1. 建立 `IHelloWorldStateful`介面，這個介面會定義將在您的服務上用於遠端程序呼叫的方法。 您的服務將使用在 `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` 命名空間中宣告的 `FabricTransportServiceRemotingListener`。 這是提供遠端功能的 `ICommunicationListener` 實作。

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. 新增接聽程式設定和安全性認證。

    確定您想要用來協助保護服務通訊安全的憑證已安裝在叢集的所有節點上。 有兩種方式可提供接聽程式設定和安全性認證：

   1. 直接在服務程式碼中提供它們：

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. 使用 [組態封裝](service-fabric-application-model.md)提供它們：

       在 settings.xml 檔案中新增 `TransportSettings` 區段。

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       在此情況下， `CreateServiceReplicaListeners` 方法看起來像這樣：

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        如果您在 settings.xml 檔案中新增 `TransportSettings` 區段，則 `FabricTransportRemotingListenerSettings ` 預設會載入此區段中的所有設定。

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        在此情況下， `CreateServiceReplicaListeners` 方法看起來像這樣：

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. 如果在安全服務上使用遠端堆疊來呼叫方法，而不是使用 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 類別來建立服務 Proxy，請使用 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`。 傳入包含 `SecurityCredentials` 的 `FabricTransportRemotingSettings`。

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    如果用戶端程式碼正在當作服務一部分執行，則可以從 settings.xml 檔案中載入 `FabricTransportRemotingSettings` 。 建立與服務程式碼類似的 HelloWorldClientTransportSettings 區段，如前所示。 對用戶端程式碼進行下列變更：

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    如果用戶端未當作服務一部分執行，則您可以在 client_name.exe 所在的同一位置中建立 client_name.settings.xml 檔案。 然後在該檔案中建立 TransportSettings 區段。

    與此服務類似，如果您在用戶端 settings.xml/client_name.settings.xml 中新增 `TransportSettings` 區段，則 `FabricTransportRemotingSettings` 預設會載入此區段中的所有設定。

    在該情況下，先前的程式碼甚至會更進一步地簡化：  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>協助保護使用 WCF 通訊堆疊時的服務安全
我們將使用現有[範例](service-fabric-reliable-services-communication-wcf.md)說明如何設定 Reliable Services 的 WCF 通訊堆疊。 若要協助保護使用 WCF 通訊堆疊時的服務安全，請遵循下列步驟 ︰

1. 對於此服務，您需要協助保護所建立之 WCF 通訊接聽程式的安全 (`WcfCommunicationListener`)。 若要這樣做，請修改 `CreateServiceReplicaListeners` 方法。

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. 在用戶端，於先前[範例](service-fabric-reliable-services-communication-wcf.md)中建立的 `WcfCommunicationClient` 類別會保持不變。 但是，您需要覆寫 `WcfCommunicationClientFactory` 的 `CreateClientAsync` 方法：

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    使用 `SecureWcfCommunicationClientFactory` 來建立 WCF 通訊用戶端 (`WcfCommunicationClient`)。 使用用戶端來叫用服務方法。

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>後續步驟
* [在 Reliable Services 中搭配 OWIN 使用 Web API](service-fabric-reliable-services-communication-webapi.md)
