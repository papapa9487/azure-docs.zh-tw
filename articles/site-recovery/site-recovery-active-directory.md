---
title: "以 Azure Site Recovery 保護 Active Directory 和 DNS | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 實作 Active directory 的災害復原解決方案。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 7/20/2017
ms.author: pratshar
ms.openlocfilehash: 197441fc24c178695d4eada6db59f503b21672ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>以 Azure Site Recovery 保護 Active Directory 和 DNS
企業應用程式 (例如 SharePoint、Dynamics AX 和 SAP) 須倚賴 Active Directory 和 DNS 基礎結構才能正確運作。 當您建立應用程式的災害復原解決方案時，請務必記住，您必須先保護及復原 Active Directory 和 DNS，再保護及復原其他應用程式元件，以確保災害發生時每樣東西能正常運作。

Site Recovery 是一項 Azure 服務，可藉由協調虛擬機器的複寫、容錯移轉及復原，提供災害復原功能。 Site Recovery 支援一些複寫案例，可持續地保護虛擬機器和應用程式，並將其順暢地容錯移轉至私密/公用或主機服務提供者的雲端。

使用 Site Recovery，您可以針對 Active Directory 建立一個完整的自動化災害復原計畫。 當發生中斷情況時，您可以在幾秒鐘內從任何地方起始容錯移轉，並在數分鐘內啟動並執行 Active Directory。 如果您已在主要網站針對多個應用程式 (例如 SharePoint 和 SAP) 部署 Active Directory，且您想要執行整個網站的容錯移轉，您可以先使用 Site Recovery 容錯移轉 Active Directory，再使用應用程式專用的復原計劃容錯移轉其他應用程式。

本文說明如何建立 Active Directory 的災害復原解決方案、如何使用單鍵復原計劃執行計劃/未計劃/測試容錯移轉，以及執行支援的組態和必要條件。  開始之前，您應該先熟悉 Active Directory 與 Azure Site Recovery。

## <a name="replicating-domain-controller"></a>複寫網域控制站

您必須在至少一個裝載網域控制站和 DNS 的虛擬機器上設定 [Site Recovery 複寫](#enable-protection-using-site-recovery)。 如果您的環境中有[多個網域控制站](#environment-with-multiple-domain-controllers)，除了使用 Site Recovery 複寫網域控制站虛擬機器之外，您也必須在目標網站 (Azure 或次要的內部部署資料中心) 上設定[其他網域控制站](#protect-active-directory-with-active-directory-replication)。 

### <a name="single-domain-controller-environment"></a>單一網域控制站環境
如果您有幾個應用程式且只有單一網域控制站，而且您想要容錯移轉整個網站，我們建議您使用 Site Recovery 將網域控制站複寫至次要網站 (無論您要容錯移轉至 Azure 或次要網站)。 相同的複寫網域控制站/DNS 虛擬機器也可用於[測試容錯移轉](#test-failover-considerations)。

### <a name="environment-with-multiple-domain-controllers"></a>含有多個網域控制站的環境
如果您有許多應用程式且環境中有多個網域控制站，或您打算一次容錯移轉幾個應用程式，建議您除了使用 Site Recovery 來複寫網域控制站虛擬機器之外，也在目標站台 (Azure 或次要的內部部署資料中心) 上設定[其他網域控制站](#protect-active-directory-with-active-directory-replication)。 針對[測試容錯移轉](#test-failover-considerations)，您可以使用 Site Recovery 所複寫的網域控制站；針對容錯移轉，則可以使用目標網站上的其他網域控制站。 


下列各節說明如何在 Site Recovery 中的網域控制站上啟用保護，以及如何在 Azure 中設定網域控制站。

## <a name="prerequisites"></a>必要條件
* 內部部署 Active Directory 和 DNS 伺服器。
* 在 Microsoft Azure 訂用帳戶中有 Azure Site Recovery 服務保存庫。
* 如果您複寫至 Azure，請在 VM 上執行 Azure 虛擬機器整備評估工具，以確保它們與 Azure VM 和 Azure Site Recovery 服務相容。

## <a name="enable-protection-using-site-recovery"></a>使用 Site Recovery 啟用保護
### <a name="protect-the-virtual-machine"></a>保護虛擬機器
在 Site Recovery 中啟用網域控制站/DNS 虛擬機器的保護。 根據虛擬機器類型 (Hyper-V 或 VMware) 設定 Site Recovery。 使用 Site Recovery 所複寫的網域控制站會用於[測試容錯移轉](#test-failover-considerations)。 請確定它符合下列需求︰

1. 網域控制站是通用類別目錄伺服器
2. 網域控制站應該是測試容錯移轉期間所需角色的 FSMO 角色擁有者 (否則在容錯移轉之後，將必須[收回](http://aka.ms/ad_seize_fsmo)這些角色)

### <a name="configure-virtual-machine-network-settings"></a>設定虛擬機器的網路設定
對於網域控制站/DNS 虛擬機器，在 Site Recovery 中設定網路設定，讓虛擬機器在容錯移轉之後連結至正確的網路。 

![VM 網路設定](./media/site-recovery-active-directory/DNS-Target-IP.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>以 Active Directory 複寫保護 Active Directory
### <a name="site-to-site-protection"></a>站對站保護
請在次要網站上建立網域控制站。 當您將伺服器提升至網域控制站角色時，請指定主要網站中所使用之相同網域的名稱。 您可以使用 **Active Directory 網站和服務** 嵌入式管理單元來設定網站要新增至的網站連結物件上的設定。 藉由設定網站連結，您可以控制兩個以上的網站之間的複寫發生的時間和頻率。 如需詳細資訊，請參閱[排程網站之間的複寫](https://technet.microsoft.com/library/cc731862.aspx)。

### <a name="site-to-azure-protection"></a>站對 Azure 保護
遵循指示以 [在 Azure 虛擬網路中建立網域控制站](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)。 當您將伺服器提升至網域控制站角色時，請指定主要網站中所使用的相同網域名稱。

然後 [重新設定虛擬網路的 DNS 伺服器](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)以在 Azure 中使用此 DNS 伺服器。

![Azure 網路](./media/site-recovery-active-directory/azure-network.png)

**Azure 實際執行網路中的 DNS**

## <a name="test-failover-considerations"></a>測試容錯移轉考量
測試容錯移轉是在與生產網路隔離的網路中發生，因此不會影響生產工作負載。

大部分的應用程式也需要網域控制站和 DNS 伺服器才能運作。 因此，應用程式容錯移轉之前，必須在隔離的網路中建立網域控制站以用於測試容錯移轉。 若要這樣做，最簡單的方法是使用 Site Recovery 複寫網域控制站/DNS 虛擬機器。 然後在執行應用程式復原方案的測試容錯移轉前，先執行網域控制站虛擬機器的測試容錯移轉。 以下是做法：

1. 使用 Site Recovery [複寫](site-recovery-replicate-vmware-to-azure.md)網域控制站/DNS 虛擬機器。
1. 建立隔離的網路。 在 Azure 中建立的任何虛擬網路預設是與其他網路隔離。 建議此網路的 IP 範圍使用與您的生產網路相同的 IP 範圍。 請勿在此網路上啟用網站對網站連線能力。
1. 提供建立之網站的 DNS IP 位址，做為您預期 DNS 虛擬機器取得的 IP 位址。 如果您是複寫到 Azure，請在 [計算與網路] 設定的 [目標 IP] 設定中提供用於容錯移轉的 VM IP 位址。 

    ![目標 IP](./media/site-recovery-active-directory/DNS-Target-IP.png) **目標 IP**

    ![Azure 測試網路](./media/site-recovery-active-directory/azure-test-network.png)

    **Azure 測試網路中的 DNS**

> [!TIP]
> Site Recovery 會嘗試在名稱相同的子網路中建立測試虛擬機器，並使用虛擬機器的 [計算與網路] 設定中提供的 IP。 如果針對測試容錯移轉提供的 Azure 虛擬網路中沒有名稱相同的子網路，則會在依字母順序的第一個子網路中建立測試虛擬機器。 如果目標 IP 是所選子網路的一部分，則 Site Recovery 會使用目標 IP 嘗試建立測試容錯移轉虛擬機器。 如果目標 IP 不屬於所選子網路的一部分，則會使用所選子網路中的任何可用 IP 建立測試容錯移轉虛擬機器。 
>
>


1. 如果您是複寫到其他內部部署網站，且您是使用 DHCP，請遵循指示來[針對測試容錯移轉設定 DNS 和 DHCP](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
1. 對隔離網路中執行的網域控制站虛擬機器進行測試容錯移轉。 請使用網域控制站虛擬機器的最新可用**應用程式一致**復原點來執行測試容錯移轉。 
1. 針對包含應用程式虛擬機器的復原方案執行測試容錯移轉。 
1. 測試完成之後，請在網域控制站虛擬機器上**清除測試容錯移轉**。 此步驟會刪除針對測試容錯移轉所建立的網域控制站。


### <a name="removing-reference-to-other-domain-controllers"></a>移除對其他網域控制器的參考
在執行測試容錯移轉時，不會納入測試網路中的所有網域控制器。 若要將存在生產環境中其他網域控制器的參考移除，您可能必須[收回 FSMO Active Directory 角色](http://aka.ms/ad_seize_fsmo)，並針對遺漏的網域控制器執行[中繼資料清理](https://technet.microsoft.com/library/cc816907.aspx)。 



> [!IMPORTANT]
> 下一節中所述的部分組態不是標準/預設的網域控制站組態。 如果您不想對生產網域控制站進行這些變更，則可以建立專用於 Site Recovery 測試容錯移轉的網域控制站，並對其進行這些變更。  
>
>

### <a name="issues-because-of-virtualization-safeguards"></a>由於虛擬化保護措施所產生的問題 

從 Windows Server 2012 開始，[Active Directory Domain Services 已內建額外保護措施](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)。 只要基礎 hypervisor 平台支援 VM-GenerationID，這些保護措施就能協助保護虛擬化網域控制站避免 USN 復原。 Azure 支援 VM-GenerationID，這表示在 Azure 虛擬機器上執行 Windows Server 2012 或更新版本的的網域控制站具有額外的保護措施。 


當 VM-GenerationID 重設時，也會重設 AD DS 資料庫的 invocationID、捨棄 RID 集區，且 SYSVOL 會標示為非授權。 如需詳細資訊，請參閱 [Active Directory Domain Services 虛擬化的簡介](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)和[安全的虛擬化 DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)

容錯移轉至 Azure 可能會導致 VM-GenerationID 重設，而在網域控制站虛擬機器於 Azure 中啟動時執行額外的保護措施。 這可能會導致使用者在登入網域控制站虛擬機器時產生**明顯的延遲**。 此網域控制站只會用於測試容錯移轉，因此不需要虛擬化保護措施。 若要確保網域控制站虛擬機器的 VM-GenerationID 不會變更，您可以將內部部署網域控制站中的下列 DWORD 值變更為 4。

        
        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start
 

#### <a name="symptoms-of-virtualization-safeguards"></a>虛擬化保護措施的徵兆
 
如果在測試容錯移轉之後執行了虛擬化保護措施，您可能會看到下列一或多個徵兆︰  

世代識別碼變更

![世代識別碼變更](./media/site-recovery-active-directory/Event2170.png)

叫用識別碼變更

![叫用識別碼變更](./media/site-recovery-active-directory/Event1109.png)

無法使用 Sysvol 與 Netlogon 共用

![Sysvol 共用](./media/site-recovery-active-directory/sysvolshare.png)

![Ntfrs Sysvol](./media/site-recovery-active-directory/Event13565.png)

任何 DFSR 資料庫都會遭到刪除

![DFSR DB 刪除](./media/site-recovery-active-directory/Event2208.png)


> [!IMPORTANT]
> 下一節中所述的部分組態不是標準/預設的網域控制站組態。 如果您不想對生產網域控制站進行這些變更，則可以建立專用於 Site Recovery 測試容錯移轉的網域控制站，並對其進行這些變更。  
>
>


### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>在測試容錯移轉期間，對網域控制站問題進行疑難排解


在命令提示字元執行下列命令，以檢查 SYSVOL 與 NETLOGON 資料夾是否為共用︰

    NET SHARE

在命令提示字元執行下列命令，以確保網域控制站正常運作。

    dcdiag /v > dcdiag.txt

在輸出記錄檔中尋找下列文字，以確認網域控制站運作良好。 

* "passed test Connectivity"
* "passed test Advertising"
* "passed test MachineAccount"

如果滿足上述條件，網域控制站應該運作良好。 如果沒有，請嘗試下列步驟。


* 執行網域控制站的權威復原。
    * 雖然[不建議使用 FRS 複寫](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)，但如果您仍在使用它，請依照[此處](https://support.microsoft.com/kb/290762)提供的步驟執行權威還原。 您可以在[此處](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/)閱讀前一個連結中談到的 Burflag 相關資訊。
    * 如果您正在使用 DFSR 複寫，請依照[此處](https://support.microsoft.com/kb/2218556)提供的步驟執行權威還原。 您也可以使用這個[連結](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)中的 Powershell 函式執行權威復原。 
    
* 將內部部署網域控制站中的下列登錄機碼設為 0，以略過初始同步處理需求。 如果此 DWORD 不存在，您可以在節點 'Parameters' 下建立。 您可以在[此處](https://support.microsoft.com/kb/2001093)閱讀相關資訊

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* 將內部部署網域控制站中的下列登錄機碼設為 1，以停用讓通用類別目錄伺服器可以驗證使用者登入的需求。 如果此 DWORD 不存在，您可以在節點 'Lsa' 下建立。 您可以在[此處](http://support.microsoft.com/kb/241789)閱讀相關資訊

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>不同電腦上的 DNS 和網域控制站
如果 DNS 與網域控制站不在相同的虛擬機器上，您必須為測試容錯移轉建立 DNS VM。 如果它們在相同的 VM 上，您可以略過本節。

您可以使用全新的 DNS 伺服器，並建立所有的必要區域。 例如，如果 Active Directory 網域是 contoso.com，您可以使用 contoso.com 的名稱來建立 DNS 區域。DNS 中對應至 Active Directory 的項目必須更新，如下：

1. 確定復原方案中的任何其他虛擬機器出現之前，這些設定均已完成：
   
   * 區域必須在樹系根名稱之後命名。
   * 區域必須是檔案備份的。
   * 區域必須能夠進行安全和非安全更新。
   * 網域控制站虛擬機器的解析程式應該指向 DNS 虛擬機器的 IP 位址。
2. 在網域控制站虛擬機器中執行下列命令：
   
    `nltest /dsregdns`
3. 在 DNS 伺服器上新增區域、允許不安全的更新，以及將適用於它的項目新增到 DNS：
   
        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>後續步驟
閱讀[我可以保護哪些工作負載?](site-recovery-workload.md)，深入了解如何以 Azure Site Recovery 保護企業工作負載。

