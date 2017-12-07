---
title: "使用多租用戶 Azure SQL Database SQL Database 在 SaaS 應用程式中佈建及編目新的租用戶 |Microsoft Docs"
description: "了解如何在 Azure SQL Database 多租用戶 SaaS 應用程式中佈建及編目新租用戶"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: e7de7bb545e0ce04dc1b3dd398cc920213d09bae
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>使用分區化多租用戶 SQL Database 在 SaaS 應用程式中佈建及編目新的租用戶

在本教學課程中，您會了解當使用分區化多租用戶資料庫模型時，佈建和分類租用戶的模式。 

在保存租用戶資料的資料表主索引鍵中包含租用戶識別碼的多租用戶結構描述，可讓多個租用戶儲存在單一資料庫中。 若要支援大量的租用戶，會跨多個分區或資料庫發佈租用戶資料。 任何一個租用戶的資料一律會完整包含在單一資料庫中。  目錄會用來將租用戶的對應保存到資料庫。   

您也可以選擇僅將單一租用戶填入一些資料庫。 保存多個租用戶的資料庫青睞每個租用戶的更低成本，但會犧牲租用戶隔離。  僅保留單一租用戶的資料庫青睞低成本的隔離。  多個租用戶與單一租用戶的資料庫可以在相同的 SaaS 應用程式中混合使用，以最佳化每個租用戶的成本或隔離。 在佈建時可以向租用戶提供其自己的資料庫，或可在稍後移到其各自的資料庫。

   ![具有租用戶目錄的多租用戶資料庫應用程式](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>租用戶目錄模式

如果跨多個資料庫發佈租用戶資料，就務必知道每個租用戶的資料儲存位置。 目錄資料庫會保存租用戶和儲存其資料的資料庫之間的對應。

在目錄中會以金鑰識別每個租用戶。 在 Wingtip Tickets SaaS 應用程式中，租用戶金鑰是由租用戶名稱的雜湊組成。 這可讓應用程式擷取網頁 URL 的租用戶名稱，並用此來連線到適當的資料庫。 也可以使用其他租用戶金鑰的配置。

使用目錄可在佈建之後變更租用戶資料庫的名稱或位置，而不會中斷應用程式。  在多租用戶資料庫模型中，這適用於在資料庫之間「移動」租用戶。  目錄也可用來指出應用程式租用戶是否離線進行維護或其他動作。

可以目錄延伸來儲存其他租用戶或資料庫中繼資料，例如資料庫層或版本、資料庫結構描述版本、租用戶名稱及服務方案或 SLA，以及可進行應用程式管理、客戶支援或 DevOps 程序的其他資訊。  

目錄也可用來就分析目的啟用跨租用戶報表、結構描述管理並擷取資料。 

### <a name="elastic-database-client-library"></a>彈性資料庫用戶端程式庫 
在 Wingtip Tickets SaaS 應用程式中，[會使用彈性資料庫用戶端程式庫 (EDCL)](sql-database-elastic-database-client-library.md) 的「分區管理」功能，在 *tenantcatalog* 資料庫中實作目錄。 文件庫可讓應用程式建立、管理及使用資料庫為基礎的分區對應。 分區對應包含分區 (資料庫) 清單，以及金鑰 (租用戶識別碼) 與分區之間的對應。  在租用戶佈建期間，可從應用程式或 PowerShell 指令碼使用 EDCL 函式來建立分區對應中的項目，以及稍後連線到正確的資料庫。 文件庫會快取連線資訊，以將對目錄資料庫的流量降到最低，並加快連線的速度。 

> [!IMPORTANT]
> 對應資料可在類別目錄資料庫中取得，但請勿編輯它！ 務必只使用彈性資料庫用戶端程式庫 API 編輯對應資料。 不支援直接操作對應資料，這樣做可能有造成類別目錄損毀的風險。


## <a name="tenant-provisioning-pattern"></a>租用戶佈建模式

在分區化多租用戶資料庫模型中佈建新的租用戶時，就必須先判斷是否要將租用戶佈建到共用資料庫或提供它自己的資料庫。 如果是共用資料庫，就必須判斷現有的資料庫中是否有空間，或是需要新的資料庫。 如果需要新的資料庫，就必須佈建在適當的位置和服務層、使用適當的結構描述和參考資料進行初始化，然後在目錄中進行註冊。 最後，可以新增租用戶對應來參考適當的分區。

執行 SQL 指令碼、部署 bacpac 或複製範本資料庫來佈建資料庫。 Wingtip Tickets SaaS 應用程式會複製範本資料庫，來建立新的租用戶資料庫。

必須在整體結構描述管理策略中了解資料庫的佈建方法，當中必須確保是使用最新的結構描述來佈建新的資料庫。  這會在[結構描述管理教學課程](saas-tenancy-schema-management.md)中進一步進行探索。  

本教學課程中的租用戶佈建指令碼包含將租用戶佈建到與其他租用戶共用的現有資料庫，以及將租用戶佈建到它自己的資料庫。 然後會將租用戶資料初始化，並在目錄分區對應中加以註冊。 在範例應用程式中，會向包含多個租用戶的資料庫提供一個通用的名稱，例如 *tenants1*、*tenants2* 等等，同時向包含單一租用戶的資料庫則提供租用戶的名稱。 範例中使用的特定命名慣例並非模式中的重要部分，因為使用目錄可允許將任何名稱指派給資料庫。  

## <a name="provision-and-catalog-tutorial"></a>佈建和目錄教學課程

在本教學課程中，您將了解如何：

> [!div class="checklist"]

> * 將租用戶佈建到多租用戶資料庫
> * 將租用戶佈建到單一租用戶資料庫
> * 將一批租用戶佈建到多租用戶和單一租用戶資料庫
> * 在目錄中註冊資料庫和租用戶對應

若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 Wingtip Tickets SaaS 多租用戶資料庫應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip Tickets SaaS 多租用戶資料庫應用程式](saas-multitenantdb-get-started-deploy.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>取得 Wingtip Tickets SaaS 多租用戶資料庫應用程式原始碼和指令碼

可在 [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 存放庫中使用 Wingtip Tickets SaaS 多租用戶資料庫指令碼和應用程式來源程式碼。 關於下載和解除封鎖 Wingtip Tickets SaaS 指令碼的步驟，請參閱[一般指引](saas-tenancy-wingtip-app-guidance-tips.md)。 

## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>在與其他租用戶共用的資料庫中佈建租用戶

若要了解 Wingtip Tickets 應用程式如何在共用的資料庫中實作新的租用戶佈建，請新增中斷點並逐步執行工作流程：

1. 在 _PowerShell ISE_ 中，開啟 ...\\Learning Modules\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_ 並設定下列參數：
   * **$TenantName** = **Bushwillow Blues**，新場地的名稱。
   * **$VenueType** = **blues**，其中一個預先定義的場地類型：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer (小寫、不含空格)。
   * **$DemoScenario** = **1**，在與其他租用戶共用的資料庫中佈建租用戶。

1. 將游標置於以第 38 行 (該行顯示︰New-Tenant `) 上的任意位置來新增中斷點，然後按 **F9**。

   ![中斷點](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. 若要執行指令碼，請按 **F5**。

1. 在指令碼於中斷點停止執行之後，請按 **F11** 進入程式碼。

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

使用 [偵錯] 功能表選項、**F10** 和 **F11** 追蹤指令碼的執行，可以跳過或進入呼叫的函式。 如需對 PowerShell 指令碼進行偵錯的詳細資訊，請參閱[使用 PowerShell 指令碼及對其進行偵錯的祕訣](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise) \(英文\)。


以下是您逐步執行佈建工作流程的重要元素：

* **計算新的租用戶索引鍵**。 會使用雜湊函式從租用戶名稱建立租用戶索引鍵。
* **檢查租用戶索引鍵是否已存在**。 會檢查目錄以確保尚未註冊金鑰。
* **在預設租用戶資料庫中初始化租用戶**。 會更新租用戶資料庫來新增新的租用戶資訊。  
* **在目錄中註冊租用戶** 已將新租用戶金鑰與現有 tenants1 資料庫之間的對應新增至目錄。 
* **將租用戶的名稱新增至目錄延伸模組資料表**。 場地名稱會新增至目錄中的租用戶資料表。  這會說明如何擴充目錄資料庫，以支援其他應用程式特定的資料。
* **開啟新租用戶的事件頁面**。 Bushwillow Blues 事件頁面隨即在瀏覽器中開啟：

   ![活動](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>在它自己的資料庫中佈建租用戶

現在逐步解說在它自己的資料庫中建立租用戶的程序：

1. 仍在 ...\\Learning Modules\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_ 中，設定下列參數：
   * **$TenantName** = **Sequoia Soccer**，新場地的名稱。
   * **$VenueType** = **soccer**，其中一個預先定義的場地類型：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer (小寫、不含空格)。
   * **$DemoScenario** = **2**在它自己的資料庫中佈建租用戶。

1. 將游標置於以第 57 行 (該行顯示︰&&nbsp;$PSScriptRoot\New-TenantAndDatabase `) 上的任意位置來新增中斷點，然後按 **F9**。

   ![中斷點](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. 按 **F5** 以執行指令碼。

1. 在指令碼於中斷點停止執行之後，請按 **F11** 進入程式碼。  使用 **F10** 和 **F11** 可以跳過及進入函式追蹤執行。

以下是您在追蹤指令碼時，逐步執行工作流程的重要元素：

* **計算新的租用戶索引鍵**。 會使用雜湊函式從租用戶名稱建立租用戶索引鍵。
* **檢查租用戶索引鍵是否已存在**。 會檢查目錄以確保尚未註冊金鑰。
* **建立新的租用戶資料庫**。 資料庫的建立方式為使用 Resource Manager 範本複製 basetenantdb 資料庫。  新的資料庫名稱是以租用戶的名稱作為基礎。
* **將資料庫新增至目錄**。 新租用戶資料庫在目錄中會註冊為分區。
* **在預設租用戶資料庫中初始化租用戶**。 會更新租用戶資料庫來新增新的租用戶資訊。  
* **在目錄中註冊租用戶** 已將新租用戶金鑰與 sequoiasoccer 資料庫之間的對應新增至目錄。
* **租用戶名稱會新增至目錄**。 場地名稱會新增至目錄中的租用戶延伸模組資料表。
* **開啟新租用戶的事件頁面**。 Sequoia Soccer 事件頁面隨即在瀏覽器中開啟：

   ![活動](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>佈建一批租用戶

此練習會佈建一批 17 個租用戶。 建議您在開始其他 Wingtip Tickets 教學課程之前佈建這一批租用戶，才會有較多資料庫可以使用。


1. 在 PowerShell ISE 中，開啟 ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*，並將 $DemoScenario 參數變更為 4：
   * **$DemoScenario** = ，以「佈建一批租用戶」。
1. 按 **F5** 並執行指令碼。


### <a name="verify-the-deployed-set-of-tenants"></a>驗證租用戶的已部署集合 
在這個階段中，您要將混合的租用戶部署到共用資料庫中，並將租用戶部署到其自己的資料庫。 Azure 入口網站可以用來檢查所建立的資料庫。 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 SQL 伺服器的清單以開啟 **tenants1-mt-\<USER\>** 伺服器。  **SQL 資料庫**清單應該包括共用的 **tenants1** 資料庫，以及位於其自己資料庫中的租用戶之資料庫：

   ![資料庫清單](media/saas-multitenantdb-provision-and-catalog/Databases.png)

儘管 Azure 入口網站會顯示租用戶資料庫，但它不會讓您查看共用資料庫內的租用戶。 [Wingtip Tickets 事件中樞] 頁面中以及瀏覽目錄都可以看到租用戶的完整清單：   

**使用 Wingtip 票證事件中樞頁面** <br>
在瀏覽器中開啟 [事件中樞] 頁面 (http:events.wingtip-mt.\<USER\>.trafficmanager.net)  

**使用目錄資料庫** <br>
目錄中會提供租用戶和其對應之資料庫的完整清單。 會在 tenantcatalog 資料庫中提供 SQL 檢視，該資料庫會將「租用戶」資料表中儲存的租用戶名稱聯結至「分區管理」資料表中的資料庫名稱。 此檢視會妥善示範目錄中儲存之擴充中繼資料的值。

1. 在 SQL Server Management Studio (SSMS) 中，使用登入：**developer**、密碼：**P@ssword1** 連線到 **catalog-mt.\<USER\>.database.windows.net** 租用戶伺服器

    ![SSMS 連線對話方塊](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

1. 在 [物件總管] 中，瀏覽至 tenantcatalog 資料庫中的檢視。
1. 以滑鼠右鍵按一下 *TenantsExtended* 檢視並選擇 [選取前 1000 個資料列]。 請注意租用戶名稱與不同租用戶的資料庫之間的對應。

    ![SSMS 中的 ExtendedTenants 檢視](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>其他佈建模式

其他有趣的佈建模式包括：

**彈性集區中的預先佈建資料庫。** 預先佈建模式會利用在使用彈性集區時，針對集區而非資料庫計費這個事實。 因此，可以在需要資料庫而未產生額外成本之前，先將資料庫新增至彈性集區。 藉由在集區中預先佈建資料庫，並在需要時才配置它們，可以大幅降低將租用戶佈建到資料庫所花費的時間。 可以視需要調整預先佈建的資料庫數目，以針對預期的佈建率保留適當的緩衝。

**自動佈建。** 在自動佈建模式中，會使用專用的佈建服務，視需要自動佈建伺服器、集區與資料庫 - 包括在彈性集區中預先佈建資料庫。 如果資料庫已被解除委任並刪除，可以視需要以佈建服務填入其在彈性集區中建立的間隔。 此類服務可以是簡單或複雜的 (例如，跨多個地理位置處理佈建)，而且可以針對災害復原設定異地複寫。 若使用自動佈建模式，用戶端應用程式或指令碼會將佈建要求送出到將由佈建服務處理的佇列，然後會輪詢以判斷是否完成。 若使用預先佈建，就會快速處理要求，而另一個服務則會在背景中管理替代資料庫的佈建。



## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]

> * 將單一新租用戶佈建到共用的多租用戶資料庫和其自己的資料庫
> * 佈建一批額外的租用戶
> * 逐步了解佈建租用戶和將它們註冊到目錄的細節

試用[效能監視教學課程](saas-multitenantdb-performance-monitoring.md)。

## <a name="additional-resources"></a>其他資源

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)
* [如何在 Windows PowerShell ISE 中針對指令碼進行偵錯](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
