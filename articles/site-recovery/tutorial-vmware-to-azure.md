---
title: "使用 Azure Site Recovery 為內部部署 VMware VM 設定災害復原至 Azure | Microsoft Docs"
description: "了解如何使用 Azure Site Recovery 為內部部署 VMware VM 設定災害復原至 Azure。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: ee445c8af2fc6620385d9c462d4c6551da3d7367
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>為內部部署 VMware VM 設定災害復原至 Azure

本教學課程說明如何為執行 Windows 的內部部署 VMware VM，設定災害復原至 Azure。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 Site Recovery 的復原服務保存庫
> * 設定來源和目標複寫環境
> * 建立複寫原則
> * 啟用 VM 複寫

這是本系列的第三個教學課程。 本教學課程假設您已經完成了先前教學課程中的工作：

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 VMware](tutorial-prepare-on-premises-vmware.md)

開始之前，最好先針對災害復原案例[檢閱架構](concepts-vmware-to-azure-architecture.md)。

## <a name="configure-vmware-account-permissions"></a>設定 VMware 帳戶權限

1. 在 vCenter 層級建立一個角色。 指定角色的名稱 **Azure_Site_Recovery**。
2. 將下列權限指派給 **Azure_Site_Recovery** 角色。

   **Task** | **角色/權限** | **詳細資料**
   --- | --- | ---
   **VM 探索** | 資料中心物件 –> 傳播至子物件、role=Read-only | 至少是唯讀使用者。<br/><br/> 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。
   **完整複寫、容錯移轉、容錯回復** |  資料中心物件 –> 傳播至子物件、role=Azure_Site_Recovery<br/><br/> 資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案<br/><br/> 網路 -> 網路指派<br/><br/> 資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM<br/><br/> 工作 -> 建立工作、更新工作<br/><br/> 虛擬機器 -> 組態<br/><br/> 虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝<br/><br/> 虛擬機器 -> 清查 -> 建立、註冊、取消註冊<br/><br/> 虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/> 虛擬機器 -> 快照 -> 移除快照 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。

3. 在 vCenter 伺服器或 vSphere 主機上建立使用者。 將角色指派給使用者。

## <a name="specify-what-you-want-to-replicate"></a>指定您要複寫的項目。

行動服務必須安裝在您要複寫的每個 VM 上。 當您啟用 VM 的複寫功能時，Site Recovery 會自動安裝此服務。 若要自動安裝，您必須準備一個可供 Site Recovery 用來存取 VM 的帳戶。

您可以使用網域或本機帳戶。 在 Linux VM 上，帳戶應該是來源 Linux 伺服器上的根使用者。 對於 Windows VM，如果您不使用網域帳戶，請停用本機電腦上的遠端使用者存取控制：

  - 在登錄的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，新增 DWORD 項目 **LocalAccountTokenFilterPolicy** 並將其值設為 1。

## <a name="set-up-the-source-environment"></a>設定來源環境

設定來源環境，包括下載 Site Recovery 整合安裝程式、設定組態伺服器和在保存庫中加以註冊，以及探索 VM。

組態伺服器是一部內部部署 VMware VM，可裝載所有的 Site Recovery 元件。 此 VM 會執行組態伺服器、處理序伺服器和主要目標伺服器。

- 組態伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- 處理序伺服器可作為複寫閘道。 接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。 處理序伺服器也會在您要複寫的 VM 上安裝行動服務，並且在內部部署 VMware 伺服器上執行 VM 的自動探索。
- 主要目標伺服器會在從 Azure 容錯回復期間，處理複寫資料。

組態伺服器 VM 應該是高度可用且符合下列需求的 VMware VM：

| **需求** | **詳細資料** |
|-----------------|-------------|
| CPU 核心數目| 8 |
| RAM | 12 GB |
| 磁碟數量 | 3 - 作業系統磁碟、處理序伺服器快取磁碟、保留磁碟機 (適用於容錯回復) |
| - 磁碟可用空間 (處理序伺服器快取) | 600 GB |
| 磁碟可用空間 (保留磁碟) | 600 GB |
| 作業系統版本 | Windows Server 2012 R2 |
| 作業系統地區設定 | 英文 (en-us) |
| VMware vSphere PowerCLI 版本 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Windows Server 角色 | 請勿啟用這些角色：Active Directory Domain Services、Internet Information Services、Hyper-V |
| NIC 類型 | VMXNET3 |
| IP 位址類型 | 靜態 |
| 連接埠 | 443 (控制通道協調流程)<br/>9443 (資料傳輸)|

在組態伺服器 VM 上，確定系統時鐘與時間伺服器同步。
時間必須同步處理到 15 分鐘內。 如果時間差異大於 15 分鐘，安裝程式就會失敗。

確定組態伺服器可以存取下列 URL：

- *.accesscontrol.windows.net。 用於存取控制和身分識別管理。
- *.backup.windowsazure.com。用於複寫資料的傳輸和協調。
- *.blob.core.windows.net。 用於存取儲存體帳戶來儲存複寫的資料。
- *.hypervrecoverymanager.windowsazure.com。用於複寫管理作業和協調。
- time.nist.gov and time.windows.com。用於檢查系統時間與通用時間之間的時間同步處理。

適用於 Azure Government 雲端的 URL：

- *.ugv.hypervrecoverymanager.windowsazure.us
- *.ugv.backup.windowsazure.us
- *.ugi.hypervrecoverymanager.windowsazure.us
- *.ugi.backup.windowsazure.us

任何以 IP 位址為基礎的防火牆規則都應該允許對 [Azure Datacenter IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)，以及連接埠 443 (HTTPS) 和 9443 (資料複寫) 進行通訊。 務必允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。

### <a name="download-the-site-recovery-unified-setup"></a>下載 Site Recovery 整合安裝程式

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後按一下 [所有資源]。
2. 按一下名為 **ContosoVMVault** 的復原服務保存庫。
3. 按一下 [Site Recovery] > [準備基礎結構] > [保護目標]。
4. 針對您電腦的所在之處選取 [內部部署]，針對您要將電腦複寫到何處選取 [到 Azure]，以及選取 [是，使用 VMware vSphere Hypervisor]。 然後按 [下一步] 。
5. 在 [準備來源] 窗格中，按一下 [+組態伺服器]。
6. 在 [新增伺服器] 中，檢查 [設定伺服器] 是否出現在 [伺服器類型] 中。
7. 下載 Site Recovery 統一安裝的安裝檔案。
8. 下載保存庫註冊金鑰。 您會在執行統一安裝時用到此金鑰。 該金鑰在產生後會維持 5 天有效。

   ![設定來源](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>設定組態伺服器

1. 執行統一安裝的安裝檔案。
2. 在 [開始之前] 中，選取 [安裝組態伺服器和處理序伺服器]，然後按 [下一步]。

3. 在 [第三方軟體授權] 中，按一下 [我接受] 來下載並安裝 MySQL，然後按 [下一步]。

4. 在 [註冊] 中，選取您從保存庫下載的註冊金鑰。

5. 在 [網際網路設定] 中，指定在設定伺服器上執行的 Provider 要如何透過網際網路連接到 Azure Site Recovery。

   - 如果您想要使用電腦上目前設定的 Proxy 來連線，請選取 [使用 Proxy 伺服器連線至 Azure Site Recovery]。
   - 如果您想要讓提供者直接連接，請選取 [不使用 Proxy 伺服器直接連線到 Azure Site Recovery]。
   - 如果現有的 Proxy 需要驗證，或是您想要讓 Provider 使用自訂 Proxy 來連線，請選取 [以自訂 Proxy 設定連線]，並指定位址、連接埠和認證。

   ![防火牆](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. 在 [必要條件檢查] 中，安裝程式會執行檢查來確定可以執行安裝。 如果出現有關「通用時間同步處理檢查」的警告，請確認系統時鐘上的時間 ([日期和時間] 設定) 與時區相同。

   ![必要條件](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. 在 [MySQL 組態] 中，建立認證來登入已安裝的 MySQL 伺服器執行個體。

8. 在 [環境詳細資料] 中，選取 [是] 來保護 VMware VM。 安裝程式會檢查是否已安裝 PowerCLI 6.0。

9. 在 [安裝位置] 中，選取您要安裝二進位檔及儲存快取的位置。 您選取的磁碟機至少必須有 5 GB 的可用磁碟空間，但我們建議快取磁碟機至少有 600 GB 的可用空間。

10. 在 [網路選取] 中，指定設定伺服器用來傳送和接收複寫資料的接聽程式 (網路介面卡和 SSL 連接埠)。 連接埠 9443 是用來傳送及接收複寫流量的預設連接埠，但您可以修改此連接埠號碼，以符合您的環境需求。 我們也會開啟連接埠 443，該連接埠用來協調複寫作業。 請勿使用連接埠 443 來傳送或接收複寫流量。

11. 在 [摘要] 中檢閱資訊，然後按一下 [安裝]。 安裝程式會安裝組態伺服器，並且向 Azure Site Recovery 服務註冊它。

    ![摘要](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    安裝完成時，會產生複雜密碼。 在您啟用複寫時會需要它，所以請將它複製並保存在安全的位置。 伺服器會顯示在保存庫的 [設定]  > [伺服器] 窗格上。

### <a name="configure-automatic-discovery"></a>設定自動探索

若要探索 VM，組態伺服器必須連線到內部部署 VMware 伺服器。 基於本教學課程的目的，請使用具有伺服器管理員權限的帳戶，新增 vCenter 伺服器或 vSphere 主機。

1. 在您的組態伺服器上，啟動 **CSPSConfigtool.exe**。 它會是桌面上可用的捷徑，位於「安裝位置」\home\svsystems\bin 資料夾中。

2. 按一下 [管理帳戶]  >  [加入帳戶]。

   ![新增帳戶](./media/tutorial-vmware-to-azure/credentials1.png)

3. 在 [帳戶詳細資料] 中，新增將用來進行自動探索的帳戶。

   ![詳細資料](./media/tutorial-vmware-to-azure/credentials2.png)

若要新增伺服器：

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後按一下 [所有資源]。
2. 按一下名為 **ContosoVMVault** 的復原服務保存庫。
3. 按一下 [Site Recovery] > [準備基礎結構] > [來源]。
4. 選取 [+vCenter] 以連線至 vCenter 伺服器或 vSphere ESXi 主機。
5. 在 [新增 vCenter] 中，為伺服器指定易記的名稱。 然後指定 IP 位址或 FQDN。
6. 讓連接埠保持設為 443，除非您的 VMware 伺服器在不同的連接埠上接聽要求。
7. 選取用來連線至此伺服器的帳戶。 按一下 [確定] 。

Site Recovery 會使用指定的設定連接至 VMware 伺服器並探索 VM。

> [!NOTE]
> 可能需要 15 分鐘以上，帳戶名稱才會出現在入口網站。 若要立即更新，請按一下 [設定伺服器] > 伺服器名稱 > [重新整理伺服器]。

## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。

1. 按一下 [準備基礎結構] > [目標]，然後選取您要使用的 Azure 訂用帳戶。
2. 指定目標部署模型是以 Resource Manager 為基礎或傳統。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

   ![目標](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>建立複寫原則

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後按一下 [所有資源]。
2. 按一下名為 **ContosoVMVault** 的復原服務保存庫。
3. 若要建立複寫原則，請按一下 [Site Recovery 基礎結構] > [複寫原則] > [+複寫原則]。
4. 在 [建立複寫原則] 中，指定原則名稱 **VMwareRepPolicy**。
5. 在 [RPO 閾值] 中，使用預設值 60 分鐘。 這個值可定義復原點的建立頻率。 連續複寫超過此限制時會產生警示。
6. 在 [復原點保留] 中，使用預設值 24 小時作為每個復原點的保留期長度。 在本教學課程中，我們使用 72 小時。 複寫的 VM 可以還原至一個週期內的任何時間點。
7. 在 [應用程式一致快照頻率] 中，使用 60 分鐘預設值作為應用程式一致快照集的建立頻率。 按一下 [確定]  以建立原則。

   ![原則](./media/tutorial-vmware-to-azure/replication-policy.png)

此原則會自動與設定伺服器產生關聯。 依預設會自動建立容錯回復的比對原則。 例如，如果複寫原則是 **rep-policy**，容錯回復原則便會是 **rep-policy-failback**。 從 Azure 起始容錯回復時才會使用此原則。

## <a name="enable-replication"></a>啟用複寫

Site Recovery 會在 VM 已啟用複寫時安裝行動服務。 可能需要 15 分鐘或更久的時間，變更才會生效並顯示在入口網站中。

啟用複寫，如下所示︰

1. 按一下 [複寫應用程式] > [來源]。
2. 在 [來源] 中，選取設定伺服器。
3. 在 [機器類型] 中，選取 [虛擬機器]
4. 在 [vCenter/vSphere Hypervisor] 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。
5. 選取處理序伺服器 (組態伺服器)。 然後按一下 [確定]。
6. 在 [目標] 中，選取您想要在其中建立容錯移轉 VM 的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或資源管理) 中，針對容錯移轉 VM 使用的部署模型。
7. 選取您要用來複寫資料的 Azure 儲存體帳戶。
8. 選取 Azure VM 在容錯移轉後啟動時所要建立的 Azure 網路和子網路。
9. 選取 [立即設定選取的機器]，將網路設定套用至您選取要進行保護的所有機器。 選取 [稍後設定] 以選取每部機器的 Azure 網路。
10. 在 [虛擬機器] > [選取虛擬機器] 中，按一下並選取您要複寫的每部機器。 您只能選取可以啟用複寫的機器。 然後按一下 [確定] 。
11. 在 [名稱] > [設定屬性] 中，選取處理序伺服器將用來在機器上自動安裝行動服務的帳戶。
12. 在 [複寫設定] > [進行複寫設定] 中，確認已選取正確的複寫原則。
13. 按一下 [啟用複寫] 。

您可以在 [設定]  >  [作業]  >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 執行 [完成保護]  作業之後，機器即準備好進行容錯移轉。

若要監視您新增的 VM，您可以在 [組態伺服器] 中查看上次探索 VM 的時間。
> **上次連絡時間**。 若要新增 VM 而不等候已排定的探索，請醒目提示設定伺服器 (不要按一下)，然後按一下 [重新整理]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [執行災害復原演練](site-recovery-test-failover-to-azure.md)
