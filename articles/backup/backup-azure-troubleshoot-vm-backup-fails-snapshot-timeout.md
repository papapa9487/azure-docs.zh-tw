---
title: "針對 Azure 備份失敗：無法使用客體代理程式狀態進行疑難排解 | Microsoft Docs"
description: "與代理程式、延伸模組、磁碟相關之 Azure 備份失敗的徵狀、原因和解決方案"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
keywords: "Azure 備份; VM 代理程式; 網路連線;"
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: genli;markgal;
ms.openlocfilehash: f3195fa83479986a3e605abce618c78bcdb64dac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-backup-failure-issues-with-agent-andor-extension"></a>針對 Azure 備份失敗進行疑難排解：與代理程式和/或擴充功能相關的問題

本文提供疑難排解步驟，協助您解決與 VM 代理程式和擴充功能通訊問題相關的備份失敗。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>VM 代理程式無法與 Azure 備份通訊
在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 代理程式通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照，接著導致備份失敗。 請遵循下列疑難排解步驟中指定的順序，然後重試作業。
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 1：[VM 沒有網際網路存取權](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>原因 2：[代理程式已安裝到 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 3︰[VM 中安裝的代理程式已過時 (針對 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 4︰[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>原因 5︰[備份擴充功能無法更新或載入](#the-backup-extension-fails-to-update-or-load)

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>因為虛擬機器沒有網路連線，所以快照集作業失敗
在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照，接著導致備份失敗。 請遵循下列疑難排解步驟中指定的順序，然後重試作業。
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 1：[VM 沒有網際網路存取權](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 2︰[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-3-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>原因 3︰[備份擴充功能無法更新或載入](#the-backup-extension-fails-to-update-or-load)

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot 擴充作業失敗

在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照，接著導致備份失敗。 請遵循下列疑難排解步驟中指定的順序，然後重試作業。
##### <a name="cause-1-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 1︰[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-2-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>原因 2︰[備份擴充功能無法更新或載入](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 3：[VM 沒有網際網路存取權](#the-vm-has-no-internet-access)
##### <a name="cause-4-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>原因 4：[代理程式已安裝到 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-5-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 5︰[VM 中安裝的代理程式已過時 (針對 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)

## <a name="unable-to-perform-the-operation-as-the-vm-agent-is-not-responsive"></a>無法執行操作，因為 VM 代理程式沒有回應

在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照，接著導致備份失敗。 請遵循下列疑難排解步驟中指定的順序，然後重試作業。
##### <a name="cause-1-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>原因 1：[代理程式已安裝到 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 2︰[VM 中安裝的代理程式已過時 (針對 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 3：[VM 沒有網際網路存取權](#the-vm-has-no-internet-access)

## <a name="backup-failed-with-an-internal-error---please-retry-the-operation-in-a-few-minutes"></a>備份因為內部錯誤而失敗 - 請在幾分鐘內重試此作業

在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照，接著導致備份失敗。 請遵循下列疑難排解步驟中指定的順序，然後重試作業。
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 1：[VM 沒有網際網路存取權](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-installed-in-the-vm-but-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>原因 2：[代理程式已安裝到 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 3︰[VM 中安裝的代理程式已過時 (針對 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 4︰[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>原因 5︰[備份擴充功能無法更新或載入](#the-backup-extension-fails-to-update-or-load)

## <a name="the-specified-disk-configuration-is-not-supported"></a>系統不支援指定的磁碟設定

Azure 備份目前不支援容量 [大於 1023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm) 的磁碟。 
- 如果您有容量大於 1 TB 的磁碟，請[附加新磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)，磁碟需要小於 1 TB <br>
- 然後，將大於 1 TB 的磁碟中的資料複製到小於 1 TB 的新建磁碟。 <br>
- 請確認所有資料皆已複製，然後移除大於 1 TB 的磁碟
- 起始備份

## <a name="causes-and-solutions"></a>原因和解決方案

### <a name="the-vm-has-no-internet-access"></a>VM 沒有網際網路存取權
根據部署需求，VM 沒有網際網路存取權，或是有既有限制，防止存取 Azure 基礎結構。

備份擴充功能需要連線到 Azure 公用 IP 位址，才能正確運作。 擴充功能會將命令傳送至 Azure 儲存體端點 (HTTP URL) 來管理 VM 的快照。 如果擴充功能無法存取公用網際網路，備份最終會失敗。

####  <a name="solution"></a>方案
若要解決此問題，請嘗試下列其中一個方法。
##### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>允許存取 Azure 資料中心的 IP 範圍

1. 取得允許存取的 [Azure 資料中心 IP 清單](https://www.microsoft.com/download/details.aspx?id=41653)。
2. 在提升權限的 PowerShell 視窗中，於 Azure VM 中執行 **New-NetRoute** Cmdlet 來解除封鎖 IP。 以系統管理員身分執行 Cmdlet。
3. 若要允許存取 IP，可將規則新增到網路安全性群組 (如果您有一個)。

##### <a name="create-a-path-for-http-traffic-to-flow"></a>建立 HTTP 流量的行經路徑

1. 如果您已有網路限制 (例如，網路安全性群組)，請部署 HTTP Proxy 伺服器來路由傳送流量。
2. 若要允許從 HTTP Proxy 存取網際網路，可將規則新增到網路安全性群組 (如果您有一個)。

若要了解如何設定 VM 備份的 HTTP Proxy，請參閱[準備環境以備份 Azure 虛擬機器](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)。

如果您要使用受控磁碟，您可能必須在防火牆上開啟其他連接埠 (8443)。

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理程式已安裝到 VM 中，但沒有回應 (適用於 Windows VM)

#### <a name="solution"></a>方案
VM 代理程式可能已損毀，或服務可能已停止。 重新安裝 VM 代理程式有助於取得最新版本並重新啟動通訊。

1. 確認 Windows 客體代理程式服務是否在虛擬機器的服務 (services.msc) 中執行。 請嘗試重新啟動 Windows 客體代理程式服務並啟動備份<br>
2. 如果在服務中看不到，請在 [程式和功能] 中確認您是否已安裝 Windows 客體代理程式服務。
4. 如果您可以在 [程式和功能] 中看到，請將 Windows 客體代理程式解除安裝。
5. 下載並安裝[最新版的代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要有系統管理員權限，才能完成安裝。
6. 然後，您應該就能在服務中看到 Windows 客體代理程式服務
7. 在入口網站中按一下 [立即備份]，以嘗試執行隨選備份或臨機操作備份。

另請確認您的虛擬機器已**[在系統中安裝 .NET 4.5](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)**。 您必須安裝此程式，VM 代理程式才能與服務通訊

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安裝的代理程式已過時 (適用於 Linux VM)

#### <a name="solution"></a>方案
針對 Linux VM，與代理程式或擴充功能相關的多數失敗是由於會影響過時 VM 代理程式的問題所造成。 若要對此問題進行疑難排解，請遵循下列一般方針：

1. 請遵循[更新 Linux VM 代理程式](../virtual-machines/linux/update-agent.md)的指示。

 >[!NOTE]
 >我們強烈建議您只透過散發套件存放庫更新代理程式。 我們不建議直接從 GitHub 下載代理程式程式碼，並加以更新。 如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以取得如何進行安裝的指示。 若要檢查最新的代理程式，請移至 GitHub 儲存機制中的 [Microsoft Azure Linux 代理程式 (英文)](https://github.com/Azure/WALinuxAgent/releases) 頁面。

2. 執行下列命令，以確定 Azure 代理程式正在 VM 上執行：`ps -e`

 如果此程序並未執行，請使用下列命令來重新啟動它：

 * 針對 Ubuntu：`service walinuxagent start`
 * 針對其他散發套件︰`service waagent start`

3. [設定自動重新啟動代理程式](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 執行新的測試備份。 如果失敗持續發生，請收集下列來自客戶 VM 的記錄：

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

如果我們需要 waagent 的詳細資訊記錄，請遵循下列步驟：

1. 在 /etc/waagent.conf 檔案中，找出下一行︰**Enable verbose logging (y|n)**
2. 將 **Logs.Verbose** 值從 n 變更為 y。
3. 儲存變更，然後遵循本節前面的步驟，重新啟動 waagent。

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>無法擷取快照集狀態或無法取得快照集
VM 備份仰賴發給底層儲存體帳戶的快照命令。 備份可能會失敗，因為它無權存取儲存體帳戶，或是因為快照工作延遲執行。

#### <a name="solution"></a>方案
下列狀況可能導致快照集工作失敗：

| 原因 | 方案 |
| --- | --- |
| VM 已設定 SQL Server 備份。 | 根據預設，VM 備份會在 Windows VM 上執行 VSS 完整備份。 在執行以 SQL Server 為基礎的伺服器並設定了 SQL Server 備份的 VM 上，可能會發生快照延遲執行。<br><br>如果您因為快照問題而遇到備份失敗，請設定下列登錄機碼：<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| 因為 RDP 中的 VM 關機，而導致報告的 VM 狀態不正確。 | 如果您關閉遠端桌面通訊協定 (RDP) 中的 VM，請檢查入口網站，以判斷 VM 狀態是否正確。 如果不正確，可使用 VM 儀表板上的 [關閉] 選項來關閉入口網站中的 VM。 |
| 相同的雲端服務中的許多 VM 都設定為同時備份。 | 最佳做法是向外分配相同雲端服務中 VM 的備份排程。 |
| VM 正在以高 CPU 或記憶體使用量執行。 | 如果 VM 正在以高 CPU 使用量 (超過 90%) 或高記憶體使用量執行，即會將快照工作排入佇列並延遲，而其最終會逾時。在此情況下，請嘗試隨選備份。 |
| VM 無法從 DHCP 取得主機/網狀架構位址。 | 必須在來賓內啟用 DHCP，IaaS VM 備份才能運作。  如果 VM 無法從 DHCP 回應 245 取得主機/網狀架構位址，則無法下載或執行任何擴充功能。 如果您需要靜態私人 IP 位址，您應該透過平台來進行設定。 VM 內的 DHCP 選項應保持啟用。 如需詳細資訊，請參閱[設定靜態內部私人 IP 位址](../virtual-network/virtual-networks-reserved-private-ip.md)。 |

### <a name="the-backup-extension-fails-to-update-or-load"></a>備份擴充功能無法更新或載入
如果無法載入擴充功能，備份就會因為無法取得快照而失敗。

#### <a name="solution"></a>方案

 **Windows 客體：**確認 iaasvmprovider 服務已啟用，而且啟動類型為「自動」。 如果服務不是使用此方式所設定，請啟用該服務以判斷下一次備份是否成功。

 **Linux 客體：**VMSnapshot for Linux (備份所使用的擴充功能) 的最新版本是 1.0.91.0。<br>


如果還是無法更新或載入備份擴充功能，您可以透過解除安裝擴充功能來強制重新載入 VMSnapshot 擴充功能。 下一次的備份嘗試將會重新載入擴充功能。

若要解除安裝擴充功能，請執行下列動作：

1. 移至 [Azure 入口網站](https://portal.azure.com/)。
2. 找出具有備份問題的 VM。
3. 按一下 [設定] 。
4. 按一下 [擴充功能]。
5. 按一下 [Vmsnapshot 擴充功能]。
6. 按一下 [解除安裝]。

此程序會導致在下一次備份期間重新安裝擴充功能。

