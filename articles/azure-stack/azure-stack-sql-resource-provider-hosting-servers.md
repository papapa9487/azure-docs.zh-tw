---
title: "Azure Stack 上的 SQL 主控伺服器 | Microsoft Docs"
description: "如何新增 SQL 執行個體以透過 SQL 配接器資源提供者佈建"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 58c83b74041e0e2e82729f569c53aca59f3aed43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="add-hosting-servers-for-use-by-the-sql-adapter"></a>新增供 SQL 配接器使用的主控伺服器

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您可以使用 [Azure Stack](azure-stack-poc.md) 內 VM 上的 SQL 執行個體，或 Azure Stack 環境外的執行個體，只要資源提供者能夠連線到該執行個體均可。 一般需求為：

* SQL 執行個體必須專門用於 RP 和使用者工作負載。 您無法使用任何其他取用者 (包括 App Service) 正在使用的 SQL 執行個體。
* RP 配接器未加入網域，並只可以使用 SQL 驗證進行連接。
* 您必須設定具有適當權限以供 RP 使用的帳戶。
* 從 RP 到 SQL 的網路流量會使用連接埠 1433，且無法變更。
* RP 和使用者 (例如 Web 應用程式) 會使用使用者網路，因此需要連線到此網路上的 SQL 執行個體。 這項需求通常表示您的 SQL 執行個體 IP 必須在公用網路上。
* SQL 執行個體和其主機的管理在您；RP 不會執行修補、備份、認證輪替等作業。
* SKU 可以用來建立不同類別的 SQL 能力，例如效能、Always On 等等。



您可以透過 Marketplace 管理功能取得一些 SQL IaaS 虛擬機器映像。 請確定在使用 Marketplace 項目部署 VM 之前，一律會下載最新版的 SQL IaaS 延伸模組。 SQL 映像與 Azure 中提供的 SQL VM 相同。 針對從這些映像建立的 SQL VM，IaaS 延伸模組和對應的入口網站增強功能，可提供自動修補和備份功能之類的功能。

有其他選項可用於部署 SQL VM，包含 [Azure Stack 快速入門資源庫](https://github.com/Azure/AzureStack-QuickStart-Templates)中的範本。

> [!NOTE]
> 安裝在多節點 Azure Stack 上的任何主控伺服器，必須透過使用者訂用帳戶建立。 無法從預設提供者訂用帳戶加以建立。 必須透過使用者入口網站或透過具有適當登入的 PowerShell 工作階段來加以建立。 所有主控伺服器都是可計費的 VM，而且必須具有適當的 SQL 授權。 服務管理員_可以_是該訂用帳戶的擁有者。


### <a name="required-privileges"></a>需要的權限

您可以建立具有較完整系統管理員更低權限的新系統管理使用者。 需要允許的特定作業為：

- 資料庫：建立、改變、具有內含項目 (僅限 Always On)、卸除、備份
- 可用性群組：改變、聯結、新增/移除資料庫
- 登入：建立、選取、改變、卸除、撤銷
- 選取作業：\[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn)、sys.availability_replicas (AlwaysOn)、sys.databases、\[master\].\[sys\].\[dm_os_sys_memory\]、SERVERPROPERTY、\[master\].\[sys\].\[availability_groups\] (AlwaysOn)、sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>透過連線到獨立式主控 SQL 伺服器來提供容量
您可以使用運用任何版本的 SQL Server 2014 或 SQL Server 2016 的獨立 (非 HA) SQL 伺服器。 確定您擁有具備系統管理員權限之帳戶的認證。

若要新增已佈建的獨立主控伺服器，請遵循下列步驟：

1. 以服務管理員身分登入 Azure Stack 管理入口網站

2. 按一下 [瀏覽]&gt;[管理資源]&gt;[SQL 主控伺服器]。

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  您可以使用 [SQL 主控伺服器] 刀鋒視窗，將 SQL Server 資源提供者連線到作為資源提供者後端的 SQL Server 實際執行個體。

  ![主控伺服器](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. 在表單中填寫 SQL Server 執行個體的連線詳細資料。

  ![新的主控伺服器](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

  > [!NOTE]
  > 只要使用者和管理 Azure Resource Manager 可以存取 SQL 執行個體，資源提供者就可以控制它。 SQL 執行個體__必須__配置為 RP 專屬。

4. 新增伺服器時，必須將它們指派給新的或現有的 SKU，以將服務產品差異化。 例如，您可以有一個 SQL Enterprise 執行個體，提供下列：
  - 資料庫容量
  - 自動備份
  - 為個別部門保留高效能伺服器
  - 依此類推。

  SKU 名稱應反映屬性，讓使用者可適當地安置其資料庫。 且 SKU 中的所有主控伺服器都應具有相同的功能。

    例如：

    ![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
最多需要一小時才能在入口網站中看到 SKU。 在完整建立 SKU 前，您無法建立資料庫。

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>使用 SQL Always On 可用性群組提供容量
設定 SQL Always On 執行個體需要額外的步驟，並包含至少三個 VM (或實體機器)。

> [!NOTE]
> 針對 Always On，SQL 配接器 RP _僅_支援 SQL 2016 SP1 企業版或更新版本的執行個體，因為它需要新的 SQL 功能，例如自動植入。 除了上述的一般需求清單：

* 除了 SQL Always On 電腦，您還必須提供檔案伺服器。 有一個 [Azure Stack 快速入門範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha)，可以為您建立此環境。 它也可做為建置您自己的執行個體之指南。

* 您必須設定 SQL 伺服器。 具體來說，您必須在 SQL Server 的每個執行個體的每個可用性群組上啟用[自動植入](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)。

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

在次要執行個體上
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



若要新增 SQL Always On 主控伺服器，請遵循下列步驟：

1. 以服務管理員身分登入 Azure Stack 管理入口網站

2. 按一下 [瀏覽] &gt; [管理資源] &gt; [SQL 主控伺服器] &gt; [+新增]。

    您可以使用 [SQL 主控伺服器] 刀鋒視窗，將 SQL Server 資源提供者連線到作為資源提供者後端的 SQL Server 實際執行個體。


3. 使用 SQL Server 執行個體的連線詳細資料填寫表單，務必使用 Always On 接聽程式的 FQDN 或 IPv4 位址。 提供您以系統管理員權限設定之帳戶的帳戶資訊。

4. 選取此方塊即可啟用 SQL Always On 可用性群組執行個體的支援。

    ![主控伺服器](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. 將 SQL Always On 執行個體新增至 SKU。 您不能在相同 SKU 中混合獨立伺服器與 Always On 執行個體。 那是在新增第一部主控伺服器時決定。 之後嘗試混合類型會導致錯誤。


## <a name="making-sql-databases-available-to-users"></a>將 SQL 資料庫提供給使用者使用

建立方案和供應項目，將 SQL 資料庫提供給使用者使用。 將 Microsoft.SqlAdapter 服務新增到方案，並新增現有的配額或建立新的配額。 如果建立配額，就可以指定容量以允許使用者使用。

![建立方案和產品來加入資料庫](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="maintenance-of-the-sql-adapter-rp"></a>SQL 配接器 RP 的維護

在這裡未涵蓋 SQL 執行個體的維護，密碼輪替資訊除外。 您需責任修補和備份/復原與 SQL 配接器搭配使用的資料庫伺服器。

### <a name="patching-and-updating"></a>修補和更新
 SQL 配接器不會隨著 Azure Stack 提供，因為它是附加元件。 Microsoft 將視需要為 SQL 配接器提供更新。 SQL 配接器會在預設提供者訂用帳戶底下的 [使用者] 虛擬機器上具現化。 因此，就必須提供 Windows 修補程式、防毒特徵標記等等。隨著修補和更新週期提供的 Windows 更新套件可用來將更新套用至 Windows VM。 釋出更新的配接器時，將提供指令碼來套用更新。 此指令碼會建立新的 RP VM，並移轉您已經具有的任何狀態。

 ### <a name="backuprestoredisaster-recovery"></a>備份/還原/災害復原
 SQL 配接器不會隨著 Azure Stack BC-DR 程序進行備份，因為它是附加元件。 會提供指令碼，以便：
- 備份必要的狀態資訊 (儲存在 Azure Stack 儲存體帳戶中)
- 在完整的堆疊復原變得必要時還原 RP。
必須先復原資料庫伺服器 (如有必要)，才能復原 RP。

### <a name="updating-sql-credentials"></a>更新 SQL 認證

您需負責建立及維護 SQL 伺服器上的系統管理員帳戶。 RP 需要具有這些權限的帳戶，才能代表使用者管理資料庫 - 不需要存取那些資料庫中的資料。 如果需要更新 SQL 伺服器的 sa 密碼，您可以使用 RP 管理員介面的更新功能來變更 RP 所使用的已儲存密碼。 這些密碼會儲存在 Azure Stack 執行個體上的金鑰保存庫中。

若要修改設定，請按一下 [瀏覽] &gt; [管理資源] &gt; [SQL 主控伺服器] &gt; [SQL 登入] 並選取登入名稱。 必須先在 SQL 執行個體上 (必要時還需在任何複本上) 進行變更。 在 [設定] 面板中，按一下 [密碼]。

![更新管理員密碼](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)


## <a name="next-steps"></a>後續步驟

[新增資料庫](azure-stack-sql-resource-provider-databases.md)
