---
title: "針對 Azure 備份伺服器進行疑難排解 | Microsoft Docs"
description: "針對安裝、註冊 Azure 備份伺服器以及備份和還原應用程式工作負載進行疑難排解"
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: 696f86f616575364bb65021260daf0c8458fc4e9
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-backup-server"></a>針對 Azure 備份伺服器進行疑難排解

您可以針對使用 Azure 備份伺服器搭配下表列出的資訊時所發生的錯誤進行疑難排解。

## <a name="invalid-vault-credentials-provided"></a>提供的保存庫認證無效 

若要解決此問題，請遵循這些[疑難排解步驟](https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues)。

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-server"></a>代理程式作業因為伺服器上的 DPM 代理程式協調員服務發生通訊錯誤而失敗 

若要解決此問題，請遵循這些[疑難排解步驟](https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues)。

## <a name="setup-could-not-update-registry-metadata"></a>安裝程式無法更新登錄中繼資料

若要解決此問題，請遵循這些[疑難排解步驟](https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#installation-issues)。


## <a name="installation-issues"></a>安裝問題

| 作業 | 錯誤詳細資料 | 因應措施 |
|-----------|---------------|------------|
|安裝 | 安裝程式無法更新登錄中繼資料。 此更新失敗會導致儲存體過度耗用。 若要避免此問題，請更新 ReFS Trimming 登錄項目。 | 調整登錄機碼 SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim。 將 Dword 值設為 1。 |
|安裝 | 安裝程式無法更新登錄中繼資料。 此更新失敗會導致儲存體過度耗用。 若要避免此問題，請更新 Volume SnapOptimization 登錄項目。 | 使用空白字串值建立登錄機碼 SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds。 |

## <a name="registration-and-agent-related-issues"></a>註冊與代理程式相關問題
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 註冊至保存庫 | 提供的保存庫認證無效。 檔案可能損毀或沒有復原服務所關聯的最新認證 | 若要修正此錯誤： <ol><li> 從保存庫下載最新的認證檔案，然後再試一次 <br>(或)</li> <li> 如果上述動作沒有用，請嘗試將認證下載至不同的本機目錄，或建立新的保存庫 <br>(或)</li> <li> 如[這個部落格](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/)所述嘗試更新日期和時間設定 <br>(或)</li> <li> 檢查 c:\windows\temp 是否有超過 65000 個檔案。 將過時檔案移至另一個位置，或刪除暫存資料夾中的項目 <br>(或)</li> <li> 檢查憑證的狀態 <br> a. 開啟 [管理電腦憑證] \(在控制台中) <br> b. 展開 [個人] 節點和它的子節點 [憑證] <br> c.  移除 [Windows Azure 工具] 憑證 <br> d. 在 Azure 備份用戶端中重試註冊 <br> (或) </li> <li> 檢查是否已備有任何群組原則 </li></ol> |
| 將代理程式推送至受保護的伺服器 | 代理程式作業因為 \<ServerName> 上的 DPM 代理程式協調員服務發生通訊錯誤而失敗 | **如果產品所示的建議動作沒有用**， <ol><li> 如果您要連結來自不受信任網域的電腦，請遵循[這些](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx)步驟 <br> (或) </li><li> 如果您要連結來自受信任網域的電腦，請使用[這個部落格](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/)中概述的步驟進行疑難排解 <br>(或)</li><li> 嘗試停用防毒來作為疑難排解步驟。 如果這能解決問題，請修改 [這篇文章] (https://technet.microsoft.com/library/hh757911.aspx) 中建議的防毒設定</li></ol> |
| 將代理程式推送至受保護的伺服器 | 為伺服器指定的認證無效 | **如果產品所示的建議動作沒有用**， <br> 嘗試手動將保護代理程式安裝在[這篇文章](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)所指定的生產伺服器上|
| Azure 備份代理程式無法連線到 Azure 備份服務 (ID：100050) | Azure 備份代理程式無法連線到 Azure 備份服務。 | **如果產品所示的建議動作沒有用**， <br>1.從提升權限的提示字元中執行下列命令︰psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"。這會開啟 IE 視窗。 <br/> 2.移至 [工具]-> [網際網路選項]-> [連線]-> [區域網路設定]。 <br/> 3.確認系統帳戶的 Proxy 設定。 設定 Proxy IP 和連接埠。 <br/> 4.關閉 Internet Explorer。|
| Azure 備份代理程式安裝失敗 | Microsoft Azure 復原服務安裝失敗。 Microsoft Azure 復原服務安裝作業對系統所做的所有變更都已復原。 (ID：4024) | 手動安裝 Azure 代理程式


## <a name="configuring-protection-group"></a>設定保護群組
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 設定保護群組 | DPM 無法列舉受保護電腦 (受保護的電腦名稱) 上的應用程式元件 | 在相關的資料來源/元件層級按一下設定保護群組 UI 畫面上的 [重新整理] |
| 設定保護群組 | 無法設定保護 | 如果受保護的伺服器是 SQL Server，請檢查是否已將 sysadmin 角色權限提供給[這篇文章](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)中所述之受保護電腦上的系統帳戶 (NTAuthority\System)
| 設定保護群組 | 此保護群組在儲存體集區中沒有足夠的可用空間 | 新增至儲存體集區的磁碟[不應包含磁碟分割](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx)。 刪除磁碟上的任何現有磁碟區，然後將它新增到儲存體集區|
| 原則變更 |無法修改備份原則。 錯誤：由於發生內部服務錯誤 [0x29834]，導致目前的作業失敗。 請稍後再重試操作。 如果問題持續發生， 請連絡 Microsoft 支援服務。 |**原因：**<br/>當安全性設定已啟用，而您嘗試將保留範圍縮減至低於上面指定的最小值，且您使用不受支援的版本 (低於 MAB 2.0.9052 版和 Azure 備份伺服器更新 1) 時，就會出現此錯誤。 <br/>**建議的動作：**<br/> 在此情況下，您應該將保留期限設定為高於指定的最小保留期限 (若是每日則 7 天、若是每週則 4 週、若是每月則 3 個月，若是每年則 1 年)，以繼續進行與原則有關的更新。 (選擇性) 較好的方法是更新備份代理程式和 Azure 備份伺服器，以利用所有安全性更新。 |

## <a name="backup"></a>備份
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 備份 | 複本不一致 | 請確定已開啟「保護群組」精靈中的自動一致性檢查選項。 在[這裡](https://technet.microsoft.com/library/cc161593.aspx)找到更多關於複本不一致原因和相關建議的詳細資料 <br> <ol><li> 如果是系統狀態/BMR 備份，請檢查受保護伺服器上是否已安裝 Windows Server Backup </li><li> 檢查 DPM/MABS 伺服器上 DPM 儲存體集區的空間相關問題，並視需要配置儲存體 </li><li> 檢查受保護伺服器上的磁碟區陰影複製服務狀態。 如果它是停用狀態，請將它設定為手動啟動，並在伺服器上啟動服務。 然後返回 DPM/MABS 主控台，並開始一致性檢查作業的同步處理。</li></ol>|
| 備份 | 執行作業時發生非預期的錯誤，裝置未就緒 | **如果產品所示的建議動作沒有用**， <br> <ol><li>針對保護群組中的項目，將陰影複製儲存空間設定為無限，並執行一致性檢查 <br></li> (或) <li>嘗試刪除現有保護群組，並建立多個新的保護群組，每個保護群組中各有一個個別項目</li></ol> |
| 備份 | 如果您只要備份系統狀態，請確認受保護的電腦上是否有足夠的可用空間可儲存系統狀態備份 | <ol><li>確認受保護的電腦上已安裝 WSB</li><li>確認受保護的電腦上有足夠空間可存放系統狀態︰最簡單的執行方法是移到受保護的電腦上、開啟 WSB、逐一點選選取項目，然後選取 BMR。 UI 會告訴您這需要多少空間。 開啟 WSB -> 本機備份 -> 備份排程 -> 選取備份設定 -> 完整伺服器 (大小會顯示出來)。 使用此大小進行驗證。</li></ol>
| 備份 | 線上復原點建立失敗 | 如果錯誤訊息指出「Windows Azure Backup Agent 無法建立所選磁碟區的快照集」，請嘗試增加複本和復原點磁碟區中的空間。
| 備份 | 線上復原點建立失敗 | 如果錯誤訊息顯示「Windows Azure 備份代理程式無法連線到 OBEngine 服務」，請確認 OBEngine 存在於電腦上的執行中服務清單。 如果 OBEngine 服務不在執行中，請使用 "net start OBEngine" 命令來啟動 OBEngine 服務。
| 備份 | 線上復原點建立失敗 | 如果錯誤訊息指出「未設定此伺服器的加密複雜密碼。 請設定加密複雜密碼」，請嘗試設定加密複雜密碼。 如果作業失敗， <br> <ol><li>請檢查暫存位置是否存在。 名稱為 “ScratchLocation” 之登錄 HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config 中所提到的位置應該要存在。</li><li> 如果暫存位置存在，請嘗試使用舊的複雜密碼重新註冊。 **每當您設定加密複雜密碼時，請將它儲存在安全的位置**</li><ol>
| 備份 | BMR 的備份失敗 | 如果 BMR 大小很大，請將一些應用程式檔案移到作業系統磁碟機後再重試 |
| 備份 | 重新保護新的 MABS 伺服器上的 VMWare VM，不會顯示為可供新增。 | VMWare 屬性會指向舊的已停用的 MABS 伺服器。 若要解決這個問題：在 VCenter (SC VMM 的對等項目) 中，移至 [摘要] 索引標籤，然後按一下 [自訂屬性]。  從 'DPMServer' 值中刪除舊的 MABS 伺服器名稱。  返回新 MABS 伺服器，並修改 PG。  使用 [重新整理] 按鈕後，會顯示 VM 和可供新增到保護的核取方塊。 |
| 備份 | 存取檔案/共用資料夾時發生錯誤 | 請嘗試依[這裡](https://technet.microsoft.com/library/hh757911.aspx)的建議修改防毒設定|
| 備份 | VMware VM 的線上復原點建立作業失敗。 DPM 在嘗試取得變更追蹤資訊時，VMware 發生錯誤。 ErrorCode - FileFaultFault (ID 33621 ) |  1.針對受影響的 VM，在 VMWare 上重設 ctk <br/> 2.檢查獨立磁碟不在 VMWare 上 <br/> 3.停止保護受影響的 VM，然後使用 [重新整理] 按鈕重新保護 <br/> 4.對受影響的 VM 執行 CC|


## <a name="change-passphrase"></a>變更複雜密碼
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 變更複雜密碼 |輸入的安全性 PIN 碼不正確。 請提供正確的安全性 PIN 碼以完成此作業。 |**原因：**<br/> 當您在執行重要作業 (例如變更複雜密碼) 時輸入無效或已到期的安全性 PIN 碼時，就會出現此錯誤。 <br/>**建議的動作：**<br/> 若要完成作業，您必須輸入有效的安全性 PIN 碼。 若要取得 PIN 碼，請登入 Azure 入口網站，並瀏覽至 [復原服務保存庫] > [設定] > [屬性] > [產生安全性 PIN 碼]。 請使用這個 PIN 碼來變更複雜密碼。 |
| 變更複雜密碼 |作業失敗。 識別碼：120002 |**原因：**<br/>當安全性設定已啟用，而您嘗試變更複雜密碼，且您使用不受支援的版本時，就會出現此錯誤。<br/>**建議的動作：**<br/> 若要變更複雜密碼，您必須先將備份代理程式至少更新為最低版本 2.0.9052，並將 Azure 備份伺服器至少更新為更新 1，然後輸入有效的安全性 PIN 碼。 若要取得 PIN 碼，請登入 Azure 入口網站，並瀏覽至 [復原服務保存庫] > [設定] > [屬性] > [產生安全性 PIN 碼]。 請使用這個 PIN 碼來變更複雜密碼。 |


## <a name="configure-email-notifications"></a>設定電子郵件通知
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 嘗試使用 Office365 帳戶設定電子郵件通知。 | 取得錯誤 ID：2013| **原因：**<br/> 嘗試使用 Office 365 帳戶 <br/> **建議的動作：**<br/> 1.所要確保的第一件事就是已 Exchange 上設定 DPM 伺服器的「允許接收連接器上的匿名轉送」。 此[連結](http://technet.microsoft.com/en-us/library/bb232021.aspx)會說明如何進行設定。  <br/> 2.如果您無法使用內部 SMTP 轉送，而需要使用 Office 365 伺服器進行設定，您可以將 IIS 設定為其轉送。 您必須將 DPM 伺服器設定為[能夠使用 IIS 轉送 SMTP 至 O365](https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx) <br/><br/> 重要事項：在步驟 3->g->ii，務必使用 user@domain.com 格式，而非 domain\user 格式 <br/> 指示 DPM 使用本機伺服器名稱作為 SMTP 伺服器、連接埠 587，以及應送出電子郵件的使用者電子郵件。 <br/> DPM SMTP 設定頁面上的使用者名稱和密碼，應該是網域 DPM 所在的網域帳戶。 <br/><br/> 注意：變更 SMTP 伺服器位址時，對新設定進行變更，關閉 [設定] 方塊，然後重新開啟來確定其反映新的值。  只有變更和測試不一定會採用新的設定，因此以這種方式測試是最佳做法。 <br/><br/> 在此程序期間，您可藉由關閉 DPM 主控台並編輯下列登錄機碼，隨時清除這些設定︰<br/> HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> 刪除 SMTPPassword 和 SMTPUserName 索引鍵。 <br/> 再次啟動時，您可以在 UI 中將它們加回。
