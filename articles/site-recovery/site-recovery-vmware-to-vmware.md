---
title: "將 VMware VM 或實體伺服器複寫到其他站台 (傳統 Azure 入口網站) | Microsoft Docs"
description: "使用這篇文章以使用 Azure Site Recovery，將 VMware VM 或 Windows/Linux 實體伺服器複寫至次要網站。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: 01a6f35fe61290f8c7275c34273d66956a53d3f9
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site-in-the-classic-azure-portal"></a>在傳統 Azure 入口網站中，將內部部署 VMware 虛擬機器或實體伺服器複寫到次要站台

## <a name="overview"></a>概觀
Azure Site Recovery 中的 InMage Scout 可提供內部部署 VMware 網站之間的即時複寫。 InMage Scout 包含在 Azure Site Recovery 服務訂用帳戶中。 

## <a name="prerequisites"></a>必要條件
**Azure 帳戶**：您將需要一個 [Microsoft Azure](https://azure.microsoft.com/) 帳戶。 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。

## <a name="step-1-create-a-vault"></a>步驟 1：建立保存庫
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [新增] > [管理] > [備份和 Site Recovery (OMS)]。 或者，您也可以按一下 [瀏覽] > [復原服務保存庫] > [加入]。
3. 在 [名稱]  中，指定保存庫的易記識別名稱。 如果您有多個訂用帳戶，請選取其中一個。
4. 在「資源群組」中，建立新的資源群組，或選取現有的資源群組。 指定 Azure 區域來完成所需的欄位。
5. 在 [位置] 中，選取保存庫的地理區域。 若要查看支援的地區，請參閱 [Azure Site Recovery 定價](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 如果您想要從「儀表板」快速存取保存庫，請按一下 [釘選到儀表板]，然後按一下 [建立]。
7. 新的保存庫會出現在 [儀表板] > [所有資源] 上，以及主要 [復原服務保存庫] 刀鋒視窗上。

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>步驟 2：設定保存庫並下載 InMage Scout 元件
1. 在 [復原服務保存庫] 刀鋒視窗中選取您的保存庫，然後按一下 [設定]。
2. 在 [設定] > [快速入門] 中，按一下 [Site Recovery] > [步驟 1︰準備基礎結構] > [保護目標]。
3. 在 [保護目標] 中選取 [到復原站台]，然後選取 [是，使用 VMware vSphere Hypervisor]。 然後按一下 [確定]。
4. 在 [Scout 設定] 中，按一下 [下載] 以下載 InMage Scout 8.0.1 GA 軟體和註冊金鑰。 所有必要元件的安裝程式檔案都在下載的 .zip 檔中。

## <a name="step-3-install-component-updates"></a>步驟 3：安裝元件更新
深入了解最新的 [更新](#updates)。 您將會依下列順序在伺服器上安裝更新檔案：

1. RX 伺服器 (如果有的話)
2. 設定伺服器
3. 處理序伺服器
4. 主要目標伺服器
5. vContinuum 伺服器
6. 來源伺服器 (Windows 和 Linux 伺服器)

安裝更新，如下所示：

1. 下載 [update](https://aka.ms/asr-scout-update5) .zip 檔案。 此 ++zip 檔案包含下列檔案：

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe
   * 適用於 RHEL5、OL5、OL6、SUSE 10、SUSE 11 的 UA update4 位元：UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. 解壓縮 .zip 檔。<br>
3. **針對 RX 伺服器**：將 **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** 複製到 RX 伺服器並將其解壓縮。 在解壓縮的資料夾中執行 **/Install**。<br>
4. **針對設定伺服器/處理伺服器**：將 **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** 複製到設定伺服器和處理伺服器。 連按兩下加以執行。<br>
5. **針對 Windows 主要目標伺服器**：若要更新整合代理程式，請將 **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** 複製到主要目標伺服器。 連按兩下加以執行。 請注意，如果來源一直未更新到 Update 4，整合代理程式也適用於來源伺服器。 您也應該將其安裝在來源伺服器上，如本清單稍後所述。<br>
6. **針對 vContinuum 伺服器**：將 **vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe** 複製到 vContinuum 伺服器。  確定您已經關閉 vContinuum 精靈。 連按兩下檔案加以執行。<br>
7. **針對 Linux 主要目標伺服器**：若要更新整合代理程式，請將 **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** 複製到主要目標伺服器並將它解壓縮。 在解壓縮的資料夾中執行 **/Install**。<br>
8. **針對 Windows 來源伺服器**︰如果來源已經是 Update 4，則不需要在來源上安裝 Update 5 代理程式。 如果低於 Update 4，請套用 Update 5 代理程式。
若要更新整合代理程式，請將 **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** 複製到來源伺服器。 連按兩下加以執行。 <br>
9. **針對 Linux 來源伺服器**：若要更新整合代理程式，請將對應的 UA 檔案版本複製到 Linux 伺服器並將它解壓縮。 在解壓縮的資料夾中執行 **/Install**。  範例：針對 RHEL 6.7 64 位元伺服器，請將 **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** 複製到伺服器並將它解壓縮。 在解壓縮的資料夾中執行 **/Install**。

## <a name="step-4-set-up-replication"></a>步驟 4：設定複寫
1. 設定來源與目標 VMware 網站之間的複寫。
2. 如需指引，請使用隨產品下載的 InMage Scout 文件。 或者，您可以存取下列文件：

   * [版本資訊](https://aka.ms/asr-scout-release-notes)
   * [相容性矩陣](https://aka.ms/asr-scout-cm)
   * [使用者指南](https://aka.ms/asr-scout-user-guide)
   * [RX 使用者指南](https://aka.ms/asr-scout-rx-user-guide)
   * [快速安裝指南](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>更新
### <a name="azure-site-recovery-scout-801-update-5"></a>Azure Site Recovery Scout 8.0.1 Update 5
Scout Update 5 是累積更新， 包含 Update 1 到 Update 4 的所有修正及下列新的錯誤修正和增強功能。
從 ASR Scout Update 4 更新至 Update 5 所新增的修正程式是針對主要目標和 vContinuum 元件。 如果您的所有來源伺服器，主要目標、設定伺服器、處理序伺服器和 RX 已是 ASR Scout Update 4，您只需要在主要目標伺服器上套用 Update 5。 

**新平台支援**
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)

> [!NOTE]
> SLES 11 SP4 64 位元 **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** 與基底 Scout GA 套件 **InMage_Scout_Standard_8.0.1 GA.zip** 封裝在一起。 請依照[步驟 1](#step-1-create-a-vault) 所述，從入口網站下載 Scout GA 套件。
>

**錯誤修正和增強功能**

* 增加 Windows 叢集支援的可靠性
    * 已修正 - 有時候某些 P2V MSCS 叢集磁碟會在復原後變 RAW
    * 已修正 - P2V MSCS 叢集的復原因為磁碟順序不符而失敗
    * 已修正 - MSCS 叢集新增磁碟作業因磁碟大小不相符而失敗
    * 已修正 - 來源 MSCS 叢集與 RDM LUN 對應整備檢查在大小確認步驟失敗
    * 已修正 - 因為 SCSI 不符的問題，單一節點叢集保護失敗 
    * 已修正 - 如果有目標叢集磁碟，重新保護 P2V Windows 叢集伺服器會失敗。 
    
* 在容錯回復保護期間，如果選取的 MT 不是在和受保護來源機器相同的 ESXi 伺服器上 (在正向保護中)，vContinuum 會在容錯回復復原期間挑出錯誤的 MT，之後的復原作業便會失敗。

> [!NOTE]
> 
> * 上述 P2V 叢集修正僅適用於剛以 ASR Scout Update 5 加以保護的實體 MSCS 叢集。 如果要讓叢集修正也能用在已經以較舊更新保護的 P2V MSCS 叢集，您必須遵循 [ASR Scout 版本資訊](https://aka.ms/asr-scout-release-notes) 第 12 節＜升級受保護的搜索 Update5 P2V MSCS 叢集＞所述的升級步驟。
> 
> * 重新保護時，只有當在每個叢集節點上使用中的幾個磁碟與最初受保護時相同，重新保護實體 MSCS 叢集才可以重複使用現有的目標磁碟。 如果沒有，可使用 [ASR Scout 版本資訊](https://aka.ms/asr-scout-release-notes)第 12 節中所述的手動步驟，將目標端磁碟移至正確的資料存放區路徑，以便在重新保護時重複使用它們。 如果未遵循升級步驟重新保護 P2V 模式中的 MSCS 叢集，則會在目標 ESXi 伺服器上建立新的磁碟。 您必須手動刪除資料存放區中的舊磁碟。
> 
> * 每當來源 SLES11 或 SLES11 與任何 Service Pack 伺服器一起正常重新開機時，皆應手動標記**根**磁碟複寫組以利重新同步處理，因為在 CX UI 中不會標注標。 如果您沒有「標示」重新同步處理的根磁碟，可能會遇到資料完整性 (DI) 問題。
> 

### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 是累積更新。 它包含 update1 到 update 3 的所有修正及下列新的錯誤修正和增強功能。

**新平台支援**

* 新增對 vCenter/vSphere 6.0、6.1 及 6.2 的支援
* 新增對下列 Linux 作業系統的支援
  * Red Hat Enterprise Linux (RHEL)7.0、7.1 及 7.2
  * CentOS 7.0、7.1 及 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 位元 **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** 與基底 Scout GA 套件 **InMage_Scout_Standard_8.0.1 GA.zip** 封裝在一起。 請依照[步驟 1](#step-1-create-a-vault) 所述，從入口網站下載 Scout GA 套件。
>
>

**錯誤修正和增強功能**

* 提升下列 Linux OS 和複製項的關機處理，以防止多餘的重新同步處理問題。
  * Red Hat Enterprise Linux (RHEL) 6.x
  * Oracle Linux (OL) 6.x
* 針對 Linux，現在僅限本機使用者可以擁有整合代理程式安裝目錄中的完整資料夾存取權限。
* 在 Windows 上，在負載繁重的分散式應用程式 (例如 SQL 和 Share Point 叢集) 上發出通用分散式一致性書籤時的逾時問題。
* 在 CX 基底安裝程式中新增記錄相關的修正。
* 在「Windows 主要目標」基底安裝程式中新增 VMware vCLI 6.0 下載連結。
* 針對容錯移轉與 DR 演練期間的網路設定變更，新增更多檢查和記錄。
* 有時不會向 CX 回報保留資訊。  
* 針對實體叢集，當發生來源磁碟區壓縮時，透過 vContinuum 精靈執行的磁碟區調整大小作業會失敗。
* 當叢集磁碟是 PRDM 磁碟時，叢集保護會因發生「找不到磁碟簽章」錯誤而失敗。
* cxps 傳輸伺服器會因發生超出範圍例外狀況而當機。
* 現在在 vContinuum 精靈的推送安裝頁面中可以調整伺服器名稱和 IP 資料行的大小。
* RX API 增強功能
  * 提供 5 個最新可用的共同一致性點 (僅限 [保證] 標記)。
  * 為所有受保護的裝置提供容量和可用空間詳細資料。
  * 提供來源伺服器上的 Scout 驅動程式狀態。

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** 基底套件現在包含已更新的 CX 基底安裝程式 **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**  和「Windows 主要目標」基底安裝程式 **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**。 針對所有新的安裝，請使用新的 CX 和「Windows 主要目標」GA 位元。
> * Update 4 可以直接在 8.0.1 GA 上套用。
> * 一旦在系統上套用設定伺服器和 RX 更新便無法回復。
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3
Update 3 包含下列錯誤修正和增強功能：

* 設定伺服器和 RX 位於 Proxy 後方時無法向 Site Recovery 保存庫註冊。
* 健全狀況報告中未更新不符合復原點目標 (RPO) 的時數。
* 當 ESX 硬體詳細資料或網路詳細資料包含任何UTF-8 字元時，設定伺服器不會與 RX 同步處理。
* Windows Server 2008 R2 網域控制站無法在復原後啟動。
* 離線同步處理未如預期般運作。
* 在虛擬機器 (VM) 容錯移轉後，複寫配對刪除因為時間太長而在 CX UI 中停滯，且使用者無法完成容錯回復或繼續作業。
* 一致性作業完成的整體快照作業已進行最佳化，有助於減少應用程式中斷連接 SQL 用戶端等。
* 降低在 Windows 上建立快照所需的記憶體使用量，改善一致性工具 (VACP.exe) 的效能。
* 密碼超過 16 個字元時，推送安裝服務會當機。
* vContinuum 不會在認證變更時檢查和提示輸入新的 vCenter 認證。
* 在 Linux 上，主要目標快取管理員 (cachemgr) 並未從處理序伺服器下載檔案，導致發生複寫配對節流。
* 當所有節點上的實體容錯移轉叢集 (MSCS) 磁碟順序不同時，某些叢集磁碟區不會設定複寫。
  <br/>請注意，必須要重新保護叢集才能利用此修正程式。  
* 在 RX 從 Scout 7.1 升級至 Scout 8.0.1 之後，SMTP 功能並未如預期般運作。
* 已在記錄檔中加入更多統計資料，以便復原作業追蹤完成此作業所需的時間。
* 已加入來源伺服器上 Linux 作業系統的支援：
  * Red Hat Enterprise Linux (RHEL) 6 Update 7
  * CentOS 6 Update 7
* CX 和 RX UI 現在可以針對進入點陣圖模式的配對顯示通知。
* RX 中已加入下列安全性修正：

| **問題描述** | **實作程序** |
| --- | --- |
| 透過竄改參數略過授權 |對不適用使用者的存取權限制。 |
| 跨網站偽造要求 |實作針對每一頁隨機產生的頁面權杖概念。 <br/>利用這項實作，您將會看到： <li> 相同使用者只有一個單一登入執行個體。</li><li>頁面重新整理無法運作，而會重新導向到儀表板。</li> |
| 惡意檔案上傳 |將檔案限制於特定副檔名。 允許的副檔名︰7z、aiff、asf、avi、bmp、csv、doc、docx、fla、flv、gif、gz、gzip、jpeg、jpg、log、mid、mov、mp3、mp4、mpc、mpeg、mpg、ods、odt、pdf、png、ppt、pptx、pxd、qt、ram、rar、rm、rmi、rmvb、rtf、sdc、sitd、swf、sxc、sxw、tar、tgz、tif、tiff、txt、vsd、wav、wma、wmv、xls、xlsx、xml、zip。 |
| 持續性跨網站指令碼 |加入輸入驗證。 |

> [!NOTE]
> * 所有的 Site Recovery 更新都是累計的。 Update3 有 Update1 和 Update2 的所有修正。 Update 3 可以直接套用於 8.0.1 GA。
> * 一旦在系統上套用設定伺服器和 RX 更新便無法回復。
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)
Update 2 中的修正包括：

* **設定伺服器**：修正當設定伺服器登錄在 Site Recovery 時，會阻止 31 天免費計量功能正常運作的問題。
* **整合代理程式**：修正 Update 1 中導致 8.0 版升級至 8.0.1 版之後，更新未安裝在主要目標伺服器上的問題。

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Update 1 包含下列錯誤修正和新功能：

* 每個伺服器執行個體享有 31 天的免費保護。 這可讓您測試功能或設定概念證明。
  * 從使用 Site Recovery Scout 第一次保護伺服器的時間開始計算的前 31 天，伺服器上的所有作業 (包括容錯移轉和容錯回復) 都是免費的。
  * 從第 32 天起，每個受保護的伺服器都會依照標準執行個體費率，向客戶擁有的網站收取 Azure Site Recovery 保護費用。
  * 在 Azure Site Recovery 保存庫的 [儀表板] 頁面上，隨時可取得目前計費的受保護伺服器數目。
* 加入對 vSphere 命令列介面 (vCLI) 5.5 Update 2 的支援。
* 已加入來源伺服器上 Linux 作業系統的支援：
  * RHEL 6 Update 6
  * RHEL 5 Update 11
  * CentOS 6 Update 6
  * CentOS 5 Update 11
* 用以處理下列問題的錯誤修正：
  * 設定伺服器或 RX 伺服器的保存庫註冊失敗。
  * 當叢集虛擬機器在恢復期間重新受到保護，叢集磁碟區就不會如預期般出現。
  * 當主要目標伺服器裝載於與內部部署實際執行虛擬機器不同的 ESXi 伺服器時，容錯回復會失敗。
  * 升級至 8.0.1 時，組態檔案權限會變更，進而影響保護和作業。
  * 不會如預期般強制執行重新同步臨界值，因而導致不一致的複寫行為。
  * RPO 設定並未正確地顯示在設定伺服器介面中。 未壓縮的資料值未正確地顯示壓縮的值。
  * 未如預期在 vContinuum 精靈中刪除移除作業，而且未從設定伺服器介面中刪除複寫作業。
  * 在 vContinuum 精靈中，在 MSCS 虛擬機器的保護期間，按一下磁碟檢視中的 [詳細資料]  ，磁碟會自動取消選取。
  * 在實體到虛擬 (P2V) 案例期間，必要的 HP 服務 (例如 CIMnotify、CqMgHost) 不會移至虛擬機器復原中的 [手動]。 這會導致額外的開機時間。
  * 主要目標伺服器上有超過 26 個磁碟時，Linux 虛擬機器的保護就會失敗。

## <a name="next-steps"></a>後續步驟
若有任何問題，請造訪 [Azure Recovery Services 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)(英文)。
