---
title: "Azure 服務匯流排地理災害復原 | Microsoft Docs"
description: "如何使用地理區域，在 Azure 服務匯流排中進行容錯移轉並執行災害復原"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 49f2992245d694f85b7b1f1c34339f1445c9d699
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Azure 服務匯流排地理災害復原 (預覽)

當區域資料中心遇到停機時，最重要的是資料處理作業能夠繼續在不同的區域或資料中心運作。 因此，「地理災害復原」和「異地複寫」對於任何企業而言都是重要的功能。 Azure 服務匯流排支援命名空間層級的地理災害復原和異地複寫。 

地理災害復原預覽目前僅適用於兩個區域 (**美國中北部**和**美國中南部)**。

## <a name="outages-and-disasters"></a>中斷與災害

[將應用程式與服務匯流排中斷和災難隔絕的最佳做法](service-bus-outages-disasters.md)一文會區別「中斷」和「災害」，這很值得注意。 「中斷」是暫時無法使用 Azure 服務匯流排，而且會影響服務的某些元件，例如訊息存放區，或甚至整個資料中心。 不過，修正問題之後，服務匯流排就可再次使用。 中斷通常不會導致訊息或其他資料遺失。 這類中斷的範例可能是資料中心停電。

「災害」定義為永久或較長期遺失服務匯流排[縮放單位](service-bus-architecture.md#service-bus-scale-units)或資料中心。 資料中心不一定能再次使用，也可能會關閉數小時或數天。 這類災害的範例包括火災、水災或地震。 會變成永久的災害可能會導致某些訊息或其他資料遺失。 不過，在大部分情況下，應該不會遺失資料，而且在備份資料中心之後，就可以復原訊息。

Azure 服務匯流排的地理災害復原功能就是一個災害復原解決方案。 本文中所述的概念和工作流程適用於災害案例，不適用暫時性或暫時中斷。  

## <a name="basic-concepts-and-terms"></a>基本概念與術語

災害復原功能會實作中繼資料災害復原，並依賴主要和次要災害復原命名空間。 請注意，地理災害復原功能僅適用於[進階命名空間](service-bus-premium-messaging.md)。 您不需要進行任何連接字串變更，因為連接是透過別名建立的。

本文中使用下列術語：

-  別名：您的主要連接字串。

-  主要/次要命名空間：描述對應到別名的命名空間。 主要是「主動」且會接收訊息，次要則是「被動」且不會接收訊息。 這兩者間的中繼資料會進行同步處理，因此，這兩者均能順暢地接受訊息，而不需進行任何應用程式的程式碼變更。

-  中繼資料：代表 Azure 服務匯流排中的物件。 我們目前只支援中繼資料。

-  容錯移轉：啟用次要命名空間的程序。 一旦先前的主要命名空間再次變成可供使用時，您就必須從其中提取訊息，然後刪除該命名空間。 為了建立另一個容錯移轉，您會將新的次要命名空間新增至配對。 如果您想要在容錯移轉之後重複使用先前的主要命名空間，則必須先移除命名空間中的所有現有實體。 請一定要先收到所有訊息，再這麼做。

## <a name="failover-workflow"></a>容錯移轉工作流程

下一節是設定初始容錯移轉的完整程序概觀，並說明如何從該點取得進展。

![1][]

您會先設定主要和次要命名空間，然後建立配對。 此配對會為您提供可用來連接的別名。 由於您使用別名，因此不需變更連接字串。 只需將新的命名空間新增至您的容錯移轉配對。 最後，您必須新增某個觸發程序邏輯 (例如，某個會偵測命名空間是否無法使用，並起始容錯移轉的商務邏輯)。 您可以使用服務匯流排的[訊息瀏覽](message-browsing.md)功能來檢查命名空間可用性。

當您設定了監視和災害復原之後，就可以查看容錯移轉程序。 如果觸發程序會起始容錯移轉，或者您會手動起始容錯移轉，則有兩個必要步驟：

1. 萬一發生另一個中斷，您會希望能夠再次進行容錯移轉。 因此，請設定第二個被動命名空間，並更新配對。 
2. 一旦新的命名空間可供使用之後，從先前的主要命名空間提取訊息。 在這之後，可重複使用或刪除舊的主要命名空間。

![2][]

## <a name="set-up-disaster-recovery"></a>設定災害復原

本節描述如何建置您自己的服務匯流排地理災害復原碼。 若要這樣做，您需要在獨立的位置中有兩個命名空間，例如，美國南部和美國中北部。 下列範例會使用 Visual Studio 2017。

1.  在 Visual Studio 中，建立新的**主控台應用程式 (.Net Framework)**專案並為它命名，例如 **SBGeoDR**。

2.  安裝下列 NuGet 封裝：
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. 確定您使用的 Newtonsoft.Json NuGet 封裝版本是版本 10.0.3。

3.  將下列 `using` 陳述式新增至您的程式碼：

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. 修改您的 `main()` 方法，以新增兩個進階命名空間：

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. 在這兩個命名空間之間啟用配對，並取得您稍後用來連接到實體的別名：

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

您已成功設定兩個配對的命名空間。 現在您可以建立實體來觀察中繼資料同步處理。 如果您之後想要立即執行容錯移轉，就應該在中繼資料進行同步處理時等候一段時間。 您可以新增短暫的睡眠時間，例如：

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

此時，您可以透過入口網站或透過 Azure Resource Manager 新增實體，並查看其同步處理的方式。 除非您計劃手動進行容錯移轉，否則，應該建立應用程式來監視主要命名空間，並在其變成無法使用時起始容錯移轉。 

## <a name="initiate-a-failover"></a>起始容錯移轉

下列程式碼示範如何起始容錯移轉：

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

當您觸發容錯移轉之後，請新增新的被動命名空間，並重新建立配對。 如需建立新配對的程式碼，請參閱上一節。 此外，一旦容錯移轉完成之後，您就必須從舊的主要命名空間移除訊息。 如需如何從佇列接收訊息的範例，請參閱[開始使用佇列](service-bus-dotnet-get-started-with-queues.md)。

## <a name="how-to-disable-geo-disaster-recovery"></a>如何停用地理災害復原

下列程式碼示範如何停用命名空間配對：

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

下列程式碼會刪除您建立的別名：

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>容錯移轉 (回復) 之後的步驟

容錯移轉之後，執行下列兩個步驟：

1.  建立新的被動次要命名空間。 如需程式碼，請參閱上一節。
2.  從佇列中移除剩餘的訊息。

## <a name="alias-connection-string-and-test-code"></a>別名連接字串和測試程式碼

如果您想要測試容錯移轉程序，就可以撰寫範例應用程式，使用別名來將訊息推送至主要命名空間。 若要這樣做，請確定您會從主動命名空間取得別名連接字串。 利用目前的預覽版本，沒有任何其他介面可直接取得連接字串。 下列範例程式碼會在容錯移轉之前和之後連接：

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>後續步驟

- 請參閱地理災害復原在[這裡的 REST API 參考](/rest/api/servicebus/disasterrecoveryconfigs)。
- 執行地理災害復原在 [GitHub 上的範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) \(英文\)。
- 請參閱地理災害復原[範例以將訊息傳送至別名](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) \(英文\)。

若要深入了解服務匯流排傳訊，請參閱下列文章：

* [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
