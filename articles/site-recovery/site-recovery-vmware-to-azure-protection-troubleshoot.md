---
title: "針對 VMware/實體到 Azure 的保護錯誤進行疑難排解 | Microsoft Docs"
description: "本文說明常見的 VMware 機器複寫錯誤以及如何疑難排解"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/26/2017
ms.author: asgang
ms.openlocfilehash: 6ebec2e06566b1e2d6834fdd81c0d8b2801b80b9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-on-premises-vmwarephysical-server-replication-issues"></a>針對內部部署 VMware/實體伺服器複寫問題進行疑難排解
使用 Azure Site Recovery 保護您的 VMware 虛擬機器或實體伺服器時，您可能會收到特定的錯誤訊息。 本文將詳述一些較常發生的錯誤訊息，並提供解決它們的疑難排解步驟。


## <a name="initial-replication-is-stuck-at-0"></a>初始複寫卡在 0%。
我們支援部門遇到的大部分初始複寫失敗，都是來自來源伺服器到處理序伺服器或處理序伺服器到 Azure 之間的連線問題。
大部分情況下，您可以依照下面所列的步驟自行疑難排解這些問題。

###<a name="check-the-following-on-source-machine"></a>檢查來源機器的下列事項
* 在來源伺服器機器的命令列中，如下所示使用 Telnet 來偵測搭配 https 連接埠 (預設值 9443) 的處理序伺服器，以查看是否有任何網路連線問題或防火牆連接埠封鎖問題。
     
    `telnet <PS IP address> <port>`
> [!NOTE]
    > 使用 Telnet，不要使用 PING 來測試連線。  如未安裝 Telnet，請依照[這裡](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)的步驟操作。

如果無法連線，允許處理序伺服器上的輸入連接埠 9443，然後檢查問題是否仍然存在。 曾有案例是處理序伺服器位在 DMZ 之後而造成此問題。

* 檢查服務 `InMage Scout VX Agent – Sentinel/OutpostStart` 的狀態，是否沒有在執行，以及檢查問題是否仍然存在。   
 
###<a name="check-the-following-on-process-server"></a>檢查處理序伺服器的下列事項

* **檢查處理序伺服器是否主動將資料推送至 Azure** 

在處理序伺服器機器上，按 Ctrl-Shift-Esc 開啟 [工作管理員]。 移至 [效能] 索引標籤，按一下 [開啟資源監視器] 連結。 在 Resource Manager 中，移至 [網路] 索引標籤。 檢查「網路活動的處理序」中的 cbengine.exe 是否主動傳送大量資料 (以 MB 為單位)。

![啟用複寫](./media/site-recovery-protection-common-errors/cbengine.png)

如果沒有，依照下列步驟操作：

* **檢查處理序伺服器是否能夠連線至 Azure Blob**：選擇並勾選 cbengine.exe 以檢視 TCP 連線，查看是否有處理序伺服器到 Azure 儲存體 blob URL 的連線。

![啟用複寫](./media/site-recovery-protection-common-errors/rmonitor.png)

如果沒有，則移至 [控制台] > [服務]，檢查下列服務是否已啟動並執行：

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     * 
(重新) 啟動任何沒有在執行的服務，然後檢查問題是否仍然存在。

* **檢查處理序伺服器是否能夠使用連接埠 443 連線到 Azure 公用 IP 位址**

從 `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` 開啟最新的 CBEngineCurr.errlog，搜尋 ":443" 和 "connection attempt failed"。

![啟用複寫](./media/site-recovery-protection-common-errors/logdetails1.png)

如果有問題，則從處理序伺服器的命令列，使用 telnet 偵測您 Azure 公用 IP 位址 (上圖中遮蔽的部分，也就是在 CBEngineCurr.currLog 中以連接埠 443 找到的位址)。

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
如果您無法連線，則檢查存取問題是否由於防火牆或 Proxy (如下一個步驟中所述)。


* **檢查處理序伺服器上的 IP 位址型防火牆是否封鎖存取**：如果您的伺服器使用 IP 位址型的防火牆規則，請從[這裡](https://www.microsoft.com/download/details.aspx?id=41653)下載 Microsoft Azure Datacenter 的 IP 範圍完整清單，將它們新增到您的防火牆設定中，以確保它們允許對 Azure (和 HTTPS (443) 連接埠) 的通訊。  允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。

* **檢查處理序伺服器上的 URL 型防火牆是否封鎖存取**：如果您的伺服器使用 URL 型的防火牆規則，請確定下列 URL 已新增至防火牆設定。 
     
  `*.accesscontrol.windows.net:` 用於存取控制和身分識別管理

  `*.backup.windowsazure.com:` 用於複寫資料的傳輸和協調

  `*.blob.core.windows.net:` 用於存取儲存體帳戶來儲存複寫的資料

  `*.hypervrecoverymanager.windowsazure.com:` 用於複寫管理作業和協調

  `time.nist.gov` 和 `time.windows.com`：用於檢查系統時間與通用時間之間的時間同步處理

適用於 **Azure Government 雲端**的 URL：

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us` 

* **檢查處理序伺服器上的 Proxy 設定是否封鎖存取**。  如果您使用 Proxy 伺服器，請確定 DNS 伺服器可解析 Proxy 伺服器名稱。
若要查看您在設定伺服器安裝時所提供的資訊， 請移至登錄機碼

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

現在，請確保 Azure Site Recovery 代理程式使用相同設定來傳送資料。
搜尋 Microsoft Azure 備份 

![啟用複寫](./media/site-recovery-protection-common-errors/mab.png)

開啟它，然後按一下 [動作] > [變更屬性]。 在 [Proxy 設定] 索引標籤上，您會看到 Proxy 位址，這應該和登錄設定顯示的相同。 如果沒有，請將它變更為相同的位址。

![啟用複寫](./media/site-recovery-protection-common-errors/mabproxy.png)

* **檢查處理序伺服器是否限制節流頻寬**：增加頻寬，然後檢查問題是否仍然存在。

##<a name="next-steps"></a>後續步驟
如果您需要更多說明，請在 [ASR 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)上張貼您的問題。 我們有一個使用中的社群，我們的其中一位工程師將協助您。