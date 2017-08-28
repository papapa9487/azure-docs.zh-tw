---
title: "Azure Service Fabric DNS 服務 | Microsoft Docs"
description: "使用 Service Fabric 的 DNS 服務來從叢集內部探索微服務。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 9871bc5aa4e74ab0faef401d67c4e9558eb5e14b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric 中的 DNS 服務
「DNS 服務」是一個選用的系統服務，您可以在叢集中啟用以使用 DNS 通訊協定來探索其他服務。

許多服務 (特別是容器化服務) 都可以有現有的 URL 名稱，因此能夠使用標準 DNS 通訊協定 (而不是「命名服務」通訊協定) 來解析這些名稱，特別是在「隨即轉移」案例中。 DNS 服務可讓您將 DNS 服務對應到某個服務名稱，再由此解析端點 IP 位址。 

DNS 服務會將 DNS 名稱對應到服務名稱，接著服務名稱會由命名服務解析並傳回服務端點。 服務的 DNS 名稱是在建立時提供的。 

![服務端點][0]

## <a name="enabling-the-dns-service"></a>啟用 DNS 服務
首先，您必須在叢集啟用 DNS 服務。 取得您想要部署之叢集的範本。 您可以使用[範例範本](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)或建立 Resource Manager 範本。 您可以使用下列步驟來啟用 DNS 服務：

1. 檢查 `Microsoft.ServiceFabric/clusters` 資源的 `apiversion` 是否已設定為 `2017-07-01-preview`，如果不是，請加以更新，如下列程式碼片段所示：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 現在，在 `fabricSettings` 區段之後新增下列 `addonFeatures` 區段以啟用 DNS 服務，如下列程式碼片段所示： 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. 一旦您使用前面的變更將叢集範本進行更新之後，將它們加以套用，使升級完成。 完成之後，在 Service Fabric Explorer 的系統服務區段下，就有名為 `fabric:/System/DnsService` 的 DNS 系統服務在叢集中執行。 

或者，您可以在叢集建立時，透過入口網站啟用 DNS 服務。 核取 `Cluster configuration`功能表中的 `Include DNS service` 方塊可啟用 DNS 服務，如下列螢幕擷取畫面所示：

![透過入口網站啟用 DNS 服務][2]


## <a name="setting-the-dns-name-for-your-service"></a>為您的服務設定 DNS 名稱
一旦 DNS 服務已在您的叢集中執行，您現在便可以在 `ApplicationManifest.xml` 中透過宣告為預設服務設定 DNS 名稱，或透過 Powershell 命令為服務設定 DNS 名稱。

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>在 ApplicationManifest.xml 中為預設服務設定 DNS 名稱
在 Visual Studio 或慣用的編輯器中開啟您的專案，然後開啟 `ApplicationManifest.xml` 檔案。 移至預設服務區段，然後為每個服務新增 `ServiceDnsName` 屬性。 下列範例示範如何將服務的 DNS 名稱設定為 `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
部署完應用程式之後，在 Service Fabric Explorer 中的服務執行個體會顯示此執行個體的 DNS 名稱，如下圖所示： 

![服務端點][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>使用 Powershell 為服務設定 DNS 名稱
您可以在建立服務時，使用 `New-ServiceFabricService` Powershell 為服務設定 DNS 名稱。 下列範例會建立 DNS 名稱為 `service1.application1` 的新無狀態服務

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>在您的服務中使用 DNS
如果您部署多個服務，您便可以使用 DNS 名稱來尋找要通訊對象的其他服務端點。 DNS 服務僅適用於無狀態服務，因為 DNS 通訊協定無法與具狀態服務通訊。 針對具狀態服務，您可以使用 HTTP 呼叫的內建反向 Proxy 來呼叫特定的服務分割區。

下列程式碼會示範如何呼叫另一個服務，這只是一般的 HTTP 呼叫，其中您會提供連接埠和任何選用路徑作為 URL 的一部分。

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>後續步驟
若要深入了解叢集內的服務通訊，請參閱[連接服務並與其進行通訊](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG

