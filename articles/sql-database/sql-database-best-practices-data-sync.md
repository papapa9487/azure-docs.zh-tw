---
title: "Azure SQL 資料同步最佳做法 | Microsoft Docs"
description: "了解設定及執行 Azure SQL 資料同步的最佳做法"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7492fffd1c18a149ef12174c79d64b47afbaa3e4
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="best-practices-for-azure-sql-data-sync-preview"></a>Azure SQL 資料同步 (預覽) 最佳做法 

本文描述 SQL 資料同步 (預覽) 的最佳做法。

如需 SQL 資料同步處理的概觀，請參閱[跨多個雲端和內部部署資料庫使用 Azure SQL 資料同步 (預覽) 同步處理資料](sql-database-sync-data.md)。

## <a name="security-and-reliability"></a> 安全性與可靠性

### <a name="client-agent"></a>用戶端代理程式

-   使用具有網路服務存取權的最低權限帳戶安裝用戶端代理程式。

-   最好是在內部部署 SQL Server 電腦以外的電腦上安裝用戶端代理程式。

-   請勿向多個代理程式註冊內部部署資料庫。

-   即使是同步處理不同同步群組的不同資料表。

-   刪除其中一個同步群組時，向多個用戶端代理程式註冊內部部署資料庫會帶來挑戰。

### <a name="database-accounts-with-least-required-privilege"></a>最低必要權限的資料庫帳戶

-   **針對同步安裝**。 建立/更改資料表、更改資料庫、建立程序、選取/更改結構描述、建立使用者定義型別。

-   **針對持續同步**。對同步處理所選取的資料表以及對同步處理中繼資料與追蹤資料表的選取/插入/更新/刪除、對服務所建立之預存程序的執行權限、對使用者定義資料表型別的執行權限。

-   **針對取消佈建**。 在同步作業中對資料表中的更改、對同步處理中繼資料資料表的選取/刪除、對同步處理追蹤資料表、預存程序和使用者定義型別的控制。

**同步群組中的資料庫只有單一認證時如何使用這項資訊？**

-   變更不同階段的認證 (例如，credential1 適用於設定，credential2 適用於持續)。

-   變更認證的權限 (也就是在設定同步處理後變更權限)。

## <a name="locate-hub"></a> 要在哪裡尋找中樞資料庫

### <a name="enterprise-to-cloud-scenario"></a>企業至雲端案例

若要將延遲降至最低，請讓中樞資料庫靠近最集中的同步群組資料庫流量。

### <a name="cloud-to-cloud-scenario"></a>雲端至雲端案例

-   當同步群組中的所有資料庫都在一個資料中心時，中樞應該位於相同的資料中心。 此設定可減少延遲以及資料中心之間的資料傳輸成本。

-   同步群組中的資料庫在多個資料中心時，中樞應該位於與大多數的資料庫和資料庫流量相同的資料中心。

### <a name="mixed-scenarios"></a>混合案例

將上述指導方針套用於更複雜的同步群組設定。

## <a name="database-considerations-and-constraints"></a> 資料庫考量與限制

### <a name="sql-database-instance-size"></a>SQL Database 執行個體大小

當您建立新的 SQL Database 執行個體時，請設定大小上限，使它一律大於您部署之資料庫。 如果您未將大小上限設為大於已部署的資料庫，同步處理將會失敗。 雖然沒有自動成長 - 您可以執行 ALTER DATABASE 在建立之後增加資料庫的大小。 確定您在 SQL Database 執行個體的大小限制範圍內。

> [!IMPORTANT]
> SQL 資料同步會與每個資料庫一起儲存其他中繼資料。 請務必在計算所需的空間時，考量此中繼資料。 所增加的額外負荷量由資料表寬度 (例如，窄的資料表需要更多額外負荷) 和流量控管。

## <a name="table-considerations-and-constraints"></a> 資料表考量與限制

### <a name="selecting-tables"></a>選取資料表

並非資料庫中的所有資料表都必須在[同步群組](#sync-group)中。 選取哪些資料表要包含在同步群組中，以及哪些要排除 (或包含在不同的同步群組中)，可能會影響效率與成本。 請您只將企業需要的資料表，以及它們依賴的資料表，包含在同步群組中。

### <a name="primary-keys"></a>主索引鍵

同步群組中的每個資料表都必須有主索引鍵。 SQL 資料同步 (預覽) 服務無法同步處理沒有主索引鍵的任何資料表。

在實際執行之前，請測試初始和持續進行的同步處理效能。

## <a name="provisioning-destination-databases"></a> 佈建目的地資料庫

SQL 資料同步 (預覽) 預覽提供基本資料庫自動佈建。

本節將討論 SQL 資料同步 (預覽) 的佈建限制。

### <a name="auto-provisioning-limitations"></a>自動佈建限制

以下是 SQL 資料同步 (預覽) 的自動佈建限制。

-   只有選取的資料行會建立在目的地資料表中。
因此，如果某些資料行不是同步群組的一部分，那些資料行便不會佈建在目的地資料表中。

-   只會針對所選資料行建立索引。
如果來源資料表索引具有不是同步群組一部分的資料行，那些索引便不會佈建在目的地資料表中。

-   不會佈建 XML 型別資料行的索引。

-   不會佈建 CHECK 條件約束。

-   不會佈建來源資料表上的現有觸發程序。

-   檢視和預存程序不會建立在目的地資料庫上。

### <a name="recommendations"></a>建議

-   自動佈建功能僅適用於試用服務。

-   針對實際執行，您應該佈建資料庫結構描述。

## <a name="avoid-a-slow-and-costly-initial-synchronization"></a> 避免慢速和高成本的首次同步處理

本節討論同步處理群組的首次同步處理，以及您可以如何避免首次同步處理花費的時間比所需還多，以及成本超過應有水準。

### <a name="how-initial-synchronization-works"></a>首次同步處理如何運作

當您建立同步群組時，請從只有一個資料庫的資料開始。 如果您有多個資料庫中的資料，則 SQL 資料同步 (預覽) 會將每個資料列視為需要解決的衝突。 這項衝突解決會導致首次同步處理進展緩慢，需要數天到數個月，視資料庫大小而定。

此外，如果資料庫在不同的資料中心，首次同步處理的成本會比所需還多，因為每個資料列都必須在不同的資料中心之間傳送。

### <a name="recommendation"></a>建議

只要可能，便只從同步群組的其中一個資料庫的資料開始。

## <a name="design-to-avoid-synchronization-loops"></a> 避免同步處理迴圈的設計

同步迴圈會在同步群組內有循環參考時形成，一個資料庫中的每項變更都會循環且無止盡地複寫通過同步群組中的資料庫。 您想要避免同步迴圈，因為它們會降低效能，而且可能會大幅增加成本。

## <a name="avoid-out-of-date-databases-and-sync-groups"></a> 避免資料庫和同步群組過期

同步群組或同步群組中的資料庫可能會過期。 當同步群組的狀態為「過期」時，它會停止運作。 當資料庫的狀態為「過期」時，可能會遺失資料。 所以最好避免這些狀況，而不是必須從中復原。

### <a name="avoid-out-of-date-databases"></a>避免資料庫過期

資料庫已離線 45 天以上時，狀態會設定為過期。 確保沒有任何資料庫離線 45 天以上，以避免資料庫變成過期狀態。

### <a name="avoid-out-of-date-sync-groups"></a>避免同步群組過期

當同步群組內的任何變更無法傳播到其餘同步群組達到 45 天以上時，狀態設定為過期。 定期檢查同步群組的歷程記錄，以避免同步群組變成過期狀態。 請確定所有衝突都已解決，而且變更已成功傳播到同步群組資料庫。

同步群組可能無法套用變更的原因包括：

-   資料表之間的結構描述不相容。

-   資料表之間的資料不相容。

-   在不允許 null 值的資料行中插入具有 null 值的資料列。

-   使用違反外部索引鍵條件約束的值更新資料列。

您也可以避免同步群組過期：

-   更新結構描述，以允許失敗資料列中包含的值。

-   更新外部索引鍵值，以包含失敗資料列中包含的值。

-   更新失敗資料列中的資料值，以和目標資料庫中的結構描述或外部索引鍵相容。

## <a name="avoid-deprovisioning-issues"></a>避免取消佈建問題

在某些情況下，向用戶端代理程式取消註冊資料庫可能會導致同步處理失敗。

### <a name="scenario"></a>案例

1. 同步群組 A 已建立，具有 SQL Database 執行個體和內部部署 SQL Server 資料庫，它與本機代理程式 1 建立關聯。

2. 相同的內部部署資料庫已向本機代理程式 2 註冊 (此代理程式並未與任何同步群組建立關聯)。

3. 從本機代理程式 2 取消註冊內部部署資料庫，會移除內部部署資料庫的同步群組 A 的追蹤/中繼資料表。

4. 現在，同步群組 A 作業失敗，並發生下列錯誤：「目前的作業無法完成，因為資料庫未佈建以進行同步處理，或是您沒有同步設定資料表的權限。」

### <a name="solution"></a>方案

絕不向多個代理程式註冊資料庫，即可完全避免此狀況。

若要從這種情況中復原，請：

1. 從其所屬的每個同步群組移除資料庫。

2. 將資料庫新增回到您剛才從中移除的每個同步群組。

3. 部署每個受影響的同步群組 (會佈建資料庫)。

## <a name="handling-changes-that-fail-to-propagate"></a> 處理無法傳播的變更

### <a name="reasons-that-changes-fail-to-propagate"></a>未能傳播變更的原因

變更可能會因為許多原因而傳播失敗。 部分原因是：

-   結構描述/資料類型不相容。

-   嘗試在不可為 Null 的資料行中插入 null。

-   違反外部索引鍵條件約束。

### <a name="what-happens-when-changes-fail-to-propagate"></a>未能傳播變更時會發生什麼事？

-   同步群組顯示處於警告狀態。

-   詳細資料位於在入口網站 UI 記錄檢視器。

-   如果問題未解決達 45 天，資料庫會變成過期。

> [!NOTE]
> 這些變更無法傳播。 唯一的復原方法是重新建立同步群組。

### <a name="recommendation"></a>建議

透過入口網站和記錄介面定期監視同步群組和資料庫健康狀態。

## <a name="modifying-your-sync-group"></a> 修改同步群組

請勿嘗試從同步群組移除資料庫，然後編輯同步群組而不先部署其中一項變更。

首先，從同步群組移除資料庫。 然後部署變更，並等候取消佈建完成。 這項作業完成後，您可以編輯同步群組，並部署變更。

如果您嘗試移除資料庫，然後編輯同步群組而不先部署其中一項變更，則其中一項作業會失敗，入口網站介面也可能會進入不一致的狀態。 在此情況下，您可以重新整理頁面，還原正確狀態。

## <a name="next-steps"></a>後續步驟
如需 SQL 資料同步的詳細資訊，請參閱：

-   [使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步資料](sql-database-sync-data.md)
-   [開始使用 Azure SQL 資料同步](sql-database-get-started-sql-data-sync.md)
-   [對 Azure SQL 資料同步的問題進行疑難排解](sql-database-troubleshoot-data-sync.md)

-   示範如何設定 SQL 資料同步的完整 PowerShell 範例：
    -   [使用 PowerShell 在多個 Azure SQL Database 之間進行同步處理](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [使用 PowerShell 設定「資料同步」在內部部署的 Azure SQL Database 和 SQL Server 之間進行同步處理](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [下載 SQL 資料同步 REST API 文件](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

如需 SQL Database 的詳細資訊，請參閱：

-   [SQL Database 概觀](sql-database-technical-overview.md)
-   [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
