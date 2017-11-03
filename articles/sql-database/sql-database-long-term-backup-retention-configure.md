---
title: "設定長期備份保留 - Azure SQL Database | Microsoft Docs"
description: "了解如何從在 Azure 復原服務保存庫中儲存自動備份，以及從 Azure 復原服務保存庫還原"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: business continuity
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: carlrab
ms.openlocfilehash: 9b218756277e52a4d582b1e8e42200f78d38580e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="configure-and-restore-from-azure-sql-database-long-term-backup-retention"></a>設定 Azure SQL Database 長期備份保留並從中還原

您可以設定 Azure 復原服務保存庫以存放 Azure SQL 資料庫備份，然後使用透過 Azure 入口網站或 PowerShell 保留在保存庫中的備份，來復原資料庫。

## <a name="azure-portal"></a>Azure 入口網站

下列幾節中會示範如何使用 Azure 入口網站來設定 Azure 復原服務保存庫、檢視保存庫中的備份，以及從保存庫中還原。

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>設定保存庫、註冊伺服器及選取資料庫

您會[設定 Azure 復原服務保存庫以保留自動備份](sql-database-long-term-retention.md)，時間較您服務層的保留期限長。 

1. 開啟您伺服器的 **SQL Server** 頁面。

   ![SQL Server 頁面](./media/sql-database-get-started-portal/sql-server-blade.png)

2. 按一下 [長期備份保留]。

   ![長期備份保留連結](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. 在伺服器的 [長期備份保留] 頁面中，檢閱並接受預覽條款 (除非您已完成，或預覽中沒有這項功能)。

   ![接受預覽條款](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. 若要設定長期備份保留時間，在方格中選取該資料庫，然後按一下工具列上的 [設定]。

   ![選取長期備份保留的資料庫](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. 在 [設定] 頁面上，按一下 [復原服務保存庫] 下的 [設定所需設定]。

   ![設定保存庫連結](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. 在 [復原服務保存庫] 頁面中，選取現有的保存庫 (若有)。 否則，如果找不到您訂用帳戶的復原服務保存庫，按一下以結束流程，並建立復原服務保存庫。

   ![建立保存庫連結](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. 在 [復原服務保存庫] 頁面上，按一下 [新增]。

   ![新增保存庫連結](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. 在 [復原服務保存庫] 頁面上，提供復原服務保存庫的有效名稱。

   ![新增保存庫名稱](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. 選取您的訂用帳戶和資源群組，然後選取保存庫的位置。 完成時，按一下 [建立]。

   ![建立保存庫](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > 保存庫必須位於與 Azure SQL 邏輯伺服器相同的區域，而且必須使用相同的資源群組做為邏輯伺服器。
   >

10. 建立新的保存庫之後，執行必要的步驟以返回 [復原服務保存庫] 頁面。

11. 在 [復原服務保存庫] 頁面上，按一下保存庫，然後按一下 [選取]。

   ![選取現有的保存庫](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. 在 [設定] 頁面上，提供新保留原則的有效名稱、適當修改預設保留原則，然後按一下 [確定]。

   ![定義保留原則](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. 在伺服器的 [長期備份保留] 頁面上，按一下 [儲存]，然後按一下 [確定]，將長期備份保留原則套用到所有選取的資料庫。

   ![定義保留原則](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. 按一下 [儲存] 以使用這個新的原則對您所設定的 Azure 復原服務保存庫啟用長期備份保留。

   ![定義保留原則](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> 設定之後，備份會在接下來七天內顯示於保存庫中。 備份出現在保存庫之前，請勿繼續本教學課程。
>

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>使用 Azure 入口網站在長期保留期限中檢視備份

檢視[長期備份保留](sql-database-long-term-retention.md)中資料庫備份的相關資訊。 

1. 在 Azure 入口網站中，開啟資料庫備份的 Azure 復原服務保存庫 (移至 [所有資源]，並從您訂用帳戶的資源清單中選取)，以在保存庫中檢視資料庫備份所使用的儲存體數量。

   ![使用備份檢視復原服務保存庫](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. 開啟您資料庫的 [SQL Database] 頁面。

   ![新範例 DB 頁面](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. 在工具列上，按一下 [還原]。

   ![還原工具列](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. 在 [還原] 頁面上，按一下 [長期]。

5. 在 Azure 保存庫備份下，按一下 [選取備份]，在長期備份保留中檢視可用的資料庫備份。

   ![保存庫中的備份](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>使用 Azure 入口網站從長期備份保留的備份中還原資料庫

從 Azure 復原服務保存庫中的備份將資料庫還原到新的資料庫。

1. 在 [Azure 保存庫備份] 頁面上，按一下要還原的備份，然後按一下 [選取]。

   ![選取保存庫中的備份](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. 在 [資料庫名稱] 文字方塊中，提供還原的資料庫名稱。

   ![新的資料庫名稱](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. 按一下 [確定]，從保存庫中的備份將資料庫還原到新的資料庫。

4. 在工具列上，按一下 [通知] 圖示以檢視還原作業的狀態。

   ![從保存庫還原作業進度](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 完成還原作業之後，開啟 [SQL Database] 頁面，以檢視剛還原的資料庫。

   ![從保存庫還原的資料庫](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> 從這裡開始，您可以使用 SQL Server Management Studio 連接到已還原的資料庫來執行所需的工作，例如[從還原的資料庫擷取一堆資料來複製到現有的資料庫，或刪除現有的資料庫，並將還原的資料庫重新命名為現有的資料庫名稱](sql-database-recovery-using-backups.md#point-in-time-restore)。
>

## <a name="powershell"></a>PowerShell

下列幾節中會示範如何使用 PowerShell 來設定 Azure 復原服務保存庫、檢視保存庫中的備份，以及從保存庫中還原。

### <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

使用 [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) 來建立復原服務保存庫。

> [!IMPORTANT]
> 保存庫必須位於與 Azure SQL 邏輯伺服器相同的區域，而且必須使用相同的資源群組做為邏輯伺服器。

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>設定您的伺服器設以將復原保存庫用於其長期保留備份

使用 [Set-AzureRmSqlServerBackupLongTermRetentionVault](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) Cmdlet，讓先前建立的復原服務保存庫與特定 Azure SQL Server 產生關聯。

```PowerShell
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>建立保留原則

保留原則就是您設定資料庫備份保留時間長度的位置。 使用 [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) Cmdlet 來取得預設保留原則，以便作為建立原則的範本。 在此範本中，保留期限設定為 2 年。 接下來，執行 [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) 完成原則建立。 

> [!NOTE]
> 有些 Cmdlet 會要求您先設定保存庫內容，然後才執行 ([Set-AzureRmRecoveryServicesVaultContext](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext))，所以您會在幾個相關的程式碼片段中看見此 Cmdlet。 因為原則是保存庫的一部分，所以我們會設定內容。 您可以為每個保存庫建立多個保留原則，然後將所需的原則套用到特定資料庫。 


```PowerShell
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>設定資料庫以使用先前定義的保留原則

使用 [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) Cmdlet，將新原則套用到特定資料庫。

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>檢視備份資訊和長期保留中的備份

檢視[長期備份保留](sql-database-long-term-retention.md)中資料庫備份的相關資訊。 

請使用下列 Cmdlet 來檢視備份資訊︰

- [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)

```PowerShell
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore

# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>從長期備份保留備份中還原資料庫

使用 [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) Cmdlet 從長期備份保留中還原。

```PowerShell
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> 從這裡開始，您可以使用 SQL Server Management Studio 連線到已還原的資料庫來執行所需的工作，例如從還原的資料庫擷取一堆資料來複製到現有的資料庫，或刪除現有的資料庫，並將還原的資料庫重新命名為現有的資料庫名稱。 請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解服務產生的自動備份，請參閱[自動備份](sql-database-automated-backups.md)
- 若要深入了解長期備份保留，請參閱[長期備份保留](sql-database-long-term-retention.md)
- 若要深入了解從備份還原，請參閱[從備份還原](sql-database-recovery-using-backups.md)
