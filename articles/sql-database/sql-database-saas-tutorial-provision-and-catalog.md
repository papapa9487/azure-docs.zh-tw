---
title: "使用 Azure SQL Database 在多租用戶應用程式中佈建新的租用戶 | Microsoft Docs"
description: "了解如何在 Wingtip SaaS 應用程式中佈建及編目新租用戶"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: c019ea9207379ea1b88ec5d990e1c2b8565092a2
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>佈建新的租用戶並在目錄中註冊它們

在本教學課程中，您會在 Wingtip SaaS 應用程式中佈建新租用戶。 您可以佈建租用戶資料庫，並在目錄中註冊來建立租用戶。 「目錄」是維護 SaaS 應用程式租用戶及其資料間之對應的資料庫。

使用這些指令碼來探索使用的佈建及編目模式，以及在類別目錄中實作註冊新租用戶的方式。 目錄扮演將應用程式要求導向正確資料庫的重要角色。

在本教學課程中，您將了解如何：

> [!div class="checklist"]

> * 佈建單一新租用戶
> * 佈建一批額外的租用戶
> * 了解佈建租用戶和將它們註冊到目錄的細節


若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 Wingtip SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip SaaS 應用程式](sql-database-saas-tutorial.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS 編目模式簡介

在許多以資料庫為基礎的多租用戶 SaaS 應用程式中，知道每個租用戶的資訊儲存位置是很重要的。 在 SaaS 編目模式中，會使用類別目錄資料庫來保存租用戶及其資料儲存位置之間的對應。 Wingtip SaaS 應用程式會每個資料庫架構都使用單一租用戶，但不論使用多租用戶或單一租用戶資料庫，都是套用在目錄中儲存租用戶與資料庫對應的基本模式。

已指派每個租用戶索引鍵，以在類別目錄中區別其資料。 在 Wingtip SaaS 應用程式中，索引鍵是由租用戶名稱的雜湊組成。 此模式可讓應用程式 URL 的租用戶名稱部分用於建構索引鍵，並擷取特定租用戶的連線。 可以使用其他識別碼配置而不會影響整體模式。

應用程式中的類別目錄是在[彈性資料庫用戶端程式庫 (EDCL)](sql-database-elastic-database-client-library.md) 中使用「分區管理」技術實作。 EDCL 負責建立及管理以資料庫為基礎的「類別目錄」，並在其中維護「分區對應」。 目錄會包含索引鍵 (租用戶) 和其分區 (資料庫) 之間的對應。

> [!IMPORTANT]
> 對應資料可在類別目錄資料庫中取得，但請勿編輯它！ 務必只使用彈性資料庫用戶端程式庫 API 編輯對應資料。 不支援直接操作對應資料，這樣做可能有造成類別目錄損毀的風險。



## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 [用於下載 Wingtip SaaS 指令碼的步驟](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="provision-one-new-tenant"></a>佈建一個新租用戶

如果您已經在[第一個 Wingtip SaaS 教學課程](sql-database-saas-tutorial.md)中建立租用戶，則可以跳至下一節來[佈建一批租用戶](#provision-a-batch-of-tenants)。

執行 *Demo-ProvisionAndCatalog* 指令碼以快速建立租用戶並在類別目錄中註冊它：

1. 在 [PowerShell ISE] 中開啟 **Demo-ProvisionAndCatalog.ps1** 並設定下列值：
   * **$TenantName** = 新場地的名稱 (例如，*Bushwillow Blues*)。
   * **$VenueType** = 其中一個預先定義的場地類型：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer。
   * **$DemoScenario** = 1，將此設定維持為 _1_，以「佈建單一租用戶」。

1. 按 **F5** 並執行指令碼。

指令碼執行完成之後，新租用戶就已佈建，且其 [Events (活動)] 應用程式會在瀏覽器中開啟：

![新租用戶](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>佈建一批租用戶

此練習會佈建一批額外的租用戶。 建議您在完成其他 Wingtip SaaS 教學課程之前佈建一批租用戶，才會有多個資料庫可以使用。

1. 在 [PowerShell ISE] 中開啟 ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*，並將 *$DemoScenario* 參數變更為 3：
   * **$DemoScenario** = **3**，變更為 **3** 以「佈建一批租用戶」。
1. 按 **F5** 並執行指令碼。

此指令碼會部署一批額外的租用戶。 它會使用控制批次處理的 [Azure Resource Manager 範本](../azure-resource-manager/resource-manager-template-walkthrough.md)，並且將每個資料庫的佈建委派到連結的範本。 以此方式使用範本可讓 Azure Resource Manager 代理指令碼的佈建程序。 範本以平行方式佈建資料庫，它可以視需要處理重試，進而最佳化整體程序。 指令碼為等冪，如果它失敗，或因為任何原因而停止，請再次執行。

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>確認已成功部署一批租用戶

* 在 [Azure 入口網站](https://portal.azure.com)中瀏覽到您的伺服器清單以開啟 *tenants1* 伺服器，按一下 [SQL 資料庫]，並驗證清單中有一批額外的 17 個資料庫：

   ![資料庫清單](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="stepping-through-the-provision-and-catalog-implementation-details"></a>逐步說明佈建和目錄實作詳細資訊

若要深入了解 Wingtip 應用程式實作新租用戶佈建的方式，請再次執行 *Demo-ProvisionAndCatalog* 指令碼並佈建另一個租用戶。 這次，新增中斷點並逐步執行工作流程：

1. 開啟 ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ 並設定下列參數：
   * **$TenantName** = 租用戶名稱必須是唯一的，因此請設定為和任何現有租用戶不同的名稱 (例如，*Hackberry Hitters*)。
   * **$VenueType** = 使用其中一個預先定義的場地類型 (例如，*judo*)。
   * **$DemoScenario** = **1**，設定為 **1** 以「佈建單一租用戶」。

1. 將游標置於以第 48 行 (該行顯示︰*New-Tenant `*) 上的任意位置來新增中斷點，然後按 **F9**。

   ![中斷點](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. 若要執行指令碼，請按 **F5**。

1. 在指令碼於中斷點停止執行之後，請按 **F11** 進入程式碼。

   ![中斷點](media/sql-database-saas-tutorial-provision-and-catalog/debug.png)



使用 [偵錯] 功能表選項追蹤指令碼的執行 - **F10** 和 **F11** 可以跳過或進入呼叫的函式。 如需對 PowerShell 指令碼進行偵錯的詳細資訊，請參閱[使用 PowerShell 指令碼及對其進行偵錯的祕訣](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise) \(英文\)。


以下並非要明確遵循的步驟，而是您在進行指令碼偵錯時逐步執行之工作流程的說明：

1. 將包含用於登入之函式的 **SubscriptionManagement.psm1** 模組匯入到 Azure，並選取您正在使用的 Azure 訂用帳戶。
1. 匯入對[分區管理](sql-database-elastic-scale-shard-map-management.md)函式提供類別目錄和租用戶層級抽象概念的 **CatalogAndDatabaseManagement.psm1** 模組。 此模組很重要且值得探討，其中包含大部分的編目模式。
1. **取得設定詳細資料**。 逐步執行 *Get-Configuration* (使用 **F11**) 並查看指定應用程式設定的方式。 資源名稱和其他應用程式特定值是在這裡定義的，但在您熟悉指令碼之前，請勿變更這裡的任何值。
1. **取得類別目錄物件**。 逐步執行 *Get-Catalog* 以查看如何使用從 **AzureShardManagement.psm1** 匯入的分區管理函式來將類別目錄初始化。 類別目錄由下列物件組成：
   * $catalogServerFullyQualifiedName 是使用標準主幹加上您的 [使用者] 名稱來建構：_catalog-\<使用者\>.database.windows.net_。
   * $catalogDatabaseName 是從下列設定擷取：*tenantcatalog*。
   * $shardMapManager 物件是從類別目錄資料庫初始化。
   * $shardMap 物件是從類別目錄資料庫中的 *tenantcatalog* 分區對應初始化。
   系統會組成並傳回類別目錄物件，並用於較高層級的指令碼中。
1. **計算新的租用戶索引鍵**。 會使用雜湊函式從租用戶名稱建立租用戶索引鍵。
1. **檢查租用戶索引鍵是否已存在**。 會檢查類別目錄以確定索引鍵可供使用。
1. **租用戶資料庫是使用 New-TenantDatabase 來佈建。** 使用 **F11** 來逐步執行並查看使用 Resource Manager 範本佈建資料庫的方式。

資料庫名稱是使用租用戶名稱來建構，使分區和租用戶之間的從屬關係一目瞭然 (輕鬆就能使用其他資料庫命名策略)。

Resource Manager 範本會用來建立租用戶資料庫，方法是在類別目錄伺服器上複製「範本」資料庫 (basetenantdb)。  另一個方法是先建立空資料庫，然後透過匯入 bacpac 將它初始化。

Resource Manager 範本位於 …\\Learning Modules\\Common\\ 資料夾：*tenantdatabasecopytemplate.json*

建立租用戶資料庫之後，會進一步使用場地 (租用戶) 名稱和場地類型將它初始化。 也可以在此進行其他初始化。

租用戶資料庫會以 *Add-TenantDatabaseToCatalog* 使用租用戶索引鍵在類別目錄中註冊。 使用 **F11** 來逐步執行以查看詳細資料：

* 類別目錄資料庫已新增到分區對應 (已知資料庫的清單)。
* 已建立將索引鍵值 (租用戶) 連結到分區 (資料庫) 的對應。
* 已新增租用戶相關的其他中繼資料。

佈建完成之後，執行會返回到原始的 *Demo-ProvisionAndCatalog* 指令碼，且新租用戶的 [Events (活動)] 頁面會在瀏覽器中開啟：

   ![活動](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>其他佈建模式

未包含在此教學課程中的其他佈建模式包括：

**預先佈建資料庫。** 預先佈建模式會利用彈性集區中的資料庫，並不會增加額外成本。 針對彈性集區計費，而不是資料庫，而且閒置資料庫不會耗用任何資源。 藉由在集區中預先佈建資料庫，並在需要時才配置它們，可以大幅降低租用戶上線時間。 可以視需要調整預先佈建的資料庫數目，以針對預期的佈建率保留適當的緩衝。

**自動佈建。** 在自動佈建模式中，會使用專用的佈建服務，視需要自動佈建伺服器、集區與資料庫 - 包括在彈性集區中預先佈建資料庫 (若有需要)。 如果資料庫已被解除委任並刪除，可以視需要以佈建服務填入彈性集區中的間隔。 此類服務可以是簡單或複雜的 (例如，跨多個地理位置處理佈建)，且如果已針對災害復原使用異地複寫，就可自動設定該策略。 若使用自動佈建模式，用戶端應用程式或指令碼會將佈建要求送出到將由佈建服務處理的佇列，然後會輪詢該服務以判斷是否完成。 若使用預先佈建，則會使用在背景執行的服務 (負責管理替代資料庫的佈建) 來快速處理要求。



## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]

> * 佈建單一新租用戶
> * 佈建一批額外的租用戶
> * 了解佈建租用戶和將它們註冊到目錄的細節

試用[效能監視教學課程](sql-database-saas-tutorial-performance-monitoring.md)。

## <a name="additional-resources"></a>其他資源

* 其他[以 Wingtip SaaS 應用程式為基礎的教學課程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [彈性資料庫用戶端程式庫](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [如何在 Windows PowerShell ISE 中針對指令碼進行偵錯](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

