---
title: "針對虛擬機器與實體伺服器的保護進行監視和疑難排解 | Microsoft Docs"
description: "Azure Site Recovery 可將內部部署伺服器上的虛擬機器複寫、容錯移轉及復原協調至 Azure 或次要資料中心。 使用這篇文章針對 Virtual Machine Manager 或 Hyper-V 網站保護進行監視和疑難排解。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 0fc8e368-0c0e-4bb1-9d50-cffd5ad0853f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.openlocfilehash: ad1e0bcb2e2c073c8fb186f5a9d8bcb0bac588a0
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>監視和疑難排解虛擬機器與實體伺服器的保護
此監視與疑難排解指南協助您了解如何追蹤複寫健康情況和 Azure Site Recovery 的疑難排解技術。

## <a name="understand-the-components"></a>了解元件
### <a name="vmware-virtual-machine-or-physical-server-site-deployment-for-replication-between-on-premises-and-azure"></a>用於在內部部署和 Azure 之間複寫的 VMware 虛擬機器或實體網站部署
若要設定內部部署 VMware 虛擬機器或實體伺服器與 Azure 之間的資料庫復原，您需要在虛擬機器或伺服器上設定組態伺服器、主要目標伺服器和處理伺服器元件。 當您啟用來源伺服器的保護時，如果尚未部署最新的版本，則 Azure Site Recovery 會從選擇的處理伺服器安裝行動服務。 在內部部署中斷和來源伺服器容錯移轉至 Azure 之後，客戶必須在 Azure 中設定處理伺服器，並在內部部署中設定主要目標伺服器，以便在內部部署中重建來源伺服器。

![VMware/Physical site deployment for replication between on-premises and Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-sites"></a>用於在內部部署網站之間複寫的 Virtual Machine Manager 網站部署
若要設定兩個內部部署位置之間的資料庫復原，您需要下載 Azure Site Recovery 提供者，並將它安裝在 Virtual Machine Manager 伺服器上。 提供者需要網際網路連線，以確保從 Azure 入口網站觸發的所有作業，都能轉譯為內部部署作業。

![用於在內部部署網站之間複寫的 Virtual Machine Manager 網站部署](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>用於在內部部署位置和 Azure 之間複寫的 Virtual Machine Manager 網站部署
當您設定內部部署位置和 Azure 之間的資料庫復原時，您需要下載 Azure Site Recovery 提供者，並將它安裝在 Virtual Machine Manager 伺服器上。 您也需要安裝 Azure 復原服務代理程式，它必須安裝在每一部 Hyper-V 主機上。 [深入了解](site-recovery-hyper-v-azure-architecture.md)以取得詳細資訊。

![用於在內部部署位置和 Azure 之間複寫的 Virtual Machine Manager 網站部署](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>用於在內部部署位置和 Azure 之間複寫的 Hyper-V 網站部署
此程序類似於 Virtual Machine Manager 部署。 唯一的差別是 Azure Site Recovery 提供者和 Azure 復原服務代理程式會安裝在 Hyper-V 主機本身。 [深入了解](site-recovery-hyper-v-azure-architecture.md)。 。

## <a name="monitor-configuration-protection-and-recovery-operations"></a>監視組態、保護和復原作業
Azure Site Recovery 中的每個作業都是在 [作業] 索引標籤下稽核和追蹤。發生任何組態、保護或復原錯誤時，請移至 [作業] 索引標籤來尋找失敗。

![The Failed filter in the JOBS tab](media/site-recovery-monitoring-and-troubleshooting/image3.png)

如果您在 [作業] 索引標籤下找到失敗，請按一下作業，然後按一下該作業的 [錯誤詳細資料]。

![The ERROR DETAILS button](media/site-recovery-monitoring-and-troubleshooting/image4.png)

錯誤詳細資料可協助您識別問題的可能原因及建議。

![Dialog box that shows error details for a specific job](media/site-recovery-monitoring-and-troubleshooting/image5.png)

在前一個範例中，另一個進行中的作業似乎導致保護組態失敗。 根據建議來解決問題，然後按一下 [重新啟動] 重新起始作業。

![The RESTART button in the JOBS tab](media/site-recovery-monitoring-and-troubleshooting/image6.png)

並非所有作業都有 [重新啟動] 選項。 如果作業沒有 [重新啟動] 選項，請返回至物件，並重做一次作業。 您可以使用 [取消] 按鈕取消任何進行中的作業。

![The CANCEL button](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machines"></a>監視虛擬機器的複寫健康情況
您可以使用 Azure 入口網站從遠端監視 Azure Site Recovery 提供者的每個受保護實體。 按一下 [受保護項目]，然後按一下 [VMM 雲端] 或 [保護群組]。 [VMM 雲端] 索引標籤只適用於以 Virtual Machine Manager 為基礎的部署。 在其他情況下，受保護實體都在 [保護群組] 索引標籤底下。

![The VMM Clouds and PROTECTION GROUPS options](media/site-recovery-monitoring-and-troubleshooting/image8.png)

按一下個別雲端或或保護群組下的受保護實體，可看到下方窗格中顯示所有可用的作業。

![Available options for a selected protected entity](media/site-recovery-monitoring-and-troubleshooting/image9.png)

如上一個螢幕擷取畫面所示，虛擬機器健康情況是 [嚴重]。 您可以按一下底部的 [錯誤詳細資料] 按鈕查看錯誤。 根據**可能的原因**和**建議**解決問題。

![The Error Details button](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Errors and recommendations in the Error Details dialog box](media/site-recovery-monitoring-and-troubleshooting/image11.png)

> [!NOTE]
> 如果有任何作用中的作業正在進行中或失敗，請移至 [作業] 檢視，如先前所述，以檢視特定作業的錯誤。
>
>

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>疑難排解內部部署 Hyper-V 問題
連接到內部部署 Hyper-V 管理員主控台，選取虛擬機器，然後查看複寫健康情況。

![Option to view replication health in the Hyper-V manager console](media/site-recovery-monitoring-and-troubleshooting/image12.png)

在此案例中，[複寫健康情況] 是 [嚴重]。 以滑鼠右鍵按一下虛擬機器，然後按一下 複寫 > 檢視複寫健康情況，以檢視詳細資訊。

![Replication health for a specific virtual machine](media/site-recovery-monitoring-and-troubleshooting/image13.png)

如果虛擬機器已暫停複寫，請以滑鼠右鍵按一下虛擬機器，然後按一下 [複寫] > [繼續複寫]。

![Option to resume replication in the Hyper-V manager console](media/site-recovery-monitoring-and-troubleshooting/image19.png)

如果虛擬機器移轉的新 Hyper-V 主機位於叢集或獨立電腦中，且 Hyper-V 主機是透過 Azure Site Recovery 所設定，則不會影響虛擬機器的複寫。 請確定新的 Hyper-V 主機符合所有必要條件，而且是使用 Azure Site Recovery 來設定。

### <a name="event-log"></a>事件記錄檔
| 事件來源 | 詳細資料 |
| --- |:--- |
| **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** (Virtual Machine Manager 伺服器) |提供有用的記錄，可針對許多不同的 Virtual Machine Manager 問題進行疑難排解。 |
| **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (Hyper-V 主機) |提供有用的記錄，可針對許多 Microsoft Azure 復原服務代理程式問題進行疑難排解。 <br/> ![Location of Replication event source for Hyper-V host](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** (Hyper-V 主機) |提供有用的記錄，可針對許多 Microsoft Azure Site Recovery 服務問題進行疑難排解。 <br/> ![Location of Operational event source for Hyper-V host](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V 主機) |提供有用的記錄，可針對許多 Hyper-V 虛擬機器管理問題進行疑難排解。 <br/> ![Location of Virtual Machine Manager event source for Hyper-V host](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |

### <a name="hyper-v-replication-logging-options"></a>Hyper-V 複寫記錄選項
與 Hyper-V 複寫有關的所有事件都記錄在位於 Applications and Services Logs\\Microsoft\\Windows 底下的 Hyper-V-VMMS\\Admin 記錄中。 此外，您還可以啟用 Hyper-V 虛擬機器管理服務的分析記錄檔。 若要啟用此記錄檔，先讓 [分析] 與 [偵錯] 記錄檔可在 [事件檢視器] 中檢視。 開啟 [事件檢視器]，然後按一下 [檢視] > [顯示分析與偵錯記錄檔]。

![The Show Analytic and Debug Logs option](media/site-recovery-monitoring-and-troubleshooting/image14.png)

在 **Hyper-V-VMMS** 底下可看到 [分析] 記錄檔。

![The Analytic log in the Event Viewer tree](media/site-recovery-monitoring-and-troubleshooting/image15.png)

在 [動作] 窗格中，按一下 [啟用記錄檔]。 啟用之後，它會在 [效能監視器] 中顯示為 [資料收集器集合工具] 底下的 [事件追蹤工作階段]。

![Event Trace Sessions in the Performance Monitor tree](media/site-recovery-monitoring-and-troubleshooting/image16.png)

若要檢視收集到的資訊，請先停用記錄檔來停止追蹤工作階段。 儲存記錄檔，然後在事件檢視器中重新開啟它，或視需要使用其他工具來轉換它。

## <a name="reach-out-for-microsoft-support"></a>連絡 Microsoft 支援
### <a name="log-collection"></a>記錄檔集合
對於 Virtual Machine Manager 網站保護，請參閱[使用支援診斷平台 (SDP) 工具收集 Azure Site Recovery 記錄檔](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)，以收集必要的記錄檔。

對於 Hyper-V 網站保護，請下載[工具](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)並在 Hyper-V 主機上執行，以收集記錄檔。

對於 VMware/實體伺服器案例，請參閱[針對 VMware 和實體網站保護收集 Azure Site Recovery 記錄檔](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx)，以收集必要的記錄檔。

此工具會在本機收集記錄檔，並放在 %LocalAppData%\ElevatedDiagnostics 底下隨機命名的子資料夾中。

![從 Hyper-V 網站保護顯示的範例步驟。](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="open-a-support-ticket"></a>開啟支援票證
若要提交有關於 Azure Site Recovery 的支援票證，請使用 <http://aka.ms/getazuresupport> URL 來連絡 Azure 支援。

## <a name="kb-articles"></a>知識庫文章
* [如何保留已容錯移轉或移轉至 Azure 的受保護虛擬機器的磁碟機代號](http://support.microsoft.com/kb/3031135)
* [如何管理內部部署至 Azure 保護網路頻寬使用](https://support.microsoft.com/kb/3056159)
* [Azure Site Recovery：當您嘗試啟用虛擬機器的保護時，發生「找不到叢集資源」錯誤](http://support.microsoft.com/kb/3010979)
* [了解及疑難排解 Hyper-V 複寫指南](http://social.technet.microsoft.com/wiki/contents/articles/21948.hyper-v-replica-troubleshooting-guide.aspx)

## <a name="common-azure-site-recovery-errors-and-their-resolutions"></a>Azure Site Recovery 的常見錯誤和其解決方式
以下是常見的錯誤及其解決方案。 每個錯誤都載明於個別的 wiki 頁面中。

### <a name="general"></a>一般
* <span style="color:green;">NEW</span> [作業失敗，發生錯誤「作業正在進行中。」錯誤 505、514、532。](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
* <span style="color:green;">NEW</span> [作業失敗，發生錯誤「伺服器未連接到網際網路」。錯誤 25018。](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>設定
* [無法註冊 Virtual Machine Manager 伺服器，因為發生內部錯誤。如需此錯誤的詳細資訊，請參閱 Site Recovery 入口網站中的作業檢視。再次執行安裝程式以註冊伺服器。](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
* [無法連接到 Hyper-V 復原管理員保存庫。請確認 Proxy 設定，或稍後再試一次。](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>組態
* [無法建立保護群組：擷取伺服器清單時發生錯誤。](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
* [Hyper-V 主機叢集至少包含一個靜態網路介面卡，或未設定連線的介面卡來使用 DHCP。](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
* [ 沒有完成動作的權限。](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
* [設定保護時無法選取訂用帳戶內的儲存體帳戶。](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>保護
* <span style="color:green;">NEW</span> [啟用保護失敗，發生錯誤「保護無法針對虛擬機器設定」。錯誤 60007、40003。](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
* <span style="color:green;">NEW</span> [啟用保護失敗，發生錯誤「保護無法針對虛擬機器啟用。」錯誤 70094。](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
* <span style="color:green;">NEW</span> [即時移轉錯誤 23848 - 虛擬機器即將使用 Live 類型移動。這可能會中斷虛擬機器的復原保護狀態。](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx)
* [啟用保護失敗，因為主機電腦上未安裝代理程式。](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
* [找不到複本虛擬機器的適當主機 - 因為計算資源不足。](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
* [找不到複本虛擬機器的適當主機 - 因為未連接邏輯網路。](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
* [無法連接到複本主機電腦 - 無法建立連線](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### <a name="recovery"></a>復原
* Virtual Machine Manager 無法完成主機作業︰
  * [容錯移轉至虛擬機器的所選復原點：一般拒絕存取錯誤。](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
  * [Hyper-V 無法容錯移轉至所選取的虛擬機器復原點：作業中止。請嘗試較新的復原點。(0x80004004)。](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
  * 無法與伺服器建立連接 (0x00002EFD)。
    * [Hyper-V 無法啟用虛擬機器的反向複寫。](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
    * [Hyper-V 無法啟用虛擬機器的複寫。](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
  * [無法認可虛擬機器的容錯移轉。](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
* [復原計劃包含未準備好進行規劃之容錯移轉的虛擬機器。](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
* [虛擬機器未準備好進行規劃的容錯移轉。](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* [虛擬機器並未執行，而且未關閉電源。](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
* [虛擬機器上發生頻外作業且認可容錯移轉失敗。](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* 測試容錯移轉
  * [無法起始容錯移轉，因為正在進行測試容錯移轉。](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
* <span style="color:green;">NEW</span> 容錯移轉因為與虛擬機器或機器所屬子網路相關聯的網路安全性群組的組態設定而逾時：「PreFailoverWorkflow 工作 WaitForScriptExecutionTaskTimeout」。 如需詳細資訊，請參閱 [PreFailoverWorkflow 工作 WaitForScriptExecutionTaskTimeout](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) 。

### <a name="configuration-server-process-server-master-target"></a>組態伺服器、處理伺服器、主要目標
* [將 PS/CS 做為 VM 加以管理的 ESXi 主機失敗，並且出現紫色的當機螢幕。](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>容錯移轉之後的遠端桌面疑難排解
* 許多客戶在 Azure 中連接到容錯移轉的虛擬機器時都遭遇問題。 [使用疑難排解文件以 RDP 連接至虛擬機器](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>將公用 IP 新增到資源管理員虛擬機器上
如果入口網站中的 [連接] 按鈕停用，而且您未透過 Express Route 或網站間 VPN 連接來連接到 Azure，則您必須先建立虛擬機器並指派公用 IP 位址給它，才能使用遠端桌面/共用殼層。 然後，您就可以將公用 IP 新增到虛擬機器的網路介面。  

![將公用 IP 新增到容錯移轉虛擬機器的網路介面上](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)
