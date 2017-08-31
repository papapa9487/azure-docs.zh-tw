
# 概觀
## [何謂 Azure 備份？](backup-introduction-to-azure-backup.md)

# 開始使用
## [備份 Azure VM](backup-azure-vms-first-look-arm.md)
## [備份 Windows Server 或 Windows 電腦](backup-try-azure-backup-in-10-mins.md)
## [備份到 VMware 伺服器](backup-azure-backup-server-vmware.md)

# 作法

## Azure 備份伺服器
### [Azure 備份伺服器保護矩陣](backup-mabs-protection-matrix.md)
### 安裝或升級
#### [在 Azure 入口網站中準備 Azure 備份伺服器工作負載](backup-azure-microsoft-azure-backup.md)
#### [在傳統入口網站中準備 Azure 備份伺服器工作負載](backup-azure-microsoft-azure-backup-classic.md)
#### [在 Azure 備份伺服器中新儲存體](backup-mabs-add-storage.md)
#### [將 Azure 備份伺服器升級至 V.2](backup-mabs-upgrade-to-v2.md)
#### [Azure 備份伺服器自動安裝](backup-mabs-unattended-install.md)
### 保護工作負載
#### [使用 Azure 備份伺服器備份 VMware 伺服器](backup-azure-backup-server-vmware.md)
#### [使用 Azure 備份伺服器備份 Exchange](backup-azure-exchange-mabs.md)
#### [使用 Azure 備份伺服器備份 SharePoint 陣列](backup-azure-backup-sharepoint-mabs.md)
#### [使用 Azure 備份伺服器備份 SQL](backup-azure-sql-mabs.md)
#### [保護系統狀態和裸機復原](backup-mabs-system-state-and-bmr.md)
### [從 Azure 備份伺服器復原資料](backup-azure-alternate-dpm-server.md)

## Azure VM
### 準備 VM
#### [準備 Resource Manager 部署的虛擬機器](backup-azure-arm-vms-prepare.md)
#### [Linux VM 的應用程式一致備份](backup-azure-linux-app-consistent.md)
#### [準備 Azure 虛擬機器](backup-azure-vms-prepare.md)
### 規劃您的環境
#### [規劃 VM 備份基礎結構](backup-azure-vms-introduction.md)
### 備份 VM
#### [將 Azure 虛擬機器備份到復原服務保存庫](backup-azure-arm-vms.md)
#### [備份加密的虛擬機器](backup-azure-vms-encryption.md)
#### [備份 Azure 虛擬機器](backup-azure-vms.md)
### 管理和監視 VM
#### [在 Azure 入口網站中管理 Azure VM 備份](backup-azure-manage-vms.md)
#### [在 Azure 入口網站中監視 Azure VM 備份的警示](backup-azure-monitor-vms.md)
#### [在傳統入口網站中管理及監視 Azure VM 備份](backup-azure-manage-vms-classic.md)
### 從 VM 還原資料
#### [從 Azure VM 備份中復原檔案](backup-azure-restore-files-from-vm.md)
#### [還原 Azure 入口網站中 Resource Manager 部署的 VM](backup-azure-arm-restore-vms.md)
#### [還原加密的虛擬機器](backup-azure-vms-encryption.md)
#### [還原 Azure 中的虛擬機器](backup-azure-restore-vms.md)
#### [還原已加密 VM 的 Key Vault 金鑰與密祕密](backup-azure-restore-key-secret.md)

## 設定 Azure 備份報告
### [設定 Azure 備份報告](backup-azure-configure-reports.md)
### [適用於 Azure 備份報告的資料模型](backup-azure-reports-data-model.md)
### [適用於 Azure 備份的 Log Analytics 資料模型](backup-azure-log-analytics-data-model.md)

## Data Protection Manager
### [在 Azure 入口網站中準備 DPM 工作負載](backup-azure-dpm-introduction.md)
### [在傳統入口網站中準備 DPM 工作負載](backup-azure-dpm-introduction-classic.md)
### [使用 System Center DPM 備份 Exchange Server](backup-azure-backup-exchange-server.md)
### [將資料復原至替代的 DPM 伺服器中](backup-azure-alternate-dpm-server.md)
### [使用 DPM 備份 SQL Server 工作負載](backup-azure-backup-sql.md)
### [使用 DPM 備份 SharePoint 伺服器陣列](backup-azure-backup-sharepoint.md)

## 使用 PowerShell
### [Azure 入口網站中的 Azure VM](backup-azure-vms-automation.md)
### [傳統入口網站中的 Azure VM](backup-azure-vms-classic-automation.md)
### [Azure 入口網站中的 DPM](backup-dpm-automation.md)
### [傳統入口網站中的 DPM](backup-dpm-automation-classic.md)
### [Azure 入口網站中的 Windows Server](backup-client-automation.md)
### [傳統入口網站中的 Windows Server](backup-client-automation-classic.md)

## Azure SQL Database
### [設定長期備份保留期](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [檢視復原服務保存庫中的備份](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [從長期備份保留期還原](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [刪除長期的 Azure SQL 備份資料](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Windows Server
### [備份 Windows Server 檔案和資料夾](backup-configure-vault.md)
### [備份 Windows Server 系統狀態](backup-azure-system-state.md)
### [將檔案從 Azure 復原至 Windows Server](backup-azure-restore-windows-server.md)
### [還原 Windows Server 系統狀態](backup-azure-restore-system-state.md)
### [監視及管理復原服務保存庫](backup-azure-manage-windows-server.md)
### 使用傳統入口網站進行備份與還原
#### [使用傳統部署模型的 Windows Server](backup-configure-vault-classic.md)
#### [使用傳統部署模型管理備份保存庫](backup-azure-manage-windows-server-classic.md)
#### [使用傳統部署模型將檔案復原到 Windows Server](backup-azure-restore-windows-server-classic.md)

## 復原服務保存庫
### [復原服務保存庫概觀](backup-azure-recovery-services-vault-overview.md)
### [將備份保存庫升級為復原服務保存庫](backup-azure-upgrade-backup-to-recovery-services.md)
### [刪除復原服務保存庫](backup-azure-delete-vault.md)

## 疑難排解
### [Azure 入口網站中的 Azure VM 備份問題](backup-azure-vms-troubleshoot.md)
### [傳統入口網站中的 Azure VM 備份問題](backup-azure-vms-troubleshoot-classic.md)
### [Azure VM 備份失敗︰無法與 VM 代理程式通訊來取得快照集狀態 - 快照集 VM 子工作已逾時](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Azure 備份中的檔案和資料夾備份速度緩慢](backup-azure-troubleshoot-slow-backup-performance-issue.md)
### [針對 Azure 備份伺服器進行疑難排解](backup-azure-mabs-troubleshoot.md)

# 概念

## 常見問題集
### [復原服務常見問題](backup-azure-backup-faq.md)
### [Azure VM 備份常見問題](backup-azure-vm-backup-faq.md)
### [使用 Azure 備份代理程式的檔案資料夾備份常見問題](backup-azure-file-folder-backup-faq.md)

## [角色型存取控制](backup-rbac-rs-vault.md)
## [混合式備份的安全性](backup-azure-security-feature.md)
## [設定離線備份](backup-azure-backup-import-export.md)
## [取代您的磁帶程式庫](backup-azure-backup-cloud-as-tape.md)


# 參考
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/)
## [MSDN 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [價格](https://azure.microsoft.com/pricing/details/backup/)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [服務更新](https://azure.microsoft.com/updates/?product=backup)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=backup)
