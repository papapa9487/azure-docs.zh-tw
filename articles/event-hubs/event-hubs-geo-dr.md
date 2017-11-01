---
title: "Azure 事件中樞地理災害復原 | Microsoft Docs"
description: "如何使用地理區域，在 Azure 事件中樞中進行容錯移轉並執行災害復原"
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Azure 事件中樞地理災害復原 (預覽)

當區域資料中心遇到停機時，最重要的是資料處理作業能夠繼續在不同的區域或資料中心運作。 因此，「地理災害復原」和「異地複寫」對於任何企業而言都是重要的功能。 Azure 事件中樞支援命名空間層級的地理災害復原和異地複寫。 

Azure 事件中樞的地理災害復原功能就是一個災害復原解決方案。 本文中所述的概念和工作流程適用於災害案例，不適用暫時性或暫時中斷。

如需 Microsoft Azure 中災害復原的詳細討論，請參閱[本文](/azure/architecture/resiliency/disaster-recovery-azure-applications)。 

## <a name="terminology"></a>術語

**配對**：主要命名空間稱為「主動」，並收到訊息。 容錯移轉命名空間是「被動」，並不會收到訊息。 這兩者間的中繼資料會進行同步處理，因此，這兩者均能順暢地接受訊息，而不需進行任何應用程式的程式碼變更。 在主動區域與被動區域之間建立災害復原設定，稱為「配對」區域。

**別名**：所設定之災害復原設定的名稱。 別名提供單一穩定完整網域名稱 (FQDN) 連接字串。 應用程式會使用這個別名連接字串連接到命名空間。

**中繼資料**：指的是事件中樞名稱、取用者群組、分割區、輸送量單位、實體，以及與命名空間建關聯的屬性。

## <a name="enable-geo-disaster-recovery"></a>啟用地理災害復原

您可以使用 3 個步驟啟用事件中樞地理災害復原： 

1. 建立地理配對，這會建立別名連接字串，並提供即時中繼資料複寫。 
2. 將現有用戶端連接字串更新為步驟 1 中所建立的別名。
3. 起始容錯移轉：地理配對會中斷，而且別名會指向要作為其新主要命名空間的次要命名空間。

下圖顯示此工作流程：

![地理配對流程][1] 

### <a name="step-1-create-a-geo-pairing"></a>步驟 1：建立地理配對

若要建立兩個區域之間的配對，您需要主要命名空間和次要命名空間。 您接著會建立別名，以形成地理配對。 將命名空間與別名配對之後，會定期複寫兩個命名空間中的中繼資料。 

下列程式碼示範如何執行這項作業：

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>步驟 2：更新現有用戶端連接字串

地理配對完成之後，必須將指向主要命名空間的連接字串更新為指向別名連接字串。 取得連接字串，如下列範例所示：

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>步驟 3：起始容錯移轉

如果發生災害，或者您決定要起始容錯移轉到次要命名空間，中繼資料和資料會開始流向次要命名空間。 應用程式使用別名連接字串，因此不需要進行任何進一步動作，因為它們會自動開始讀取和寫入至次要命名空間中的事件中樞。 

下列程式碼示範如何觸發容錯移轉：

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

容錯移轉完成而且您需要主要命名空間中的資料之後，若要擷取資料，您必須使用主要命名空間中事件中樞的明確連接字串。

### <a name="other-operations-optional"></a>其他作業 (選擇性)

您也可以中斷地理配對，或刪除別名，如下列程式碼所示。 請注意，若要刪除別名連接字串，您必須先中斷地理配對：

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>公開預覽考量

請注意此版本的下列考量：

1. 地理災害復原功能目前僅適用於美國中北部和美國中南部區域。 
2. 只有新建的命名空間才支援這個功能。
3. 在預覽版本中，只會啟用中繼資料複寫。 不會複寫實際資料。
4. 在預覽版本中，啟用這個功能沒有任何成本。 不過，主要和次要命名空間會產生保留輸送量單位的成本。

## <a name="next-steps"></a>後續步驟

* [GitHub 上的範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient)會逐步解說簡單工作流程，以建立地理配對並在災害復原情況下起始容錯移轉。
* [REST API 參考](/rest/api/eventhub/disasterrecoveryconfigs)描述用來執行地理災害復原設定的 API。

如需事件中樞的詳細資訊，請造訪下列連結：

* 開始使用[事件中樞教學課程](event-hubs-dotnet-standard-getstarted-send.md)
* [事件中樞常見問題集](event-hubs-faq.md)
* [使用事件中樞的範例應用程式](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

