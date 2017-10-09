---
title: "SAP HANA on Azure (大型執行個體) 的高可用性和災害復原 | Microsoft Docs"
description: "建立高可用性並為 SAP HANA on Azure (大型執行個體) 的災害復原做規劃"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2016
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: c7f3da9a92d5e9c60355c93a7205d16dc9ab8390
ms.contentlocale: zh-tw
ms.lasthandoff: 09/28/2017

---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure 上 SAP Hana (大型執行個體) 的高可用性和災害復原 

高可用性和災害復原 (DR) 對執行具任務關鍵性的 SAP HANA on Azure (大型執行個體) 伺服器來說，是相當重要的層面。 請務必與 SAP、您的系統整合者或 Microsoft 合作，以便正確建構及實作正確的高可用性和災害復原策略。 也務必考量您環境特有的復原點目標 (RPO) 和復原時間目標。

Microsoft 可透過 HANA 大型執行個體支援某些 SAP HANA 高可用性功能。 這些功能包括：

- **儲存體複寫：**儲存體系統將所有資料複寫到另一個 Azure 區域中另一個 HANA 大型執行個體戳記的能力。 SAP HANA 獨立運作，不依賴此方法。
- **HANA 系統複寫**︰將 SAP HANA 中的所有資料複寫到個別的 SAP HANA 系統。 復原時間目標是透過定期的資料複寫而最小化。 SAP HANA 支援非同步、記憶體內同步及同步模式。 同步模式僅建議用於相同資料中心內或距離 100 公里內的 SAP HANA 系統。 以 HANA 大型執行個體戳記目前的設計而言，HANA 系統複寫只可用於高可用性。 目前，HANA 系統複寫需要第三方反向 Proxy 元件，以便在另一個 Azure 區域中進行災害復原設定。 
- **主機自動容錯移轉**︰SAP HANA 的本機錯誤復原解決方案，可供作為 HANA 系統複寫的替代選項。 如果主要節點變得無法使用，您可以相應放大模式設定一或多個待命 SAP HANA 節點，而 SAP HANA 會自動容錯移轉到待命節點。

SAP HANA on Azure (大型執行個體) 會在兩個 Azure 區域提供，以地緣政治來說，則涵蓋三個不同的區域 (美國、澳洲和歐洲)。 裝載 HANA 大型執行個體戳記的兩個不同區域會分別連線至各自專用的網路電路，以便複寫儲存體快照集來提供災害復原方法。 系統依預設不會建立複寫。 訂購了災害復原功能的客戶才會設定複寫作業。 執行儲存體複寫需要使用 HANA 大型執行個體的儲存體快照集。 您無法選擇 Azure 區域作為 DR 區域，因為該區域位於不同的地緣政治地區。 

下表顯示目前支援的高可用性和災害復原方法以及兩者的組合：

| HANA 大型執行個體所支援的案例 | 高可用性選項 | 災害復原選項 | 註解 |
| --- | --- | --- | --- |
| 單一節點 | 無法使用。 | 專用 DR 設定。<br /> 多用途 DR 設定。 | |
| 主機自動容錯移轉：N+m<br /> 包括 1 + 1 | 可透過擔任作用中角色的待命節點來實現。<br /> HANA 會控制角色的切換。 | 專用 DR 設定。<br /> 多用途 DR 設定。<br /> 使用儲存體複寫進行的 DR 同步處理。 | HANA 磁碟區組會連結至所有節點 (n+m)。<br /> DR 網站必須具有相同數目的節點。 |
| HANA 系統複寫 | 可透過主要或次要設定來實現。<br /> 在容錯移轉的情況下，次要節點會轉而成為主要節點。<br /> HANA 系統複寫和 OS 控制容錯移轉。 | 專用 DR 設定。<br /> 多用途 DR 設定。<br /> 使用儲存體複寫進行的 DR 同步處理。<br /> 若沒有第三方元件，則還無法實現使用 HANA 系統複寫來進行的 DR。 | 另一組不同的磁碟區會連結至每個節點。<br /> 只有生產網站中次要複本的磁碟區會複寫到 DR 位置。<br /> DR 網站需要一組磁碟區。 | 

作為專用 DR 設定時，DR 網站中的 HANA 大型執行個體單位不會用於執行任何其他工作負載或非生產系統。 該單位是被動的，只會在災害容錯移轉執行時部署。 然而，這不是許多客戶偏好的選擇。

作為多用途 DR 設定時，DR 網站上的 HANA 大型執行個體單位會執行非生產工作負載。 在災害情況下，您會關閉非生產系統，掛接儲存體複寫的 (其他) 磁碟區組，然後啟動生產 HANA 執行個體。 許多使用 HANA 大型執行個體災害復原功能的客戶都使用此組態。 


您可以在下列 SAP 文章中找到更多關於 SAP HANA 高可用性的資訊： 

- [SAP HANA 高可用性白皮書](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理指南](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP Academy 的 SAP HANA 系統複寫影片](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 支援附註 #1999880 - SAP HANA 系統複寫常見問題集](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP 支援附註 #2165547 - SAP HANA 系統複寫環境內的 SAP HANA 備份與還原](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 支援附註 #1984882 - 使用 SAP HANA 系統複寫以最短/零停機時間的方式進行硬體交換](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>使用 HANA 大型執行個體之災害復原的網路考量

若要利用 HANA 大型執行個體的災害復原功能，您必須設計連到兩個不同 Azure 區域的網路連線。 您需要一條從主要 Azure 區域中的內部部署連接的 ExpressRoute 線路，還需要另一條從內部部署連接到災害復原區域的線路。 此措施可解決 Azure 區域 (包含 Microsoft Enterprise Edge (MSEE) 路由器位置) 發生問題的情況。

作為第二個措施，您可以將連線到其中一個區域中 SAP HANA on Azure (大型執行個體) 的所有 Azure 虛擬網路，都連線到與其他區域中的 HANA 大型執行個體連線的 ExpressRoute 線路。 透過該「交叉連線」，於區域 #1 的 Azure 虛擬網路中執行的服務可以連線到區域 #2 的 HANA 大型執行個體單位，反之亦然。 此措施可解決將內部部署位置與 Azure 連接的 MSEE 位置之中，只有一個離線的情況。

下圖說明災害復原的復原組態：

![災害復原的最佳組態](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-when-you-use-hana-large-instances-storage-replication-for-disaster-recovery"></a>在災害復原中使用 HANA 大型執行個體儲存體複寫時的其他需求

使用 HANA 大型執行個體之災害復原設定的其他需求如下：

- 您必須訂購與生產 SKU 大小相同的 SAP HANA on Azure (大型執行個體) SKU，並部署在災害復原區域中。 在目前的客戶部署中，這些執行個體會用來執行非生產 HANA 執行個體。 我們將其稱為「多用途 DR 設定」。   
- 針對您在災害復原網站中想要復原的每個 SAP HANA on Azure (大型執行個體) SKU，您都必須在 DR 網站上為其訂購其他儲存體。 購買額外的儲存體，可讓您配置存放磁碟區。 您可以配置磁碟區，這些磁碟區是從生產 Azure 區域將儲存體複寫到災害復原 Azure 區域時的目標。
 

## <a name="backup-and-restore"></a>備份與還原

操作資料庫的其中一個最重要層面，就是保護資料庫免於遭受各種災難事件。 這些事件可能由任何原因造成，從天然災害到簡單的使用者錯誤都有可能。

備份資料庫時，若提供還原到任何時間點 (例如在某人刪除重要資料之前) 的功能，便可讓還原到儘可能接近中斷之前的狀態。

必須執行兩種類型的備份，才能獲得最佳結果：

- 資料庫備份：完整、增量或差異備份
- 交易記錄備份

除了在應用程式層級執行的完整資料庫備份之外，您可以透過儲存體快照集執行備份。 儲存體快照集並不會取代交易記錄備份。 對於「將資料庫還原到特定時間點」或是「從已經認可的交易清空記錄」來說，交易記錄備份仍然很重要。 不過，儲存體快照集可加快復原速度，因為它們可以快速地提供資料庫的向前復原映像。 

SAP HANA on Azure (大型執行個體) 提供兩個備份和還原選項：

- 自己動手做 (DIY)。 在計算以確保磁碟空間足夠之後，請使用磁碟備份方法來執行完整資料庫和記錄備份。 您可以直接備份到連結至 HANA 大型執行個體單位的磁碟區，或備份到 Azure 虛擬機器 (VM) 中設定的網路檔案共用 (NFS)。 在後面一種情況下，客戶會在 Azure 中設定 Linux VM、將 Azure 儲存體連結至 VM，並透過該 VM 中所設定的 NFS 伺服器來共用儲存體。 如果對直接連結至 HANA 大型執行個體單位的磁碟區執行備份，您必須將備份複製到 Azure 儲存體帳戶 (在設定 Azure VM 來匯出以 Azure 儲存體為基礎的 NFS 共用之後)。 或者，您可使用 Azure 備份保存庫或 Azure 冷儲存體。 

   另一個選項是在備份複製到 Azure 儲存體帳戶之後，使用第三方資料保護工具來儲存備份。 針對因合規性和稽核目的而需要較長儲存期的資料，DIY 備份選項可能也有其必要性。 不論是哪種情況，備份都會複製到透過 VM 與 Azure 儲存體所表示的 NFS 共用中。

- 使用 SAP HANA on Azure (大型執行個體) 的根本基礎結構所提供的備份和還原功能。 此選項可滿足備份和快速還原的需要。 本節的其餘部分討論 HANA (大型執行個體) 隨附的備份和還原功能。 本節也涵蓋備份和還原對於 HANA 大型執行個體所提供之災害復原功能的關聯性。

> [!NOTE]
> HANA 大型執行個體的底層基礎結構所使用的快照集技術相依於 SAP HANA 快照集。 此時，SAP HANA 快照集還無法搭配 SAP HANA 多租用戶資料庫容器的多個租用戶一起使用。 因此，當您在 SAP HANA 多租用戶資料庫容器中部署多個租用戶時，將無法使用這種備份方法。 如果只部署一個租用戶，SAP HANA 快照集則可使用。

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 SAP HANA on Azure (大型執行個體) 的儲存體快照

SAP HANA on Azure (大型執行個體) 底下的儲存體基礎結構支援磁碟區的儲存體快照集。 不論是備份還是還原磁碟區都受到支援，但有下列考量事項：

- 系統會經常建立存放磁碟區快照，而不是進行完整的資料庫備份。
- 針對 /hana/data、/hana/log 和 /hana/shared (包括 /usr/sap) 磁碟區來觸發快照集時，儲存體快照集會先起始 SAP HANA 快照集，再執行儲存體快照集。 在儲存體快照集復原之後，此 SAP HANA 快照集是最後記錄還原的設定點。
- 在順利執行儲存體快照集後，系統會刪除 SAP HANA 快照集。
- 系統會經常建立交易記錄備份並儲存在 /hana/logbackups 磁碟區或 Azure 中。 您可以觸發含有交易記錄備份的 /hana/logbackups 磁碟區，對它個別擷取快照集。 在此情況下，您不需要執行 HANA 快照集。
- 如果您必須將資料庫還原到特定時間點，請要求「Microsoft Azure 支援服務」(適用於生產環境中斷) 或「SAP HANA on Azure 服務管理」將資料庫還原到特定的儲存體快照集。 例如，將沙箱系統還原到其原始狀態的計劃性還原。
- 儲存體快照集所包含的 SAP HANA 快照集是一個位移點，用來套用在儲存體快照集建立後已執行和儲存的交易記錄備份。
- 建立這些交易記錄備份的目的是要將資料庫還原回特定的時間點。

您可以執行以三種不同的磁碟區類別為目標的儲存體快照集：

- 將針對 /hana/data、/hana/log 和 /hana/shared (包括 /usr/sap) 的快照集合在一起。 此快照集需要執行 SAP HANA 快照集。
- 針對 /hana/logbackups 的個別快照集。
- OS 分割區 (只適用於 Type I 的 HANA 大型執行個體)。


### <a name="storage-snapshot-considerations"></a>儲存體快照考量事項

>[!NOTE]
>儲存體快照集會取用已配置給 HANA 大型執行個體單位的儲存空間。 因此，您必須考慮以下有關排定儲存體快照集和保留多少儲存體快照集等方面的問題。 

SAP HANA on Azure (大型執行個體) 儲存體快照集的獨特技巧包括：

- 特定的儲存體快照集 (在建立快照集的時間點) 只會取用少量儲存體。
- 隨著在存放磁碟區上資料內容發生變更及 SAP HANA 資料檔中的內容發生變更，快照便需要儲存原始區塊內容以及資料變更。
- 因此，儲存體快照的大小會增加。 快照存在的時間越長，儲存體快照就會變得越大。
- 在儲存體快照集的生命週期中對 SAP HANA 資料庫磁碟區進行的變更越多，儲存體快照集所耗用的空間就越大。

SAP HANA on Azure (大型執行個體) 隨附固定的磁碟區大小供 SAP HANA 資料和記錄磁碟區使用。 執行這些磁碟區的快照集會耗用磁碟區空間。 您必須決定何時要排定儲存快照集。 您也必須監視存放磁碟區的空間耗用量，以及管理您儲存的快照集數目。 您可以在匯入大量資料或對 HANA 資料庫執行其他重大變更時，停用儲存體快照集。 


下列各節提供執行這些快照集的資訊，包括一般建議事項：

- 雖然硬體的每一磁碟區都可支援 255 個快照集，但強烈建議您讓快照集數目保持遠低於這個數字。
- 執行儲存體快照集之前，請監視並追蹤可用空間。
- 根據可用空間來降低儲存體快照的數目。 您可以降低保留的快照集數目，也可以擴充磁碟區。 您可以訂購額外的儲存空間，以 1 TB 為單位。
- 在「使用 SAP 平台移轉工具 (R3load) 將資料移到 SAP HANA」或是「從備份還原 SAP HANA 資料庫」等活動進行期間，請在 /hana/data 磁碟區上停用儲存體快照集。 
- 在進行較大規模的 SAP HANA 資料表重組期間，應儘可能避免執行儲存體快照。
- 儲存體快照集也是利用 SAP HANA on Azure (大型執行個體) 之災害復原功能的必要條件。

### <a name="setting-up-storage-snapshots"></a>設定儲存體快照

使用 HANA 大型執行個體來設定儲存體快照集的步驟如下：
1. 確定 HANA (大型執行個體) 伺服器的 Linux 作業系統上已安裝 Perl。
2. 修改 /etc/ssh/ssh\_config 以新增 _MACs hmac-sha1_ 這一行。
3. 針對您目前執行的每個 SAP HANA 執行個體，在主要節點上建立 SAP HANA 備份使用者帳戶 (如果適用)。
4. 在所有 SAP HANA (大型執行個體) 伺服器上安裝 SAP HANA HDB 用戶端。
5. 在每個區域的第一部 SAP HANA (大型執行個體) 伺服器上，建立一個公開金鑰，以便存取其底下控制快照集建立的儲存體基礎結構。
6. 將指令碼和組態檔從 [ GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 複製到 SAP HANA 安裝中的 **hdbsql** 位置。
7. 視需要修改 HANABackupDetails.txt 檔案以符合適當的客戶規格。

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步驟 1：安裝 SAP HANA HDB 用戶端

安裝在 SAP HANA on Azure (大型執行個體) 上的 Linux 作業系統包含針對備份和災害復原目的執行 SAP HANA 儲存體快照集時所需的資料夾與指令碼。 檢查 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 中是否有更近期的版本。 最新的指令碼版本為 2.1。
不過，安裝 SAP HANA 時，是由您負責在 HANA 大型執行個體單位上安裝 SAP HANA HDB 用戶端 (Microsoft 不會安裝 HDBclient 或 SAP HANA)。

### <a name="step-2-change-the-etcsshsshconfig"></a>步驟 2：變更 /etc/ssh/ssh\_config

新增 _MACs hmac-sha1_ 這一行來變更 `/etc/ssh/ssh_config`，如下所示：
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>步驟 3︰建立公開金鑰

若要能夠存取 HANA 大型執行個體租用戶的儲存體快照集介面，您必須透過公開金鑰建立登入。 在租用戶的第一部 SAP HANA on Azure (大型執行個體) 伺服器上，建立用來存取儲存體基礎結構的公開金鑰，讓您能夠建立快照集。 此公開金鑰可確保不需密碼即可登入儲存體快照集介面。 建立公開金鑰，也表示您不需要維護密碼認證。 在 SAP HANA (大型執行個體) 伺服器上的 Linux 中，請執行下列命令來產生公開金鑰：
```
  ssh-keygen –t dsa –b 1024
```
新的位置是 **_/root/.ssh/id\_dsa.pub**。 請勿輸入實際密碼，否則您每次登入時都需要輸入密碼。 改為按 **Enter** 兩次，在登入時就不需要輸入密碼。

將資料夾切換至 **/root/.ssh/**，然後執行 `ls` 命令，以確定已如預期地更正公開金鑰。 如果金鑰存在，您可以執行下列命令來複製它︰

![執行此命令即可複製公開金鑰](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

此時，請連絡「SAP HANA on Azure 服務管理」並提供公開金鑰給他們。 服務代表會使用公開金鑰，將它註冊在針對 HANA 大型執行個體租用戶所開闢的底層儲存體基礎結構中。

### <a name="step-4-create-an-sap-hana-user-account"></a>步驟 4：建立 SAP HANA 使用者帳戶

若要開始建立 SAP HANA 快照集，您必須在 SAP HANA 中建立使用者帳戶，以供儲存體快照集指令碼使用。 為此目的，在 SAP HANA Studio 中建立 SAP HANA 使用者帳戶。 此帳戶必須具備下列權限：「備份管理」和「目錄讀取」。 在此範例中，使用者名稱是 **SCADMIN**。 HANA Studio 中所建立的使用者帳戶名稱會區分大小寫。 請務必選取 [否]，要求使用者在下次登入時變更密碼。

![在 HANA Studio 中建立使用者](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>步驟 5：授權 SAP HANA 使用者帳戶

在此步驟中，您可針對您所建立的 SAP HANA 使用者帳戶進行授權，讓指令碼不必在執行階段提交密碼。 SAP HANA 命令 `hdbuserstore` 可讓您建立儲存在一或多個 SAP HANA 節點上的 SAP HANA 使用者金鑰。 使用者金鑰可讓使用者存取 SAP HANA，而不需要在指令碼程序內管理密碼。 稍後再討論指令碼處理。

>[!IMPORTANT]
>以 `root` 身分執行下列命令。 否則，指令碼無法正常運作。

輸入 `hdbuserstore` 命令，如下所示︰

**非 MDC HANA 安裝程式**
```
hdbuserstore set <key> <host><3[instance]15> <user> <password>
```

**MDC HANA 安裝程式**
```
hdbuserstore set <key> <host><3[instance]13> <user> <password>
```

在以下範例中，使用者為 **SCADMIN01**、主機名稱為 **lhanad01**，而執行個體編號是 **01**：
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
如果您有 SAP HANA 相應放大組態，請從單一伺服器管理所有指令碼。 在此範例中，必須針對每個主機改變 SAP HANA 金鑰 **SCADMIN01**，以反映與金鑰相關的主機。 以 HANA DB 的執行個體編號修訂 SAP HANA 備份帳戶。 此金鑰必須具備所被指派主機上的系統管理權限，而相應放大組態的備份使用者則必須具備所有 SAP HANA 執行個體的存取權限。 假設有三個名稱分別為 **lhanad01**、**lhanad02** 和 **lhanad03** 的相應放大節點，命令序列會像這樣：

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步驟 6：取得快照集指令碼、設定快照集並測試組態與連線能力

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 下載最新版本的指令碼。 將下載的指令碼和文字檔複製到 **hdbsql** 的工作目錄。 就目前的 HANA 安裝而言，這個目錄就像 /hana/shared/D01/exe/linuxx86\_64/hdb。 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl 
HANABackupCustomerDetails.txt 
``` 


不同指令碼和檔案的用途如下：

- **azure\_hana\_backup.pl**：使用 cron 來排定這個指令碼，以在 HANA 資料/記錄/共用磁碟區、/hana/logbackups 磁碟區或作業系統 (在 HANA 大型執行個體的 Type I SKU 上) 上執行儲存體快照集。
- **azure\_hana\_replication\_status.pl**：此指令碼提供有關從生產網站到災害復原網站的複寫狀態基本詳細資料。 此指令碼可進行監視以確保正在進行複寫，並顯示正在複寫的項目大小。 它也可提供複寫時間太久或連結中斷時的指引。
- **azure\_hana\_snapshot\_details.pl**：此指令碼提供每個磁碟區在環境中現存之所有快照集的基本詳細資料清單。 此指令碼可在主要伺服器或在災害復原位置的伺服器單位上執行。 此指令碼提供下列資訊，這些資訊會依包含快照集的每個磁碟區細分：
   * 磁碟區中所有快照集的大小
   * 該磁碟區中的每個快照集都包含下列詳細資料： 
      - 快照集名稱 
      - 建立時間 
      - 快照集的大小
      - 快照集的頻率
      - 與該快照集相關聯的 HANA 備份識別碼 (如果相關)
- **azure\_hana\_snapshot\_delete.pl**：此指令碼可刪除一個儲存體快照集或一組快照集。 您可以使用在 HANA Studio 中找到的 SAP HANA 備份識別碼或儲存體快照集名稱。 備份識別碼目前只會與針對 HANA 資料/記錄/共用磁碟區所建立的快照集繫結。 否則，如果您輸入快照集識別碼，則指令碼會搜尋所有與輸入的快照集識別碼相符的快照集。  
- **testHANAConnection.pl**：此指令碼會測試 SAP HANA 執行個體的連線，需要有此連線才能設定儲存體快照集。
- **testStorageSnapshotConnection.pl**：此指令碼有兩種用途。 第一種用途是確保執行指令碼的 HANA 大型執行個體單位可存取指派的儲存體虛擬機器，進而能夠存取 HANA 大型執行個體的儲存體快照集介面。 第二種用途是針對您要測試的 HANA 執行個體建立暫時性的快照集。 您應該對伺服器上的每個 HANA 執行個體執行此指令碼，以確保備份指令碼可如預期般運作。
- **removeTestStorageSnapshot.pl**：此指令碼可刪除以 **testStorageSnapshotConnection.pl** 指令碼所建立的測試快照集。 
- **HANABackupCustomerDetails.txt**：這個檔案是可修改的組態檔，請加以修改以適應您的 SAP HANA 組態。

 
HANABackupCustomerDetails.txt 檔案是執行儲存體快照集之指令碼的控制及組態檔。 調整該檔案以因應您的用途和設定。 當您的執行個體部署完成時，您應該會從「SAP HANA on Azure 服務管理」收到「儲存體備份名稱」和「儲存體 IP 位址」。 您不能修改此檔案中任何變數的順序、排序或間距。 否則，指令碼將無法正常執行。 此外，您會從「SAP HANA on Azure 服務管理」收到相應放大節點或主要節點 (如果相應放大) 的 IP 位址。 您也知道在 SAP HANA 安裝期間取得的 HANA 執行個體編號。 您現在必須將備份名稱新增至組態檔。

若為相應增加或相應放大部署，在您填入儲存體備份名稱和儲存體 IP 位址之後，組態檔會如以下範例所示。 您也需要填寫組態檔中的下列資料：
- 單一節點或主要節點 IP 位址
- HANA 執行個體編號
- 備份名稱 
    
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA userstore Name:
```

>[!NOTE]
>目前，實際 HANA 儲存快照集指令碼中只使用節點 1 詳細資料。 我們建議您往返所有 HANA 節點來測試存取，以便如果主要備份節點變更，您就已確定任何其他節點可以修改節點 1 中的詳細資料，以接替此節點。

將所有組態資料放入 HANABackupCustomerDetails.txt 檔案後，您必須檢查有關 HANA 執行個體資料的組態是否正確。 使用指令碼 `testHANAConnection.pl`。 此指令碼與 SAP HANA 相應增加或相應放大組態無關。

```
testHANAConnection.pl
```

如果您有 SAP HANA 相應放大組態，請確定主要的 HANA 執行個體能夠存取所有必要的 HANA 伺服器和執行個體。 測試指令碼並無參數，但您必須將資料新增至 HANABackupCustomerDetails.txt 組態檔，指令碼才能正常執行。 只會傳回殼層命令錯誤碼，所以指令碼無法檢查每個執行個體的錯誤。 即便如此，指令碼會提供一些實用的意見讓您再次檢查。

若要執行指令碼，請輸入下列命令：
```
 ./testHANAConnection.pl
```
如果指令碼成功取得 HANA 執行個體的狀態，則會顯示 HANA 連接成功的訊息。


下一個測試步驟是根據您放入 HANABackupCustomerDetails.txt 組態檔的資料來檢查儲存體的連線，然後執行測試快照集。 在執行 `azure_hana_backup.pl` 指令碼之前，您必須先執行此指令碼。 如果磁碟區不含快照集，則無法判斷原因是磁碟區空白，還是 SSH 失敗而無法取得快照集詳細資料。 因此，指令碼會執行兩個步驟：

- 它會確認租用戶的儲存體虛擬機器和介面是否可供存取以便讓指令碼執行快照集。
- 依 HANA 執行個體，為每個磁碟區建立測試 (或虛設) 快照集。

基於這個理由，會包含 HANA 執行個體作為引數。 如果執行失敗，則無法提供儲存體連線錯誤檢查。 即使沒有錯誤檢查，指令碼也會提供有用的提示。

指令碼的執行方式為：
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
接著，指令碼會嘗試使用在先前設定步驟中提供的公開金鑰和 HANABackupCustomerDetails.txt 檔案中所設定的資料來登入儲存體。 如果登入成功，則會顯示下列內容：

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

如果發生問題，請連線到儲存體主控台，其輸出如下所示：

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

成功登入儲存體虛擬機器介面之後，指令碼會繼續進行階段 #2 並建立測試快照集。 SAP HANA 的三個節點相應放大組態輸出如下所示：

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

如果已成功使用此指令碼執行測試快照集，您可以繼續設定實際的儲存體快照集。 如果不成功，請先調查問題，再繼續進行。 測試快照集應先留著，直到完成第一個實際快照集為止。


### <a name="step-7-perform-snapshots"></a>步驟 7：執行快照集

當所有準備步驟都完成後，您就可以開始設定實際的儲存體快照集組態。 要排程的指令碼可與 SAP HANA 相應增加和相應放大組態搭配運作。 您應透過 cron 來排定指令碼的執行。 

可以建立的快照集備份有三種：
- **HANA**：合併的快照集備份，其中包含 /hana/data 和 /hana/shared (也包含 /usr/sap) 的磁碟區會由協調式快照集所涵蓋。 您可以從這個快照集還原單一檔案。
- **記錄**︰/hana/logbackups 磁碟區的快照集備份。 不會觸發 HANA 快照集來執行此儲存體快照集。 此存放磁碟區是應包含 SAP HANA 交易記錄備份的磁碟區。 SAP HANA 交易記錄備份會提高執行頻率，以限制記錄成長並防止資料遺失。 您可以從這個快照集還原單一檔案。 請勿將執行頻率降低到 5 分鐘以下。
- **開機**：包含 HANA 大型執行個體之開機邏輯單元編號 (LUN) 的磁碟區快照集。 您只能透過 HANA 大型執行個體的 Type I SKU 來進行此快照集備份。 您無法從包含開機 LUN 之磁碟區的快照集還原單一檔案。  


這三種不同快照集的呼叫語法如下所示：
```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot none manual 30

```

您必須指定下列參數︰

- 第一個參數描繪快照集備份的類型。 允許的值為 **hana**、**logs** 和 **boot**。 
- 第二個參數是 **HANA SID** (如 HM3) 或 **none**。 如果第一個提供的參數值 **hana** 或 **logs**，則此參數的值是 **HANA SID** (如 HM3)，否則對開機磁碟區備份而言，這個值是 **none**。 
- 第三個參數是屬於快照集類型的快照集或備份標籤。 它有兩個目的。 其中一個目的是為它命名，好讓您知道這些快照集的相關內容。 第二個的目的是要讓指令碼 azure\_hana\_backup.pl 判斷保留在該特定標籤之下的儲存體快照集數目。 如果您使用兩個不同的標籤來排定兩個相同類型的儲存體快照集備份 (例如**hana**)，並定義各自應該保留 30 個快照集，您最後將會得到受影響磁碟區的 60 個儲存體快照集。 
- 第四個參數會藉由定義要保留且具有相同快照集首碼 (標籤) 的快照集數目，來間接定義快照集的保留期。 對於透過 cron 進行的已排定執行作業來說，這個參數很重要。 

在相應放大的情況下，指令碼會執行一些額外檢查，確保您可以存取所有的 HANA 伺服器。 指令碼也會先檢查所有 HANA 執行個體是否都傳回適當的執行個體狀態，然後再建立 SAP HANA 快照集。 SAP HANA 快照集的後面會接著儲存快照集。

執行 `azure_hana_backup.pl` 指令碼會以下列三個不同的階段建立儲存體快照集：

1. 執行 SAP HANA 快照集
2. 執行儲存體快照集
3. 先移除已建立的 SAP HANA 快照集，再執行儲存體快照集。

若要執行指令碼，您可從指令碼複製到的 HDB 可執行檔資料夾中呼叫它。 

保留期取決於您執行指令碼時以參數送出的快照集數目 (例如上面所示的 **30**)。 因此，儲存體快照集所涵蓋的時間量是由以下兩項所構成的函式：執行期間以及在執行指令碼時提交作為參數的快照集數目。 如果所保留的快照集數目超出指令碼呼叫中以參數指定的數目，將會先刪除相同標籤的最舊儲存體快照集 (在我們前面的案例中為 manual)，然後才執行新的快照集。 您所提供作為最後呼叫參數的數字，就是您可用來控制要保留之快照集數目的數字。 藉此數字，您也可以間接控制快照集所使用的磁碟空間。 

> [!NOTE]
>只要您一變更標籤，就會重新開始計算。 這表示您必須嚴謹處理標籤，您的快照集才不會遭到意外刪除。

### <a name="snapshot-strategies"></a>快照集策略
針對不同類型的快照集，其頻率取決於您是否使用 HANA 大型執行個體災害復原功能。 HANA 大型執行個體的災害復原功能會依賴儲存快照集。 依賴儲存快照集可能需要一些有關儲存體快照集執行頻率和期間的特殊建議。 

在下面的考量和建議中，我們假設您「不」使用 HANA 大型執行個體所提供的災害復原功能。 相反地，您會使用儲存體快照集作為建立備份的方法，而且您能夠提供過去 30 天的時間點復原。 在快照集數量和空間有限的情況下，客戶考量了下列需求：

- 時間點復原的復原時間。
- 使用的空間。
- 潛在災害復原的復原點目標和復原時間目標。
- 對磁碟執行的最終 HANA 完整資料庫備份。 每次對磁碟執行完整資料庫備份，或執行 **backint** 介面時，執行儲存體快照集都會失敗。 如果除了儲存體快照集之外，您還打算執行完整資料庫備份，請確定在此期間停用儲存體快照集的執行。
- 每個磁碟區的快照集數目只能有 255 個。


對於不使用 HANA 大型執行個體災害復原功能的客戶，快照集期間較不頻繁。 在這種情況下，我們發現客戶會以 12 小時或 24 小時的期間來對 /hana/data 和 /hana/shared (包括 /usr/sap) 執行合併的快照集，並保留這些快照集以涵蓋一整個月。 記錄備份磁碟區的快照集也是如此。 然而，記錄備份磁碟區的 SAP HANA 交易記錄備份，則會以 5 到 15 分鐘的期間來執行。

我們鼓勵您利用 cron 執行排定的儲存體快照集。 我們也建議您使用相同的指令碼來符合所有備份和災害復原需求。 您需要修改指令碼輸入，以符合各種要求的備份時間。 這些快照集在 cron 中都會根據其執行時間以不同方式排定：每小時、12 小時、每天或每週。 

/etc/crontab 中的 cron 排程範如下所示：
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
在上述範例中，有每小時的合併快照集會涵蓋含有 /hana/data 和 /hana/shared (包括 /usr/sap) 位置的磁碟區。 這種快照集可會來加快過去兩天內的時間點復原速度。 此外，這些磁碟區會有每日快照集。 因此，您會獲得涵蓋兩天的每小時快照集，以及涵蓋四週的每日快照集。 此外，每天會備份一次交易記錄備份磁碟區。 這些備份也會保存四週。 正如您在 crontab 第三行所看到的，系統會排定每五分鐘執行一次 HANA 交易記錄備份。 執行儲存體快照集之不同 cron 作業的開始時間會錯開，以免系統在特定時間點一次執行所有快照集。 

在以下範例中，您會每小時執行一次合併的快照集，其所涵蓋的磁碟區包含 /hana/data 和 /hana/shared (包括 /usr/sap) 位置。 您會將這些快照集保留兩天。 交易記錄備份磁碟區的快照集會每隔五分鐘執行一次，並保留四個小時。 和之前一樣，系統會將 HANA 交易記錄檔排定為每五分鐘備份一次。 在開始交易記錄備份後，系統會延後兩分鐘再執行交易記錄備份磁碟區的快照集。 一般情況下，SAP HANA 交易記錄備份應該能在這兩分鐘的時間內完成。 和之前一樣，儲存體快照集每天會將包含開機 LUN 的磁碟區備份一次，並保留四週之久。

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

下圖說明上述範例的順序，但不包括開機 LUN：

![備份與快照集之間的關聯性](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 會定期對 /hana/log 磁碟區執行寫入作業，以將認可的變更記錄至資料庫。 SAP HANA 會定期將儲存點寫入 /hana/data 磁碟區。 SAP HANA 交易記錄備份會依照 crontab 的指定每五分鐘執行一次。 您也會看到系統會因為針對 /hana/data 和 /hana/shared 磁碟區觸發合併的儲存體快照集，而每小時執行一次 SAP HANA 快照集。 HANA 快照集成功之後，就會執行合併的儲存體快照集。 在 HANA 交易記錄備份完成大約兩分鐘後，系統會依照 crontab 的指示，每隔五分鐘在 /hana/logbackup 磁碟區上執行一次儲存體快照集。


>[!IMPORTANT]
> 只有在快照集與 SAP HANA 交易記錄備份一起執行時，使用儲存體快照集來進行 SAP HANA 備份才有價值。 這些交易記錄備份必須要能夠涵蓋儲存體備份之間的時段。 

如果您已對使用者承諾執行 30 天的時間點復原，請執行下列作業︰

- 在極端情況下，您必須能夠存取針對 /hana/data 和 /hana/shared 所建立的合併儲存體快照集，而且可達 30 天之久。
- 擁有連續交易記錄備份，其涵蓋任何合併儲存體快照之間的時間。 因此，最舊的交易記錄備份磁碟區快照集必須達 30 天之久。 如果您將交易記錄備份複製到位於 Azure 儲存體上的另一個 NFS 共用，情況並非如此。 在這種情況下，您可以從該 NFS 共用提取舊的交易記錄備份。

若要受惠於儲存體快照集並最終受益於交易記錄備份的儲存體複寫，您必須變更 SAP HANA 用來寫入交易記錄備份的目的地位置。 您可以在 HANA Studio 中進行這項變更。 雖然 SAP HANA 會自動備份完整的記錄區段，但您應該指定記錄備份間隔來加以確定。 使用災害復原選項時尤其如此，因為您通常會想要以確定的期間來執行記錄備份。 在下面的案例中，我們採用 15 分鐘的記錄備份間隔。

![在 SAP HANA Studio 中排定 SAP HANA 備份記錄](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

您可以選擇比每隔 15 分鐘更頻繁的備份。 在搭配災害復原時會頻繁進行。 有些客戶會每隔五分鐘執行一次交易記錄備份。  

如果資料庫從未進行過備份，則最後一個步驟是執行檔案型備份，以建立必須存在於備份資料目錄內的單一備份項目。 否則，SAP HANA 無法起始您指定的記錄備份。

![建立檔案型備份以建立單一備份項目](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


在成功執行第一個儲存體快照集之後，您也可以刪除步驟 6 所執行的測試快照集。 若要這樣做，請執行 `removeTestStorageSnapshot.pl` 指令碼：
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>監視磁碟區上的快照集數目和大小

在特定的存放磁碟區上，您可以監視快照集的數目和這些快照集的儲存體耗用量。 `ls` 命令不會顯示快照集目錄或檔案。 不過，Linux 作業系統命令 `du` 會顯示這些儲存體快照集的詳細資料，因為它們儲存在相同的磁碟區上。 此命令可與下列選項搭配使用：

- `du –sh .snapshot`：提供快照集目錄內所有快照集的總計。
- `du –sh --max-depth=1`：列出所有儲存在 **.snapshot** 資料夾中的快照集及每個快照集的大小。
- `du –hc`：提供所有快照集使用的總大小。

您可以使用這些命令來確定所建立和儲存的快照集並未耗用磁碟區上的所有儲存體。

>[!NOTE]
>使用上述命令無法看到開機 LUN 的快照集。

### <a name="getting-details-of-snapshots"></a>取得快照集的詳細資料
若要取得更詳細的快照集資料，您也可以使用 `azure_hana_snapshot_details.pl` 指令碼。 如果災害復原位置有作用中的伺服器，則可以在任一位置執行此指令碼。 此指令碼提供下列輸出，這些輸出會依包含快照集的每個磁碟區細分： 
   * 磁碟區中所有快照集的大小
   * 該磁碟區中的每個快照集都包含下列詳細資料： 
      - 快照集名稱 
      - 建立時間 
      - 快照集的大小
      - 快照集的頻率
      - 與該快照集相關聯的 HANA 備份識別碼 (如果相關)

指令碼的執行語法如下所示：

```
./azure_hana_snapshot_details.pl 
```

因為指令碼會嘗試擷取 HANA 備份識別碼，所以它需要連線到 SAP HANA 執行個體。 此連線需要正確設定 HANABackupCustomerDetails.txt 組態檔。 磁碟區上兩個快照集的輸出如下所示：

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-a-storage-snapshot"></a>從儲存體快照集進行檔案層級還原
針對快照集類型 hana 和 logs，您可以直接在 **.snapshot** 目錄中的磁碟區上存取快照集。 每個快照集都有一個子目錄。 您應該能夠將處於快照集製作時間點之狀態的快照集所涵蓋的每個檔案，從該子目錄複製到實際的目錄結構中。

>[!NOTE]
>單一檔案還原不適用於開機 LUN 的快照集。 **.snapshot** 目錄不會在開機 LUN 中顯示出來。 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>縮減伺服器上的快照數目

如先前所述，您可以針對您儲存的快照集，減少某些標籤的數目。 用來起始快照之命令的最後兩個參數是一個標籤及您想要保留的快照數目。

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

在前一個範例中，快照集標籤是 customer，而此標籤內要保留的快照集數目則是 30。 在您因應磁碟空間耗用量時，可能會想要縮減所儲存的快照集數目。 若要將快照集數目減少為 15 (舉例來說)，有個簡單的方法是將最後一個參數設定為 **15** 來執行指令碼：

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

如果您以此設定執行指令碼，快照集數目 (包括新的儲存體快照集) 會是 15。 15 個最新的快照集會保留下來，然後會刪除 15 個較舊的快照集。

 >[!NOTE]
 > 有存在時間超過一小時的快照集時，此指令碼才會減少快照集數目。 指令碼不會刪除過去一小時內的快照集。 這些限制與所提供的選擇性災害復原功能有關。

如果您不想再維護一組含有特定備份標籤 (即語法範例中的 hanadaily) 的快照集，您可以使用 **0** 作為保留數字來執行指令碼。 這會移除所有符合該標籤的快照集。 不過，移除所有快照集可能會影響災害復原的功能。

若要刪除特定快照集，第二種可能性是使用 `azure_hana_snapshot_delete.pl` 指令碼。 此指令碼的用途在於使用在 HANA Studio 中找到的 HANA 備份識別碼或透過快照集名稱本身，來刪除單一快照集或一組快照集。 備份識別碼目前只會與針對 **hana** 快照集類型所建立的快照集繫結。 **logs** 和 **boot** 類型的快照集備份不會執行 SAP HANA 快照集。 因此，您不會找到這些快照集的備份識別碼。 如果您輸入快照集名稱，則指令碼會尋找不同磁碟區上符合所輸入快照集名稱的所有快照集。 此指令碼的呼叫語法為：

```
./azure_hana_snapshot_delete.pl 

```

您必須以 **root** 使用者身分執行指令碼。

如果您選取某個快照集，就能夠個別地刪除每個快照集。 您可先提供包含快照集的磁碟區，然後提供快照集名稱。 如果快照集存在於該磁碟區中，而且存在時間超過一小時，系統就會將它刪除。 您可藉由執行 `azure_hana_snapshot_details` 指令碼來尋找磁碟區名稱和快照集名稱。 

>[!IMPORTANT]
>如果有資料只存在於您要刪除的快照集上，若執行刪除作業，該資料就會永久遺失。

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>復原到最近的 HANA 快照集

如果您遇到生產環境停止運作的情況，可以向「Microsoft Azure 支援服務」以客戶事件的形式起始從儲存體快照集復原的程序。 如果生產系統中的資料遭到刪除，而挽救資料的唯一方法是還原生產資料庫，則為高緊急性的情況。

若是其他情況，則時間點復原可能為低緊急性，可提前幾天規劃。 您可以使用「SAP HANA on Azure 服務管理」來規劃此復原，以避免引發高優先性的問題。 例如，您可能打算藉由套用新的增強套件來升級 SAP 軟體。 然後，您必須還原為代表增強套件升級前狀態的快照集。

傳送要求之前，您必須做準備。 接著，「SAP HANA on Azure 服務管理」小組就可以處理該要求，並提供已還原的磁碟區。 之後，您會根據快照集來還原 HANA 資料庫。 以下是在要求之前如何做好準備︰

>[!NOTE]
>視您使用的 SAP HANA 版本而定，您的使用者介面可能與下列螢幕擷取畫面不同。

1. 決定要還原哪一個快照。 除非另有指示，否則只會還原 hana/data 磁碟區。 

2. 關閉 HANA 執行個體。

 ![關閉 HANA 執行個體](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 將每個 HANA 資料庫節點上的資料磁碟區卸載。 如果資料磁碟區仍掛接至作業系統，快照集還原會失敗。
 ![將每個 HANA 資料庫節點上的資料磁碟區卸載](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. 提出 Azure 支援要求，指示他們還原特定快照集。

 - 還原期間︰「SAP HANA on Azure 服務管理」可能會要求您參加電話會議，確保您會協調、驗證和確認所還原的儲存體快照集是正確的。 

 - 還原之後：還原儲存體快照集之後，「SAP HANA on Azure 服務管理」會通知您。

5. 在還原程序完成後，重新掛接所有資料磁碟區。

 ![重新掛接所有資料磁碟區](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. 當您透過 SAP HANA Studio 重新連接到 HANA DB 時，如果沒有自動出現復原選項，請選取 SAP HANA Studio 內的復原選項。 以下範例顯示還原到最後一個 HANA 快照集。 儲存體快照集會內嵌一個 HANA 快照集。 如果您要復原到最近的儲存體快照集，它應該是最近的 HANA 快照集。 (如果您要復原到較舊的儲存體快照，您就必須根據儲存體快照的建立時間找出 HANA 快照)。

 ![選取 SAP HANA Studio 內的復原選項](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. 選取 [將資料庫復原到特定資料備份或儲存體快照集]。

 ![[指定復原類型] 視窗](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. 選取 [指定不含目錄的備份]。

 ![[指定備份位置] 視窗](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. 在 [目的地類型] 清單中，選取 [快照集]。

 ![[指定要復原的備份] 視窗](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. 選取 [完成] 以開始復原程序。

 ![選取 [完成] 以開始復原程序。](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA 資料庫會還原並復原到儲存體快照集所包含的 HANA 快照集。

 ![HANA 資料庫會還原並復原到 HANA 快照集](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>復原到最近狀態

下列程序會還原儲存體快照集所包含的 HANA 快照集。 然後，將交易記錄備份還原至資料庫在還原儲存體快照集之前的最近狀態。

>[!IMPORTANT]
>繼續進行之前，請先確定您擁有一串完整且連續的交易記錄備份。 如果沒有這些備份，您就無法還原資料庫的目前狀態。

1. 完成[復原到最近的 HANA 快照集](#recovering-to-the-most-recent-hana-snapshot)中的步驟 1-6。

2. 選取 [將資料庫復原到最近狀態]。

 ![選取 [將資料庫復原到最近狀態]。](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. 指定最近 HANA 記錄備份的位置。 此位置必須包含從 HANA 快照集到最近狀態的所有 HANA 交易記錄備份。

 ![指定最近 HANA 記錄備份的位置](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. 選取一個備份作為基礎來從中復原資料庫。 在我們的範例中，螢幕擷取畫面中的 HANA 快照集是儲存體快照集所包含的 HANA 快照集。 

 ![選取一個備份作為基礎來從中復原資料庫](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. 如果 HANA 快照集快時間與最近狀態之間沒有差異，請清除 [使用差異備份] 核取方塊。

 ![如果沒有差異，請清除 [使用差異備份] 核取方塊](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. 在摘要畫面上，選取 [完成] 以開始還原程序。

 ![在摘要頁面上按一下 [完成]](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>復原到另一個時間點
若要復原到介於 HANA 快照集 (包含在儲存體快照集中) 與晚於 HANA 快照集時間點復原的快照集之間的時間點，請執行下列作業：

1. 確定您擁有包含從 HANA 快照到您想要復原之時間在內的所有 HANA 交易記錄備份。
2. 開始執行[復原到最近狀態](#recovering-to-the-most-recent-state)下的程序。
3. 在程序的步驟 2 中，於 [指定復原類型] 視窗選取 [將資料庫復原到下列時間點]，然後指定時間點。 接著完成步驟 3-6。

### <a name="monitoring-the-execution-of-snapshots"></a>監視快照集的執行

當您使用 HANA 大型執行個體的儲存體快照集時，您也需要監視這些儲存體快照集的執行情形。 執行儲存體快照集的指令碼會將輸出寫入檔案，然後儲存至與 Perl 指令碼相同的位置。 針對每個儲存體快照集都會寫入一個個別的檔案。 每個檔案的輸出會清楚顯示快照集指令碼所執行的各階段︰

1. 尋找需要建立快照集的磁碟區。
2. 尋找從這些磁碟區建立的快照集。
3. 刪除最後存在的快照集以符合您指定的快照集數目。
4. 建立 SAP HANA 快照集。
5. 建立所有磁碟區的儲存體快照集。
6. 刪除 SAP HANA 快照集。
7. 將最近的快照集重新命名為 **.0**。

此指令碼 cab 最重要的部分如下：
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
您可以從此範例看到指令碼如何記錄 HANA 快照的建立。 在相應放大案例中，是在主要節點上起始此程序。 主要節點會在每個背景工作角色節點上開始同步建立 SAP HANA 快照集。 然後建立儲存體快照。 順利執行儲存體快照之後，就會刪除 HANA 快照。 系統會從主要節點開始刪除 HANA 快照集。


## <a name="disaster-recovery-principles"></a>災害復原準則
對於 HANA 大型執行個體，我們提供了能在不同 Azure 區域的 HANA 大型執行個體戳記之間執行的災害復原功能。 例如，如果您將 HANA 大型執行個體單位部署在 Azure 的美國西部區域，即可使用美國東部區域的 HANA 大型執行個體單位來作為災害復原單位。 如先前所述，災害復原功能不會自動進行設定，因為如果要使用此功能，您還必須支付 DR 區域中的另一個 HANA 大型執行個體單位。 災害復原設定適用於相應增加以及相應放大設定。 

在截至目前為止所部署的案例中，客戶會使用 DR 區域中的單位來執行非生產系統，這些系統會使用已安裝的 HANA 執行個體。 HANA 大型執行個體單位的 SKU 必須與生產用途所使用的 SKU 相同。 Azure 生產區域與災害復原區域中的伺服器單位之間的磁碟組態如下所示：

![從磁碟觀點來看的 DR 設定組態](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

如此概觀圖所示，您接著需要排序第二組的磁碟區。 對災害復原單位中的生產執行個體而言，目標磁碟區與生產磁碟區的大小相同。 這些磁碟區會與災害復原網站中的 HANA 大型執行個體伺服器單位產生關聯。 下列磁碟區會從生產區域複寫至 DR 網站：

- /hana/data
- /hana/logbackups 
- /hana/shared (包括 /usr/sap)

因為從這些磁碟區進行還原時不需要 SAP HANA 交易記錄，所以不會複寫 /hana/log 磁碟區。 

所提供之災害復原功能的基礎是 HANA 大型執行個體基礎結構所提供的儲存體複寫功能。 儲存體端所使用的這項功能不會在存放磁碟區發生變更時，以非同步方式連續不斷地複寫變更。 相反地，這項機制要仰賴「系統會定期對這些磁碟區建立快照集」的事實。 已複寫的快照集和尚未複寫的新快照集之間的差異，接著會傳送至災害復原網站的目標磁碟區。  這些快照集會儲存在磁碟區上，而在災害復原容錯移轉的情況下，需要在這些磁碟區上進行還原。  

您必須在快照集之間的差異資料量變小之前，第一次將完整的磁碟區資料傳送出去。 因此，DR 網站中的磁碟區會包含生產網站中所執行之磁碟區快照集的每個人。 此事實可讓您最終使用該 DR 系統來取得先前的狀態，以復原遺失的資料，而又不必復原生產系統。

若您在生產環境中使用 HANA 系統複寫來作為高可用性功能，則只會複寫第 2 層 (或複本) 執行個體的磁碟區。 如果您在此單位中維護或關閉次要複本 (第 2 層) 伺服器單位或 SAP HANA 執行個體，此組態可能會導致儲存體複寫至 DR 網站的作業延遲。 

>[!IMPORTANT]
>和多層式 HANA 系統複寫一樣，當您使用 HANA 大型執行個體的災害復原功能時，關閉第 2 層 HANA 執行個體或伺服器單位會封鎖複寫到災害復原網站的作業。


>[!NOTE]
>HANA 大型執行個體儲存體複寫功能會鏡像處理及複寫儲存體快照集。 因此，如果您未依照本文件的備份章節所說的方式來執行儲存體快照集，系統就完全不會複寫到災害復原網站。 若要執行儲存體複寫到災害復原網站的作業，就必須先執行儲存體快照集。



## <a name="preparation-of-the-disaster-recovery-scenario"></a>災害復原案例的準備工作
我們假設您具有在生產 Azure 區域中的 HANA 大型執行個體上執行的生產系統。 在後續的文件中，我們假設 HANA 系統的 SID 為 "PRD"。 我們也會假設您具有在災害復原 Azure 區域中執行的 HANA 大型執行個體上執行的非生產系統。 在本文件中，我們假設其 SID 是 "TST"。 所以組態如下所示：

![DR 設定的開頭](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

如果伺服器執行個體尚未與額外的存放磁碟區組一起排序，則「SAP HANA on Azure 服務管理」會將一組額外的磁碟區作為生產複本的目標，連結至執行 TST HANA 執行個體所在的 HANA 大型執行個體單位。 基於這個目的，您需要提供生產 HANA 執行個體的 SID。 在「SAP HANA on Azure 服務管理」確認這些磁碟區的連結之後，您需要將這些磁碟區掛接到 HANA 大型執行個體單位。

![DR 設定的下一個步驟](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

下一個步驟是在災害復原 Azure 區域 (您在其中執行 TST HANA 執行個體) 中的 HANA 大型執行個體單位上安裝第二個 SAP HANA 執行個體。 新安裝的 SAP HANA 執行個體必須具有相同的 SID。 所建立的使用者必須具有與生產執行個體相同的 UID 和群組識別碼。 如果安裝成功，您必須：
- 在災害復原 Azure 區域中的 HANA 大型執行個體單位上停止新安裝的 SAP HANA 執行個體。
- 卸載這些 PRD 磁碟區並連絡「SAP HANA on Azure 服務管理」。 磁碟區無法一直掛接至此單位，因為它們在當作儲存體複寫目標時無法加以存取。  

![建立複寫之前的 DR 設定步驟](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

營運小組將會建立生產 Azure 區域中的 PRD 磁碟區與災害復原 Azure 區域中的 PRD 磁碟區之間的複寫關聯性。

>[!IMPORTANT]
>不會複寫 /hana/log 磁碟區，因為不必將所複寫的 SAP HANA 資料庫還原到災害復原網站中的一致狀態。

下一個步驟是設定或調整儲存體快照集備份排程，以在災害情況下取得您的 RTO 和 RPO。 若要盡量縮減復原點目標，請在 HANA 大型執行個體服務中設定下列複寫間隔：
- 合併的快照集 (快照集類型 = **hana**) 所涵蓋的磁碟區每隔 15 分鐘就會複寫到災害復原網站中對等的存放磁碟區目標。
- 交易記錄備份磁碟區 (快照集類型 = **logs**) 每隔 3 分鐘就會複寫到災害復原網站中對等的存放磁碟區目標。

若要盡量縮減復原點目標，請設定下列各項：
- 每隔 30 分鐘到 1 小時執行一次 **hana** 類型的儲存體快照集 (請參閱「步驟 7：執行快照集」)。
- 每隔 5 分鐘執行一次 SAP HANA 交易記錄備份。
- 每隔 5-15 分鐘執行一次 **logs** 類型的儲存體快照集。 使用此間隔期間，您應該就能達到約 15-25 分鐘的 RPO。

若您使用此設定，HANA 交易記錄備份磁碟區、/hana/data 和 /hana/shared (包括 /usr/sap) 的交易記錄備份、儲存體快照集和複寫的順序會如下圖所示：

 ![時間軸上交易記錄備份快照集與快照鏡像之間的關聯性](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

若要在災害復原案例中達成更好的 RPO，您可以從 SAP HANA on Azure (大型執行個體) 將 HANA 交易記錄備份複製到其他 Azure 區域。 若要再縮減此 RPO，請執行下列概略步驟：

1. 儘可能經常將 HANA 交易記錄複製到 /hana/logbackups。
2. 使用 rsync 將交易記錄備份複製到 NFS 共用裝載的 Azure 虛擬機器。 VM 位於 Azure 生產區域和 DR 區域中的 Azure 虛擬網路。 您需要將這兩個 Azure 虛擬網路連線到可將生產 HANA 大型執行個體連線到 Azure 的線路。 請參閱[使用 HANA 大型執行個體進行災害復原的網路考量](#Network-considerations-for-disaster-recovery-with-HANA-Large-Instances)一節中的圖形。 
3. 將交易記錄備份保留在該區域內 VM 所連結的 NFS 匯出儲存體中。
4. 在災害容錯移轉案例中，為您在 /hana/logbackups 磁碟區所找到的交易記錄備份，補充您在災害復原網站的 NFS 共用上所擷取的較新交易記錄備份。 
5. 現在，您可以開始將交易記錄備份還原至可能儲存到 DR 區域的最新備份。

當 HANA 大型執行個體營運小組確認已設定複寫關聯性並開始執行儲存體快照集備份時，資料就會開始進行複寫。

![建立複寫之前的 DR 設定步驟](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

進行複寫時，不會還原災害復原 Azure 區域中 PRD 磁碟區上的快照集。 只會儲存這些快照集。 如果磁碟區是在此種狀態下掛接，則代表在 PRD SAP HANA 執行個體安裝於災害復原 Azure 區域的伺服器單位之後，您卸載這些磁碟區的狀態。 它們也代表尚未還原的儲存體備份。

在容錯移轉時，您也可以選擇還原到較舊的儲存體快照集，而不是最新的儲存體快照集。

## <a name="disaster-recovery-failover-procedure"></a>災害復原容錯移轉程序
如果您想要或需要容錯移轉至 DR 網站，您必須與 SAP HANA on Azure 營運小組互動。 就目前來說，此程序的步驟大致如下：

1. 因為您會在 HANA 大型執行個體的災害復原單位上執行非生產的 HANA 執行個體，所以必須關閉此執行個體。 我們會假設您已預先安裝休眠中的 HANA 生產執行個體。
2. 確定沒有任何 SAP HANA 程序處於執行狀態。 您可使用下列命令進行這項檢查：`/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`。 您應該會從輸出中看到 **hdbdaemon** 程序處於已停止狀態，而且已沒有其他 HANA 程序處於執行中或已啟動狀態。
3. 決定要讓災害復原網站還原到哪個快照集名稱或 SAP HANA 備份識別碼。 在真實的災害復原案例中，此快照集通常會是最新的快照集。 如果您需要復原遺失的資料，請挑選較早的快照集。
4. 請透過高優先順序的支援要求來與 Azure 支援服務連絡，並要求在 DR 網站上還原該快照集 (快照集的名稱和日期) 或 HANA 備份識別碼。 預設值是營運小組只會還原 /hana/data 磁碟區。 如果您也想要有 / hana/logbackups 磁碟區，您需要特別指明。 *我們不建議您還原 /hana/shared 磁碟區。* 相反地，在您針對 PRD 重新掛接 /hana/shared 之後，您應該挑選特定的檔案，例如 **.snapshot** 目錄及其子目錄中的 global.ini。 營運小組端將會進行下列步驟：a. 停止將快照集從生產磁碟區複寫至災害復原磁碟區。 如果生產網站中斷是您需要 DR 的原因，則可能已經發生這種情形。
    b. 此儲存體快照集名稱或具有您所選備份識別碼的快照集會在災害復原磁碟區上還原。
    c. 還原後，災害復原磁碟區即可供掛接到災害復原區域中的 HANA 大型執行個體單位。
5. 將災害復原磁碟區掛接至災害復原網站中的 HANA 大型執行個體單位。 
6. 啟動到目前為止處於休眠狀態的 SAP HANA 生產執行個體。
7. 如果您另外選擇複製交易記錄備份的記錄以降低 RPO 時間，您必須將這些交易記錄備份合併到新掛接之 DR 的 /hana/logbackups 目錄。 請勿覆寫現有的備份。 只要複製尚未隨著儲存體快照集的最新複寫一起複寫的較新備份。
8. 您也可以從尚未複寫到災害復原 Azure 區域中 /hana/shared/PRD 磁碟區的快照集，還原單一檔案。

後續步驟涉及根據所還原的儲存體快照集和可用的交易記錄備份來復原 SAP HANA 生產執行個體。 這些步驟如下：

1. 使用 SAP HANA Studio，將備份位置變更為 **/hana/logbackups**。
   ![變更 DR 復原的備份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA 會掃描所有備份檔案位置，並建議可作為還原目的地的最新交易記錄備份。 掃描可能需要幾分鐘的時間，完成後會出現如下所示的畫面：![DR 復原的交易記錄備份清單](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. 調整某些預設設定：

      - 清除 [使用差異備份]。
      - 選取 [初始化記錄區域]。

   ![設定初始化記錄區域](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. 選取 [完成]。

   ![完成 DR 還原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

此時應該會出現進度視窗，如下所示。 請記住，這是具有 3 節點相應放大 SAP HANA 組態的災害復原還原範例。

![還原進度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果還原似乎卡在 [完成] 畫面，而且未顯示進度畫面，請確認背景工作節點上的所有 SAP HANA 執行個體是否正在執行。 如有必要，請手動啟動 SAP HANA 執行個體。


### <a name="failback-from-dr-to-a-production-site"></a>從 DR 容錯回復到生產網站
您可以從 DR 容錯回復到生產網站。 讓我們看看以下情況：因為生產 Azure 區域發生問題而容錯移轉至災害復原網站，而不是因為您需要復原遺失的資料。 這表示您已在災害復原網站中執行您的 SAP 生產工作負載一段時間。 由於生產網站中的問題已經解決，因此您想容錯回復到生產網站。 因為您不能遺失資料，所以退回生產網站的作業會涉及許多步驟，並且要與 SAP HANA on Azure 營運小組密切合作。 在問題獲得解決之後，由您決定是否觸發營運小組來開始反向同步處理到生產網站的程序。

這些步驟如下所示：

1. SAP HANA on Azure 營運小組取得觸發程序，以從災害復原存放磁碟區 (目前代表生產狀態) 同步處理生產存放磁碟區。 處於此狀態時，生產網站中的 HANA 大型執行個體單位會關閉。
2. SAP HANA on Azure 營運小組會監視複寫程序，並確保能趕上通知您的時間。
3. 您會關閉使用災害復原網站中生產 HANA 執行個體的應用程式。 接著執行 SAP HANA 交易記錄備份。 然後將在災害復原網站中 HANA 大型執行個體單位上執行的 HANA 執行個體停止。
4. 在災害復原網站中 HANA 大型執行個體單位上執行的 HANA 執行個體關閉之後，營運小組必須再次手動同步處理磁碟區。
5. SAP HANA on Azure 營運小組會再次啟動生產網站中的 HANA 大型執行個體單位，然後將它轉交給您。 請確定在您啟動 HANA 大型執行個體單位時，SAP HANA 執行個體是處於關閉狀態。
6. 您可執行和先前容錯移轉至災害復原網站時所執行的相同資料庫還原步驟。

### <a name="monitoring-disaster-recovery-replication"></a>監視災害復原複寫

您可藉由執行`azure_hana_replication_status.pl` 指令碼來監視儲存體複寫進度的狀態。 此指令碼必須從正在災害復原位置中執行的單位來執行。 否則不會如預期般運作。 無論複寫是否為作用中，指令碼都有作用。 您可以對災害復原位置中的租用戶所具有的每個 HANA 大型執行個體單位執行這個指令碼。 但不能將它用來取得開機磁碟區的詳細資料。

依下列方式呼叫指令碼：
```
./replication_status.pl <HANA SID>
```

輸出會依磁碟區細分成下列各區段：  

- 連結狀態
- 目前的複寫活動
- 所複寫的最新快照集 
- 最新快照集的大小
- 目前在快照集間的延隔時間--最後一次完成的快照式複寫與現在時刻  

連結狀態會顯示為 [作用中] (除非位置之間的連結已關閉)，或是顯示為目前正在進行容錯移轉事件。 複寫活動會指出資料目前正在複寫、處於閒置，或者連結目前是否正發生其他活動。 最後複寫的快照集應該只會顯示為 `snapmirror…`。 然後會顯示最後一個快照集的大小。 最後則會顯示延隔時間。 延隔時間代表從排程的複寫時間到複寫完成時的時間。 資料複寫的延隔時間可能會超過一個小時，特別是初始的複寫，即使複寫已啟動亦然。 延隔時間會持續增加，直到進行中的複寫完成。

輸出範例如下所示：

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```













