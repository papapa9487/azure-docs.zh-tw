---
title: "針對 Azure 虛擬機器的備份錯誤進行疑難排解 | Microsoft Docs"
description: "Azure 虛擬機器備份與還原的疑難排解"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 73214212-57a4-4b57-a2e2-eaf9d7fde67f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: trinadhk;markgal;jpallavi;
ms.openlocfilehash: 096c97f4cb41ff8df2e646f59dbc0bf845721ac7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure 虛擬機器備份的疑難排解
> [!div class="op_single_selector"]
> * [復原服務保存庫](backup-azure-vms-troubleshoot.md)
> * [備份保存庫](backup-azure-vms-troubleshoot-classic.md)
>
>

您可以疑難排解將 Azure 備份使用於下表所列資訊時發生的錯誤。

## <a name="backup"></a>備份

### <a name="error-the-specified-disk-configuration-is-not-supported"></a>錯誤：系統不支援指定的磁碟設定

Azure 備份目前不支援容量 [大於 1023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm) 的磁碟。 
- 如果您有容量大於 1 TB 的磁碟，請[附加新磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)，磁碟需要小於 1 TB <br>
- 然後，將大於 1 TB 的磁碟中的資料複製到小於 1 TB 的新建磁碟。 <br>
- 請確認所有資料皆已複製，然後移除大於 1 TB 的磁碟
- 起始備份。

| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 無法執行作業，因為 VM 已不存在。 - 停止保護虛擬機器，但不刪除備份資料。 http://go.microsoft.com/fwlink/?LinkId=808124 有更多詳細資料 |刪除主要 VM 後，但備份原則繼續尋找 VM 來備份時，就會發生這種情況。 若要修正此錯誤： <ol><li> 重新建立具有相同名稱和相同資源群組名稱 [雲端服務名稱] 的虛擬機器，<br>(或)</li><li> 停止保護虛擬機器 (不論是否刪除備份資料)。 [更多詳細資料](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| 因為虛擬機器沒有網路連線，所以快照集作業失敗 - 請確定 VM 可存取網路。 若要讓快照集成功，請將 Azure 資料中心 IP 範圍設為白名單，或設定網路存取的 Proxy 伺服器。 如需詳細資訊，請參閱 http://go.microsoft.com/fwlink/?LinkId=800034。 如果您已經使用 Proxy 伺服器，請確定已正確設定 Proxy 伺服器設定 | 當您拒絕虛擬機器上的輸出網際網路連線時，就會擲回這個錯誤。 VM 快照集延伸模組需要網際網路連線，才能建立虛擬機器基礎磁碟的快照集。 [進一步了解](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine)如何修正封鎖網路存取所造成的快照集失敗。 |
| VM 代理程式無法與 Azure 備份服務通訊。 - 請確認 VM 具有網路連線，且 VM 代理程式是最新版且正在執行。 如需詳細資訊，請參閱 http://go.microsoft.com/fwlink/?LinkId=800034 |如果 VM 代理程式發生問題，或以某種方式封鎖對 Azure 基礎結構的網路存取，則會擲回這個錯誤。 [深入了解](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vm-agent-unable-to-communicate-with-azure-backup)如何進行 VM 快照集問題偵錯。<br> 如果 VM 代理程式並未造成任何問題，請重新啟動 VM。 有時，不正確的 VM 狀態可能會造成問題，重新啟動 VM 就可清除此「錯誤狀態」。 |
| VM 處於失敗佈建狀態 - 請將 VM 重新啟動，並確定 VM 為執行中，或關機狀態可進行備份 | 當其中一個延伸模組失敗而導致 VM 狀態變成失敗的佈建狀態時，就會發生這個情況。 請移至延伸模組清單，查看是否有失敗的延伸模組，將它移除並嘗試重新啟動虛擬機器。 如果所有延伸模組都是處於執行狀態，請檢查 VM 代理程式服務是否正在執行。 若否，請重新啟動 VM 代理程式服務。 | 
| 受管理磁碟的 VMSnapshot 延伸模組作業失敗 - 請重試備份作業。 如果問題重複出現，請遵循 'http://go.microsoft.com/fwlink/?LinkId=800034' 中的指示。 如果進一步失敗，請連絡 Microsoft 支援 | Azure 備份服務無法觸發快照集時，就會發生此錯誤。 [深入了解](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vmsnapshot-extension-operation-failed)如何進行 VM 快照集問題偵錯。 |
| 無法複製虛擬機器的快照集，因為儲存體帳戶中的可用空間不足 - 請確定儲存體帳戶中的可用空間等於連結至虛擬機器的進階儲存體磁碟上所呈現的資料 | 若為進階 VM，我們會將快照集複製到儲存體帳戶。 這是為了確定快照集上運作的備份管理流量不會限制可供使用進階磁碟的應用程式使用的 IOPS 數目。 Microsoft 建議只配置 50% 的總儲存體帳戶空間，讓 Azure 備份服務可以將快照集複製到儲存體帳戶，並從儲存體帳戶中複製的這個位置，將資料傳送到到保存庫。 | 
| 無法執行操作，因為 VM 代理程式沒有回應 |如果 VM 代理程式發生問題，或以某種方式封鎖對 Azure 基礎結構的網路存取，則會擲回這個錯誤。 針對 Windows VM，請檢查服務中的VM 代理程式服務狀態，以及代理程式是否出現在 [控制台] 的 [程式集] 中。 請嘗試從 [控制台] 中刪除程式，然後以[下方](#vm-agent)所述的方式重新安裝代理程式。 重新安裝代理程式之後，請觸發臨機操作備份以確認。 |
| 復原服務擴充作業失敗。 - 請確定虛擬機器上有最新的虛擬機器代理程式，且代理程式服務正在執行中。 請重試備份作業，如果失敗，請連絡 Microsoft 支援服務。 |VM 代理程式過期時會擲回這個錯誤。 請參閱下面的「更新 VM 代理程式」一節以更新 VM 代理程式。 |
| 虛擬機器不存在。 - 請確定該虛擬機器存在，或選取不同的虛擬機器。 |當刪除主要 VM 但備份原則繼續尋找 VM 來執行備份時，就會發生這種情況。 若要修正此錯誤： <ol><li> 重新建立具有相同名稱和相同資源群組名稱 [雲端服務名稱] 的虛擬機器，<br>(或)<br></li><li>停止保護虛擬機器，但不刪除備份資料。 [更多詳細資料](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| 命令執行失敗。 另一項作業目前正在此項目上進行。 請等到前一項作業完成，然後重試 |VM 上的現有備份正在執行中，而當現有作業正在執行時，無法啟動新的作業。 |
| 從備份保存庫複製 VHD 已逾時 - 請在幾分鐘內重試此作業。 如果問題持續發生，請連絡 Microsoft 支援服務。 | 發生此問題的時機為儲存體端上產生暫時性錯誤，或者備份服務尚未從裝載 VM 的儲存體帳戶取得足夠的 IOPS，以便在逾時期限內將資料轉送到保存庫。 確定您在設定備份時已遵循[最佳作法](backup-azure-vms-introduction.md#best-practices)。 請嘗試將 VM 移至其他尚未載入的儲存體帳戶，然後重試備份。|
| 備份因為內部錯誤而失敗 - 請在幾分鐘內重試此作業。 如果問題持續發生，請連絡 Microsoft 支援服務。 |這個錯誤的發生原因有 2 個： <ol><li> 存取 VM 儲存體時發生暫時性問題。 請檢查 [Azure 狀態](https://azure.microsoft.com/en-us/status/)，以查看該區域中目前是否有任何與計算、儲存體或網路相關的問題。 然後在問題解決後，立即重試備份作業。 <li>已刪除原始 VM，因此無法取得復原點。 若要保留已刪除 VM 的備份資料，但移除備份錯誤：請取消保護 VM 並選擇保留資料。 這個動作會停止排定的備份作業和週期性錯誤訊息。 |
| 無法在選取的項目上安裝 Azure 復原服務延伸模組 - VM 代理程式是 Azure 復原服務延伸模組的先決條件。 安裝 Azure VM 代理程式並重新啟動註冊作業 |<ol> <li>檢查是否已正確安裝 VM 代理程式。 <li>確定已正確設定 VM 組態中的旗標。</ol> [深入了解](#validating-vm-agent-installation)如何安裝 VM 代理程式，以及如何驗證 VM 代理程式安裝。 |
| 擴充功能安裝失敗，發生錯誤「COM+ 無法與 Microsoft Distributed Transaction Coordinator 通話」 |這通常表示 COM+ 服務未執行。 請連絡 Microsoft 支援服務來協助解決此問題。 |
| 快照集作業失敗，發生 VSS 作業錯誤「這個磁碟機已由 BitLocker 磁碟機加密鎖定」。 您必須至 [控制台] 解除鎖定這個磁碟機。 |對 VM 上的所有磁碟機關閉 BitLocker，並觀察是否已解決 VSS 問題 |
| VM 目前的狀態不允許備份。 |<ul><li>檢查 VM 是否處於「執行中」與「關閉」之間的暫時性狀態。 如果是，請等待 VM 狀態變成這其中一種狀態，然後再次觸發備份。 <li> 如果 VM 是 Linux VM 並使用 [安全性強化 Linux] 核心模組，您必須從安全性原則中排除 Linux 代理程式路徑 (_/var/lib/waagent_)，以確定會安裝備份擴充功能。  |
| 找不到 Azure 虛擬機器。 |當刪除主要 VM 但備份原則繼續尋找 VM 來執行備份時，就會發生這種情況。 若要修正此錯誤： <ol><li>重新建立具有相同名稱和相同資源群組名稱 [雲端服務名稱] 的虛擬機器， <br>(或) <li> 停用此 VM 的保護，因此不會建立備份作業。 </ol> |
| 虛擬機器代理程式不存在於虛擬機器上：請安裝任何必要元件和 VM 代理程式，然後重新啟動作業。 |[深入了解](#vm-agent) VM 代理程式安裝，以及如何驗證 VM 代理程式安裝。 |
| 快照集作業失敗，因為 VSS 寫入器處於不正常狀態 |您需要重新啟動 VSS (磁碟區陰影複製服務) 寫入器處於不正常的狀態。 若要達到這個目的，從提高權限的命令提示字元，執行 _vssadmin list writers_。 輸出會包含所有 VSS 寫入器和其狀態。 對於每個狀態不是「[1] 穩定」的 VSS 寫入器，請從提升權限的命令提示字元執行下列命令，重新啟動 VSS 寫入器：<br> _net stop serviceName_ <br> _net start serviceName_|
| 快照集作業失敗，因為組態的剖析失敗 |這是因為 MachineKeys 目錄中的權限已變更︰_%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>請執行下列命令，並確認 MachineKeys 目錄的權限是預設的︰<br>_icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> 預設權限是︰<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>如果您發現 MachineKeys 目錄的權限不同於預設權限，請依照下列步驟來修正權限、刪除憑證，並觸發備份。<ol><li>修正 MachineKeys 目錄上的權限。<br>在目錄上使用 Explorer 安全性屬性和進階安全性設定，將權限重設回預設值，從目錄移除任何額外 (預設值以外) 的使用者物件，然後確認「Everyone」權限有特殊存取權︰<br>-列出資料夾/讀取資料 <br>-讀取屬性 <br>-讀取擴充屬性 <br>-建立檔案/寫入資料 <br>-建立資料夾/附加資料<br>-寫入屬性<br>-寫入擴充屬性<br>-讀取權限<br><br><li>刪除 [發給] 欄位 = "Windows Azure Service Management for Extensions" 或 "Windows Azure CRP Certificate Generator" 的所有憑證。<ul><li>[開啟 [憑證 (本機電腦)] 主控台](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>刪除 [發給] 欄位 = "Windows Azure Service Management for Extensions" 或 "Windows Azure CRP Certificate Generator" 的所有憑證 (在 [個人] -> [憑證] 下)。</ul><li>觸發 VM 備份。 </ol>|
| 驗證失敗，因為虛擬機器單獨以 BEK 進行加密。 只會對同時使用 BEK 和 KEK 加密的虛擬機器啟用備份。 |虛擬機器應該使用「BitLocker 加密金鑰」和「金鑰加密金鑰」進行加密。 之後，應該啟用備份。 |
| Azure 備份服務沒有足夠的 Key Vault權限可備份加密的虛擬機器。 |應使用 [PowerShell 文件](backup-azure-vms-automation.md)的**啟用備份**區段中所述的步驟，在 PowerShell 中向備份服務提供這些權限。 |
|快照集擴充安裝失敗，錯誤為 - COM+ 無法與 Microsoft Distributed Transaction Coordinator 通話 | 請嘗試重新啟動 Windows 服務 "COM+ System Application" (從提高權限的命令提示字元 - _net start COMSysApp_)。 <br>如果在啟動時失敗，請遵循下列步驟︰<ol><li> 確認服務 "Distributed Transaction Coordinator" 的登入帳戶是 "Network Service"。 如果不是，請變更為 "Network Service"，重新啟動此服務，然後嘗試啟動服務 "COM+ System Application"。'<li>如果仍然無法啟動，請依照下列步驟解除安裝/安裝服務 "Distributed Transaction Coordinator"：<br> - 停止 MSDTC 服務<br> - 開啟命令提示字元 (cmd) <br> - 執行命令 “msdtc -uninstall” <br> - 執行命令 “msdtc -install” <br> - 啟動 MSDTC 服務<li>啟動 Windows 服務 "COM+ System Application"，啟動之後，從入口網站觸發備份。</ol> |
|  因 COM + 錯誤而導致快照集作業失敗 | 建議的動作是重新啟動 Windows 服務「COM + 系統應用程式」(從提升權限的命令提示字元 - _net start COMSysApp_)。 如果問題持續發生，請重新啟動 VM。 如果重新啟動 VM 沒有幫助，請嘗試[移除 VMSnapshot 延伸模組](https://docs.microsoft.com/en-us/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load)，並手動觸發備份。 |
| 無法凍結 VM 的一或多個掛接點以取得檔案系統一致快照集 | 請使用下列步驟： <ol><li>使用 _'tune2fs'_ 命令檢查所有已裝載裝置的檔案系統狀態。<br> 例如：tune2fs -l /dev/sdb1 \| grep "Filesystem state" <li>使用 _'umount'_ 命令卸載檔案系統狀態不是空白的裝置 <li> 使用 _'fsck'_ 命令對這些裝置執行 FileSystemConsistency 檢查 <li> 重新裝載裝置並嘗試備份。</ol> |
| 因為建立安全網路通訊通道時發生失敗，所以快照集作業失敗 | <ol><Li> 在提高權限的模式中執行 regedit.exe，開啟登錄編輯程式。 <li> 識別系統中存在的所有 .NetFramework 版本。 它們位於登錄機碼 "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft" 的階層下 <li> 針對登錄機碼中的每個 .NetFramework，新增下列機碼︰ <br> "SchUseStrongCrypto"=dword:00000001 </ol>|
| 因為安裝適用於 Visual Studio 2012 的 Visual C++ 可轉散發套件時發生失敗，所以快照集作業失敗 | 瀏覽至 C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion，並安裝 vcredist2012_x64。 請確定允許這項服務安裝的登錄機碼值設為正確的值，也就是登錄機碼 _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ 的值為 3，而不是 4。 如果您仍遇到安裝問題，請從提高權限的命令提示字元執行 _MSIEXEC /UNREGISTER_，再執行 _MSIEXEC /REGISTER_，以重新啟動安裝服務。  |


## <a name="jobs"></a>作業
| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 不支援取消這種工作類型：請等待工作完成。 |None |
| 作業不是處於可取消的狀態：請等待作業完成。 <br>或<br> 所選取的作業不是處於可取消的狀態 - 請等待作業完成。 |對於所有可能性而言，作業幾乎已完成。 請等待作業完成。|
| 無法取消工作，因為它不在進行中：只支援取消進行中的工作。 請嘗試取消進行中的工作。 |這起因於暫時性狀態。 請稍候再重試取消作業。 |
| 無法取消工作：請等待工作完成。 |None |

## <a name="restore"></a>還原
| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 還原失敗並發生雲端內部錯誤 |<ol><li>您嘗試還原的雲端服務是使用 DNS 設定所設定。 您可以檢查 <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>如果設定了 Address，這表示已設定 DNS 設定。<br> <li>您嘗試還原到其中的雲端服務是使用 ReservedIP 所設定，而雲端服務中現有的 VM 目前處於停止狀態。<br>您可以使用下列 powershell Cmdlet，來檢查雲端服務是否具有保留的 IP：<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>您嘗試將具有下列特殊網路組態的虛擬機器還原至相同的雲端服務。 <br>- 負載平衡器組態下的虛擬機器 (內部與外部)<br>- 具有多個保留 IP 的虛擬機器<br>- 具有多個 NIC 的虛擬機器<br>請在 UI 中選取新的雲端服務，或請參閱適用於具有特殊網路組態之 VM 的[還原考量](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)。</ol> |
| 所選取的 DNS 名稱已被使用：請指定不同的 DNS 名稱，然後再試一次。 |此處的 DNS 名稱是指雲端服務名稱 (結尾通常是 .cloudapp.net)。 這必須是唯一的。 如果您遇到這個錯誤，您需要在還原期間選擇不同的 VM 名稱。 <br><br> 只有 Azure 入口網站的使用者才會看到這個錯誤。 透過 PowerShell 執行還原作業將會成功，因為它只會還原磁碟，並不會建立 VM。 當您在磁碟還原作業之後明確建立 VM 時，將會遇到此錯誤。 |
| 指定的虛擬網路組態不正確：請指定不同的虛擬網路組態，然後再試一次。 |None |
| 指定的雲端服務使用保留的 IP，但不符合所要還原的虛擬機器組態：請指定另一個不使用保留 IP 的雲端服務，或選擇從另一個復原點還原。 |None |
| 雲端服務已達到輸入端點數目限制：請指定不同的雲端服務或使用現有的端點來重試作業。 |None |
| 備份保存庫和目標儲存體帳戶位於兩個不同區域：請確定還原作業中指定的儲存體帳戶，位於與備份保存庫相同的 Azure 區域。 |None |
| 不支援還原作業所指定的儲存體帳戶：只支援具有本地備援或異地備援複寫設定的基本/標準儲存體帳戶。 請選取支援的儲存體帳戶 |None |
| 還原作業所指定的儲存體帳戶類型不在線上：請確定還原作業中指定的儲存體帳戶在線上 |這可能是因為 Azure 儲存體的暫時性錯誤或運作中斷。 請選擇另一個儲存體帳戶。 |
| 已達到資源群組配額：請從 Azure 入口網站刪除一些資源群組，或連絡 Azure 支援以提高限制。 |None |
| 選取的子網路不存在：請選取已存在的子網路 |None |
| 備份服務無權存取您訂用帳戶中的資源。 |若要解決此問題，請先使用[選擇 VM 還原組態](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration)的＜還原備份的磁碟＞一節中所述的步驟來還原磁碟。 之後，使用[從還原的磁碟建立 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 中所述的 PowerShell 步驟，從還原的磁碟建立完整的 VM。 |

## <a name="backup-or-restore-taking-time"></a>備份或還原很花時間
如果您發現備份 (超過 12小時) 或還原 (超過 6小時) 很花時間：
* 了解[增加備份時間的因素](backup-azure-vms-introduction.md#total-vm-backup-time)和[增加還原時間的因素](backup-azure-vms-introduction.md#total-restore-time)。
* 請務必遵循[備份最佳做法](backup-azure-vms-introduction.md#best-practices)。 

## <a name="vm-agent"></a>VM 代理程式
### <a name="setting-up-the-vm-agent"></a>設定 VM 代理程式
一般而言，VM 代理程式已存在於從 Azure 資源庫建立的 VM 中。 不過，從內部部署資料中心移轉的虛擬機器不會安裝 VM 代理程式。 對於這類 VM，必須明確安裝 VM 代理程式。

若為 Windows VM：

* 下載並安裝 [代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要有系統管理員權限，才能完成安裝。
* 若為傳統虛擬機器，請[更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) \(英文\) 以指出代理程式已安裝。 資源管理員虛擬機器不需要此步驟。

如為 Linux VM：

* 從發佈存放庫安裝最新版。 我們「強烈建議」只透過發佈存放庫安裝代理程式。 如需封裝名稱的詳細資料，請參閱 [Linux 代理程式存放庫](https://github.com/Azure/WALinuxAgent) 
* 若為傳統 VM，請[更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) \(英文\) 以指出代理程式已安裝。 資源管理員虛擬機器不需要此步驟。

### <a name="updating-the-vm-agent"></a>更新 VM 代理程式
若為 Windows VM：

* 更新 VM 代理程式與重新安裝 [VM 代理程式二進位檔](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)一樣簡單。 不過，您需要確定在更新 VM 代理程式時，沒有任何執行中的備份作業。

如為 Linux VM：

* 遵循[更新 Linux VM 代理程式](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)上的指示進行。
我們**強烈建議**只透過發佈存放庫更新代理程式。 我們不建議直接從 github 下載代理程式程式碼，並加以更新。 如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以取得如何安裝最新代理程式的指示。 您可以在 github 存放庫中檢查最新的 [Windows Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent/releases)資訊。

### <a name="validating-vm-agent-installation"></a>驗證 VM 代理程式安裝
如何檢查 Windows VM 上的 VM 代理程式版本：

1. 登入 Azure 虛擬機器，然後瀏覽至 *C:\WindowsAzure\Packages* 資料夾。 您應該會發現 WaAppAgent.exe 檔案已存在。
2. 在該檔案上按一下滑鼠右鍵，前往 [屬性]，然後選取 [詳細資料] 索引標籤。[產品版本] 欄位應為 2.6.1198.718 或更高版本

## <a name="troubleshoot-vm-snapshot-issues"></a>疑難排解 VM 快照問題
VM 備份仰賴發給底層儲存體的快照命令。 無法存取儲存體，或快照工作執行延遲可能會造成備份作業失敗。 下列可能導致快照工作失敗。

1. 使用 NSG 封鎖儲存體網路存取<br>
    深入了解如何使用 IP 允許清單或透過 Proxy 伺服器對儲存體[啟用網路存取](backup-azure-vms-prepare.md#network-connectivity)。
2. 已設定 SQL Server 備份的 VM 會造成快照工作延遲  <br>
   根據預設，VM 備份會核發 Windows VM 上的 VSS 完整備份。 在執行 SQL Server 且已設定 SQL Server 備份的 VM 上，這可能造成快照執行延遲。 如果您因為快照的問題而遇到備份失敗，請設定下列登錄機碼。

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. VM 狀態報告不正確，因為 RDP 中的 VM 關機。  <br>
   如果您已將 RDP 中的虛擬機器關機，請回到入口網站中檢查 VM 的狀態是否已正確反映。 如果沒有，請在入口網站中使用 VM 儀表板中的 [關機] 選項將 VM 關機。
4. 如果有四部以上的 VM 共用相同雲端服務，請設定多個備份原則以錯開備份時間，避免超過四部 VM 同時啟動備份。 請試著讓各個原則之間的備份啟動時間相隔一小時。
5. VM 正在以高 CPU/記憶體執行。<br>
   若虛擬機器正在以高 CPU 使用量 (>90%) 或記憶體執行，快照工作會排入佇列、延遲且最終會逾時。在此情況下，請嘗試隨選備份。

<br>

## <a name="networking"></a>網路
如同所有的延伸模組，備份延伸模組需要存取公用網際網路才能運作。 沒有公用網際網路的存取權，可能會以各種不同方式的資訊清單形式顯現：

* 延伸模組安裝可能會失敗
* 備份作業 (如磁碟快照) 可能會失敗
* 顯示備份作業狀態可能會失敗

解析網際網路的公用位址的需求已在 [這裡](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)說明。 您必須檢查 VNET 的 DNS 設定，並確定可以解析 Azure URI。

正確完成名稱解析後，也必須提供 Azure IP 的存取權。 若要解除封鎖對 Azure 基礎結構的存取，請遵循下列步驟進行：

1. 將 Azure 資料中心的 IP 範圍列入允許清單。
   * 取得要列入允許清單的 [Azure 資料中心 IP](https://www.microsoft.com/download/details.aspx?id=41653) 。
   * 使用 [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) Cmdlet 解除封鎖該 IP。 在提升權限的 PowerShell 視窗中 (以系統管理員身分執行)，於 Azure VM 內執行這個 Cmdlet。
   * 將規則加入 NSG (若已有規則)，以允許存取該 IP。
2. 建立 HTTP 流量的行經路徑
   * 如果已有一些網路限制 (例如，網路安全性群組)，請部署 HTTP Proxy 伺服器來路由傳送流量。 部署 HTTP Proxy 伺服器的步驟位於[這裡](backup-azure-vms-prepare.md#network-connectivity)。
   * 將規則加入 NSG (若已有規則)，以允許從 HTTP Proxy 存取網際網路。

> [!NOTE]
> 必須在來賓內啟用 DHCP，IaaS VM 備份才能運作。  如果您需要靜態私人 IP 位址，您應該透過平台來進行設定。 VM 內的 DHCP 選項應保持啟用。
> 請參閱有關 [設定靜態內部私人 IP 位址](../virtual-network/virtual-networks-reserved-private-ip.md)的詳細資訊。
>
>
