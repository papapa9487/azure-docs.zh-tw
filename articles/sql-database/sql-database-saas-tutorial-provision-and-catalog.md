---
title: "使用 Azure SQL Database 在多租用戶應用程式中佈建新的租用戶 | Microsoft Docs"
description: "了解如何在 Azure SQL Database 多租用戶 SaaS 應用程式中佈建及編目新租用戶"
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
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: febf9d9f8a32a95ae61734cc2d3632bb74f6c8f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="learn-how-to-provision-and-catalog-new-tenants-and-register-them-in-the-catalog"></a>了解如何中佈建及編目新租用戶，並在目錄中註冊它們

在本教學課程中，您會了解佈建和目錄 SaaS 模式，以及這些模式在 Wingtip SaaS 應用程式中的實作方式。 您會建立並初始化新的租用戶資料庫，並在應用程式的租用戶目錄中加以註冊。 「目錄」是維護許多 SaaS 應用程式租用戶及其資料間之對應的資料庫。 目錄扮演將應用程式要求導向正確資料庫的重要角色。  

在本教學課程中，您將了解如何：

> [!div class="checklist"]

> * 佈建單一的新租用戶，包括逐步執行其實作方式
> * 佈建一批額外的租用戶


若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 Wingtip SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip SaaS 應用程式](sql-database-saas-tutorial.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS 編目模式簡介

在許多以資料庫為基礎的多租用戶 SaaS 應用程式中，知道每個租用戶的資訊儲存位置是很重要的。 在 SaaS 目錄模式中，會使用目錄資料庫來保存每個租用戶及其資料儲存位置之間的對應。 Wingtip SaaS 應用程式會每個資料庫架構都使用單一租用戶，但不論使用多租用戶或單一租用戶資料庫，都是套用在目錄中儲存租用戶與資料庫對應的基本模式。

每個租用戶都會獲指派金鑰，可在目錄中識別租用戶，且已對應至適當資料庫的位置。 在 Wingtip SaaS 應用程式中，金鑰是由租用戶名稱的雜湊組成。 這樣可讓應用程式 URL 的租用戶名稱部分用於建構金鑰。 您可以使用其他租用戶金鑰的配置。  

目錄可讓資料庫的名稱或位置在對應用程式影響最低的情況下進行變更。  在多租用戶資料庫模型中，這也適用於在資料庫之間「移動」租用戶。  目錄也可用來指出租用戶或資料庫是否離線進行維護或其他動作。 這會在[還原單一租用戶教學課程](sql-database-saas-tutorial-restore-single-tenant.md)中加以探索。

此外，目錄實際上是 SaaS 應用程式的管理資料庫，可以儲存其他租用戶或資料庫中繼資料，例如資料庫層或版本、結構描述版本、服務方案或提供給租用戶的 SLA，以及可進行應用程式管理、客戶支援或 DevOps 程序的其他資訊。  

SaaS 應用程式以外，目錄可以啟用資料庫工具。  在 Wingtip SaaS 範例中，目錄是用來啟用跨租用戶的查詢，可在[臨機操作分析教學課程](sql-database-saas-tutorial-adhoc-analytics.md)中加以探索。 跨資料庫作業管理可在[結構描述管理](sql-database-saas-tutorial-schema-management.md)和[租用戶分析](sql-database-saas-tutorial-tenant-analytics.md)教學課程中加以探索。 

在 Wingtip SaaS 應用程式中，會使用[彈性資料庫用戶端程式庫 (EDCL)](sql-database-elastic-database-client-library.md) 的「分區管理」功能來實作目錄。 EDCL 可讓應用程式建立、管理及使用資料庫為基礎的分區對應。 分區對應包含分區 (資料庫) 清單，以及金鑰 (租用戶) 與資料庫之間的對應。  在租用戶佈建期間，可從應用程式或 PowerShell 指令碼使用 EDCL 函式來建立分區對應中的項目，以及從應用程式有效率地連線到正確的資料庫。 EDCL 會快取連線資訊，以將對目錄資料庫的流量降到最低，並加快應用程式的速度。  

> [!IMPORTANT]
> 對應資料可在類別目錄資料庫中取得，但請勿編輯它！ 務必只使用彈性資料庫用戶端程式庫 API 編輯對應資料。 不支援直接操作對應資料，這樣做可能有造成類別目錄損毀的風險。


## <a name="introduction-to-the-saas-provisioning-pattern"></a>SaaS 佈建模式簡介

在使用單一租用戶資料庫模型的 SaaS 應用程式中將新的租用戶上線時，必須佈建新的租用戶資料庫。  它必須建立在適當的位置和服務層、使用適當的結構描述和參考資料進行初始化，然後在目錄中的適當租用戶金鑰下進行註冊。  

可將不同的方法用於資料庫佈建，其中包括執行 SQL 指令碼、部署 bacpac，或複製「最佳」範本資料庫。  

必須在整體結構描述管理策略中了解您所使用的佈建方法，當中必須確保是使用最新的結構描述來佈建新的資料庫。  這會在[結構描述管理教學課程](sql-database-saas-tutorial-schema-management.md)中加以探索。  

Wingtip SaaS 應用程式會佈建新的租用戶，方法是複製目錄伺服器上所部署、名為 basetenantdb 的最佳資料庫。  可將佈建整合到應用程式作為註冊體驗的一部分，及/或使用指令碼整合到支援的離線。 本教學課程將會使用 PowerShell 來探索佈建。 佈建指令碼會複製 basetenantdb，以在彈性集區中建立新的租用戶資料庫，然後使用租用戶專屬的資訊進行初始化，並在目錄分區對應中進行註冊。  在範例應用程式中，會以租用戶名稱作為基礎來指定資料庫的名稱，但這並非模式的重要部分 – 使用目錄可讓任何名稱指派給資料庫。 


## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 [用於下載 Wingtip SaaS 指令碼的步驟](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。


## <a name="provision-and-catalog-detailed-walkthrough"></a>佈建及編目的詳細逐步解說

若要了解 Wingtip 應用程式如何實作新的租用戶佈建，請在佈建租用戶時，新增中斷點並逐步執行工作流程：

1. 開啟 ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ 並設定下列參數：
   * **$TenantName** = 新場地的名稱 (例如，*Bushwillow Blues*)。
   * **$VenueType** = 其中一個預先定義的場地類型：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer。
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
1. **取得設定詳細資料**。 逐步執行 Get-Configuration (使用 F11) 並查看指定應用程式設定的方式。 資源名稱和其他應用程式特定值是在這裡定義的，但在您熟悉指令碼之前，請勿變更這裡的任何值。
1. **取得類別目錄物件**。 逐步執行 Get-Catalog，其會撰寫並傳回較高層級指令碼中使用的目錄物件。  此函式會使用從 **AzureShardManagement.psm1** 匯入的分區管理函式。 目錄物件是由下列項目組成：
   * $catalogServerFullyQualifiedName 是使用標準主幹加上您的 [使用者] 名稱來建構：_catalog-\<使用者\>.database.windows.net_。
   * $catalogDatabaseName 是從下列設定擷取：*tenantcatalog*。
   * $shardMapManager 物件是從類別目錄資料庫初始化。
   * $shardMap 物件是從類別目錄資料庫中的 *tenantcatalog* 分區對應初始化。
   系統會組成並傳回類別目錄物件，並用於較高層級的指令碼中。
1. **計算新的租用戶索引鍵**。 會使用雜湊函式從租用戶名稱建立租用戶索引鍵。
1. **檢查租用戶索引鍵是否已存在**。 會檢查類別目錄以確定索引鍵可供使用。
1. **租用戶資料庫是使用 New-TenantDatabase 來佈建。** 使用 **F11** 來逐步執行並查看使用 [Azure Resource Manager 範本](../azure-resource-manager/resource-manager-template-walkthrough.md)佈建資料庫的方式。

資料庫名稱是使用租用戶名稱來建構，使分區和租用戶之間的從屬關係一目瞭然 (輕鬆就能使用其他資料庫命名策略)。Resource Manager 範本會用來建立租用戶資料庫，方法是在類別目錄伺服器上複製「範本」資料庫 (baseTenantDB)。 另一個方法是先建立空資料庫，然後透過匯入 bacpac 將它初始化，或從已知位置執行初始化指令碼。  

Resource Manager 範本位於 …\Learning Modules\Common\ 資料夾：tenantdatabasecopytemplate.json

建立租用戶資料庫之後，會進一步**使用場地 (租用戶) 名稱和場地類型將它初始化**。 也可以在此進行其他初始化。

租用戶資料庫會以 Add-TenantDatabaseToCatalog 使用租用戶金鑰**在類別目錄中註冊**。 使用 **F11** 來逐步執行以查看詳細資料：

* 類別目錄資料庫已新增到分區對應 (已知資料庫的清單)。
* 已建立將索引鍵值連結到分區的對應。
* 已將租用戶相關的其他中繼資料 (場地的名稱) 新增至目錄中的租用戶資料表。  租用戶資料表並非 ShardManagement 結構描述的一部分，且不是由 EDCL 安裝。  這份表格說明如何擴充目錄資料庫，以支援其他應用程式特定的資料。   


佈建完成之後，執行會返回到原始的 *Demo-ProvisionAndCatalog* 指令碼，這會在瀏覽器中開啟新租用戶的 [Events (活動)] 頁面：

   ![活動](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>佈建一批租用戶

此練習會佈建一批 17 個租用戶。 建議您在開始其他 Wingtip SaaS 教學課程之前佈建這一批租用戶，才會有多個資料庫可以使用。

1. 在 [PowerShell ISE] 中開啟 ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*，並將 *$DemoScenario* 參數變更為 3：
   * **$DemoScenario** = **3**，變更為 **3** 以「佈建一批租用戶」。
1. 按 **F5** 並執行指令碼。

此指令碼會部署一批額外的租用戶。 它會使用控制批次處理的 [Azure Resource Manager 範本](../azure-resource-manager/resource-manager-template-walkthrough.md)，並且將每個資料庫的佈建委派到連結的範本。 以此方式使用範本可讓 Azure Resource Manager 代理指令碼的佈建程序。 範本以平行方式佈建資料庫，它可以視需要處理重試，進而最佳化整體程序。 指令碼為等冪，如果它失敗，或因為任何原因而停止，請再次執行。

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>確認已成功部署一批租用戶

* 在 [Azure 入口網站](https://portal.azure.com)中瀏覽到您的伺服器清單以開啟 *tenants1* 伺服器，按一下 [SQL 資料庫]，並驗證清單中有一批額外的 17 個資料庫：

   ![資料庫清單](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)



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
