---
title: "在多租用戶 SaaS 應用程式中還原 Azure SQL Database | Microsoft Docs"
description: "了解如何在不小心刪除資料之後還原單一租用戶 SQL Database"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: 60788b8a1b417e9bdfbe5ea8424dff9a39f36f2d
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>在多租用戶 SaaS 應用程式中還原單一租用戶 Azure SQL Database

Wingtip SaaS 應用程式是使用每一租用戶一個資料庫的模型來建置的，其中每個租用戶都有自己的資料庫。 此模型的其中一個優點是很容易以隔離方式還原單一租用戶的資料，而不會影響到其他租用戶。

在本教學課程中，您將了解兩個資料復原模式：

> [!div class="checklist"]

> * 將資料庫還原到平行資料庫 (並存)
> * 就地還原資料庫


|||
|:--|:--|
| **將租用戶還原到先前的時間點且還原到平行資料庫** | 此模式可供租用戶用於檢閱、稽核及合規性等用途。原始資料庫仍保持連線而不變。 |
| **就地還原租用戶** | 此模式通常是在租用戶不小心將資料刪除後，用來將租用戶復原到先前的時間點。 原始資料庫將會離線，然後被還原的資料庫取代。 |
|||

若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 Wingtip SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip SaaS 應用程式](saas-dbpertenant-get-started-deploy.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>SaaS 租用戶還原模式簡介

就租用戶還原模式而言，有兩個可還原個別租用戶資料的簡單模式。 由於租用戶資料庫彼此互相隔離，因此還原一個租用戶並不會影響到任何其他租用戶的資料。

在第一個模式中，資料會還原到新的資料庫。 租用戶會接著獲得存取權來存取此資料庫及它們的生產環境資料。 此模式可讓租用戶系統管理員檢閱已還原的資料，並可能使用此資料來選擇性地覆寫目前的資料值。 資料復原選項應有的複雜程度是由 SaaS 應用程式設計人員決定。 在某些情況下，只要能夠檢閱資料在所指定時間點時的狀態，可能就已足以滿足需求。 如果資料庫使用[異地複寫](sql-database-geo-replication-overview.md)，建議您從已還原的複本將所需的資料複製到原始資料庫。 如果您以已還原的資料庫取代原始資料庫，則必須重新設定並重新同步處理異地複寫。

在第二個模式中 (其中假設租用戶的資料遺失或損毀)，租用戶的生產環境資料會還原到先前的時間點。 在就地還原模式中，租用戶會在資料庫還原時短暫離線，然後再重新上線。 此模式會將原始資料庫刪除，但如果您需要回到先前時間點中的某個事件，則仍可從此資料庫還原。 此模式的一個變通形式可將此資料庫重新命名，而不是刪除它，不過就資料安全性而言，將資料庫重新命名並不會提供任何額外的優點。

## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 [用於下載 Wingtip SaaS 指令碼的步驟](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>模擬租用戶不小心刪除資料的情況

為了示範這些復原情況，我們需要「不小心」刪除其中一個租用戶資料庫中的一些資料。 您可以刪除任何記錄，而下一個步驟則是設定示範，讓它不會因為違反參考完整性而遭到封鎖！ 它也會新增一些您可以稍後在＜Wingtip SaaS 分析教學課程＞中使用的票證購買資料。

請執行票證產生器指令碼並建立額外的資料。 票證產生器會刻意不為每個租用戶的最後一個事件購買票證。

1. 在 *PowerShell ISE* 中開啟 ...\\Learning Modules\\Utilities\\*Demo-TicketGenerator.ps1*
1. 按 **F5** 以執行指令碼並產生客戶與票證銷售資料。


### <a name="open-the-events-app-to-review-the-current-events"></a>開啟事件應用程式以檢閱目前的事件

1. 開啟 *[事件中樞]* \(http://events.wtp.&lt;user&gt;.trafficmanager.net)，然後按一下 **[Contoso Concert Hall]**：

   ![事件中樞](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. 捲動事件清單，然後記下清單中的最後一個事件：

   ![最後一個事件](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>執行示範案例來不小心刪除最後一個事件

1. 在 *PowerShell ISE* 中開啟 ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1*，然後設定下列值：
   * **$DemoScenario** = **1**，設定為 **1** - 刪除沒有任何票證銷售額的事件。
1. 按 **F5** 以執行指令碼並刪除最後一個事件。 您應該會看見類似下方的確認訊息：

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Contoso 事件頁面隨即開啟。 請向下捲動並確認該事件已消失。 如果該事件仍在清單中，請按一下 [重新整理] 並確認它已消失。

   ![最後一個事件](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>將租用戶資料庫以與生產環境資料戶平行的方式還原

此練習會將 Contoso Concert Hall 資料庫還原到刪除事件前的時間點。 在於先前的步驟中刪除事件之後，您想要復原並查看已刪除的資料。 您不需要還原含有所刪除記錄的生產環境資料庫，但基於其他業務原因，您必須復原舊資料庫以存取舊資料。

 *Restore-TenantInParallel.ps1* 指令碼會建立一個平行租用戶資料庫，以及一個平行目錄項目，兩者皆名為 *ContosoConcertHall\_old*。 此復原模式最適用於從較不嚴重的資料遺失進行復原，或用於合規性和稽核復原案例。 當您使用的是[異地複寫](sql-database-geo-replication-overview.md)時，這也是建議採用的方法。

1. 完成[模擬使用者不小心刪除資料的情況](#simulate-a-tenant-accidentally-deleting-data)一節。
1. 在 *PowerShell ISE* 中開啟 ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_。
1. 設定 **$DemoScenario** = **2**，將此項目設定為 **2** 以「平行還原租用戶」。
1. 按 **F5** 以執行指令碼。

此指令碼會將租用戶資料庫還原 (到平行資料庫) 到您在上一節中刪除事件之前的時間點。 它會建立第二個資料庫、移除此資料庫中任何現有的目錄中繼資料，然後將資料庫新增到目錄中 *ContosoConcertHall\_old* 項目底下。

此示範指令碼會在您的瀏覽器中開啟事件頁面。 請注意 URL：```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old```，這顯示資料來自所還原的資料庫，其中在名稱加上了 *_old*。

請捲動瀏覽器中列出的事件，以確認在上一節中刪除的事件已經還原。

請注意，將已還原的租用戶公開為額外的租用戶 (具有自己的「事件」應用程式來瀏覽票證)，不太可能是您將所還原資料的存取權提供給租用戶的方式，但可用來輕鬆說明還原模式。

事實上，您可能只會依據一段定義的期間保留這個已還原的資料庫。 在您完成作業之後，即可呼叫 *Remove-RestoredTenant.ps1* 指令碼來刪除已還原的租用戶項目。

1. 將 **$DemoScenario** 設定為 **4** 以選取「移除已還原的租用戶」案例。
1. **使用** **F5** 來**執行**
1. *ContosoConcertHall\_old* 項目現在已從目錄中刪除。 請在您的瀏覽器中逕行關閉此租用戶的事件頁面。


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>就地還原租用戶，取代現有的租用戶資料庫

此練習會將 Contoso Concert Hall 租用戶還原到刪除事件前的時間點。 *Restore-TenantInPlace* 指令碼會將目前的租用戶資料庫還原到指向先前時間點的新資料庫，並刪除原始資料庫。 此復原模式最適用於從嚴重的資料損毀進行復原，因為可能有租用戶將必須調適的重大資料遺失。

1. 在 PowerShell ISE 中開啟 **Demo-RestoreTenant.ps1** 檔案
1. 將 **$DemoScenario** 設定為 **5** 以選取「就地還原租用戶」案例。
1. 使用 **F5** 來執行。

此指令碼會將租用戶資料庫還原到刪除事件前 5 分鐘的時間點。 其做法是先讓 Contoso Concert Hall 租用戶離線，使得對資料無法進行任何進一步的更新。 然後，會從還原點還原來建立一個平行資料庫，並以時間戳記命名，以確保資料庫名稱不會與現有的租用戶資料庫名稱衝突。 接著，會刪除舊的租用戶資料庫，並將已還原的資料庫重新命名成原始資料庫名稱。 最後，會讓 Contoso Concert Hall 上線以允許應用程式存取已還原的資料庫。

您已成功地將資料庫還原到刪除事件前的時間點。 此時會開啟 [事件] 頁面，因此請確認最後一個事件已經還原。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]

> * 將資料庫還原到平行資料庫 (並存)
> * 就地還原資料庫

[管理租用戶資料庫結構描述](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>其他資源

* 其他[以 Wingtip SaaS 應用程式為基礎的教學課程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [使用 Azure SQL Database 的商務持續性概觀](sql-database-business-continuity.md)
* [了解 SQL Database 備份](sql-database-automated-backups.md)
