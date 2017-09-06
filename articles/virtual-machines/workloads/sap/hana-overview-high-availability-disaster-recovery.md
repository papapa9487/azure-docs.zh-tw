---
title: "SAP HANA on Azure (大型執行個體) 的高可用性和災害復原 | Microsoft Docs"
description: "建立高可用性並為 SAP HANA on Azure (大型執行個體) 的災害復原做規劃。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 4356e35609fa7a9727d8ad4f20fd18df01a05e84
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP Hana (大型執行個體) 在 Azure 上的高可用性和災害復原 

高可用性和災害復原對執行具任務關鍵性的 SAP HANA on Azure (大型執行個體) 伺服器來說，是相當重要的層面。 請務必與 SAP、您的系統整合者或 Microsoft 合作，以便正確建構及實作正確的高可用性/災害復原策略。 也務必考量您環境特有的復原點目標和復原時間目標。

Microsoft 可透過 HANA 大型執行個體立即支援援某些 SAP HANA 高可用性方法。 其中包含：

- **儲存體複寫：**儲存體系統將所有資料複寫到另一個 Azure 區域中另一個 HANA 大型執行個體戳記的能力。 SAP HANA 獨立運作，不依賴此方法。
- **HANA 系統複寫︰**將 SAP HANA 中的所有資料複寫到個別的 SAP HANA 系統。 復原時間目標是透過定期的資料複寫而最小化。 SAP HANA 支援非同步、記憶體內同步及同步模式 (僅針對位於相同資料中心內或距離 100 公里內的 SAP HANA 系統建議使用)。 以 HANA 大型執行個體戳記目前的設計而言，HANA 系統複寫只可用於高可用性。 根據目前的網路設計，HANA 系統複寫無法在沒有第三方反向 Proxy 元件的情況下，用來在另一個 Azure 區域中進行災害復原設定。 
- **主機自動容錯移轉︰**SAP HANA 的本機錯誤復原解決方案，可供作為 HANA 系統複寫的替代選項。 當主要節點變成無法使用時，有一或多個待命 SAP HANA 節點會以相應放大模式設定，而 SAP HANA 會自動容錯移轉到待命節點。

SAP HANA on Azure (大型執行個體) 會在兩個 Azure 區域提供，以地緣政治來說，則是三個不同的區域 (美國、澳洲和歐洲)。 裝載 HANA 大型執行個體戳記的兩個不同區域會分別與各自專用的網路電路連線，以便複寫儲存體快照集來提供災害復原方法。 系統依預設不會建立複寫。 訂購了災害復原功能的客戶才會設定複寫作業。 執行儲存體複寫需要使用 HANA 大型執行個體的儲存體快照集。 您也無法選擇 Azure 區域來作為 DR 區域，因為該區域位於不同的地緣政治地區。 

下表顯示目前支援的高可用性和災害復原方法以及兩者的組合：

| HANA 大型執行個體所支援的案例 | 高可用性選項 | 災害復原選項 | 註解 |
| --- | --- | --- | --- |
| 單一節點 | 無法使用 | 專用 DR 設定<br /> 多用途 DR 設定 | |
| 主機自動容錯移轉：N + m<br /> 包括 1 + 1 | 可透過擔任作用中角色的待命節點來實現<br /> HANA 會控制角色的切換 | 專用 DR 設定<br /> 多用途 DR 設定<br /> 使用儲存體複寫來進行的 DR 同步處理 | HANA 磁碟區組會連結至所有節點 (n + m)<br /> DR 網站必須具有相同數目的節點 |
| HANA 系統複寫 | 可透過主要/次要設定來實現<br /> 在容錯移轉的情況下，次要節點會轉而成為主要節點<br /> HANA 系統複寫和 OS 控制容錯移轉 | 專用 DR 設定<br /> 多用途 DR 設定<br /> 使用儲存體複寫來進行的 DR 同步處理<br /> 若沒有第三方元件，則還無法實現使用 HANA 系統複寫來進行的 DR | 另一組不同的磁碟區會連結至每個節點<br /> 只有生產網站中次要複本的磁碟區會複寫到 DR 位置<br /> DR 網站需要一組磁碟區 | 

作為專用 DR 設定時，我們會將設定描繪成「DR 網站中的 HANA 大型執行個體單位不會用於執行任何其他工作負載或非生產系統」。 該單位是被動的，之所以部署只是為了在災害容錯移轉執行時能立即採取行動。 到目前為止，我們還沒有任何客戶採用這種組態。

作為多用途 DR 設定時，我們會將 DR 網站上的設定描繪成「HANA 大型執行個體單位會執行非生產工作負載」。 在非生產系統會關閉的災害情況下，儲存體複寫的 (其他) 磁碟區組會掛接上去，而且生產 HANA 執行個體會啟動。 到目前為止，所有使用 HANA 大型執行個體災害復原功能的客戶都使用此替代組態。 


您可以在下列 SAP 文章中找到更多關於 SAP HANA 高可用性的資訊： 

- [SAP HANA 高可用性白皮書](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理指南](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP Academy 的 SAP HANA 系統複寫影片](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 支援附註 #1999880 - SAP HANA 系統複寫常見問題集](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP 支援附註 #2165547 - SAP HANA 系統複寫環境內的 SAP HANA 備份與還原](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 支援附註 #1984882 - 使用 SAP HANA 系統複寫以最短/零停機時間的方式進行硬體交換](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>使用 HANA 大型執行個體之災害復原的網路考量

若要利用 HANA 大型執行個體的災害復原功能，您必須開始設計連到兩個不同 Azure 區域的網路連線。 在做法上，您需要一條從主要 Azure 區域中的內部部署連接的 ExpressRoute 線路，還需要另一條從內部部署連接到災害復原區域的線路。 此措施可解決整個 Azure 區域 (包含 Microsoft Enterprise Edge (MSEE) 路由器位置) 發生問題的情況。

作為第二個措施，您可以將連線到其中一個區域中 SAP HANA on Azure (大型執行個體) 的所有 Azure VNet，都連線到與其他區域中的 HANA 大型執行個體連線的 ExpressRoute 線路。 透過該「交叉連線」，於區域 #1 的 Azure Vnet 中執行的服務可以連線到區域 #2 的 HANA 大型執行個體單位，反之亦然。 此措施可解決將內部部署位置與 Azure 連接的 MSEE 位置之中，只有一個停止運作的情況。

下圖說明災害復原的這種復原組態：

![災害復原的最佳組態](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-for-using-hana-large-instances-storage-replication-for-disaster-recovery"></a>在災害復原中使用 HANA 大型執行個體儲存體複寫的其他需求

使用 HANA 大型執行個體之災害復原設定的其他需求如下：

- 您必須訂購與生產 SKU 大小相同的 SAP HANA on Azure (大型執行個體) SKU，並部署在災害復原區域中。 到目前為止，客戶的部署都使用這些執行個體來執行非生產 HANA 執行個體。 我們將其稱為「多用途 DR 設定」。   
- 如有必要，針對您在災害復原網站中想要復原的每個 SAP HANA on Azure (大型執行個體) SKU，您都必須在 DR 網站上為其訂購其他儲存體。 這個動作會導致配置存放磁碟區，這些磁碟區是從您生產 Azure 區域將儲存體複寫到災害復原 Azure 區域時的目標。

在本文件的最後一章之後，會有更多關於災害復原的詳細資料。
 

## <a name="backup-and-restore"></a>備份與還原

操作資料庫的其中一個最重要層面，就是確保可以保護資料庫免於各種災難事件的影響。 這些災害可能由任何原因造成，從天然災害到簡單的使用者錯誤都有可能。

備份資料庫時，若提供還原到任何時間點 (例如在某人刪除重要資料之前) 的功能，便可允許還原到儘可能接近發生中斷之前的狀態。

必須執行兩種類型的備份，才能獲得最佳結果：

- 資料庫備份 - 完整、增量或差異備份
- 交易記錄備份

除了在應用程式層級執行的完整資料庫備份之外，您可以透過以儲存體快照執行備份的方式來進行更徹底的備份。 不過，儲存體快照集不會取代交易記錄備份。 對於「將資料庫還原到特定時間點」或是「從已經認可的交易清空記錄」來說，交易記錄備份仍然很重要。 但是，儲存體快照集可加快復原速度，因為它們可以快速地提供資料庫的向前復原映像。 

SAP HANA on Azure (大型執行個體) 提供兩個備份和還原選項：

- 自己動手做 (DIY)。 在經過計算以確保磁碟空間足夠後，藉由使用磁碟備份方法來執行完整的資料庫和記錄備份，以直接備份到與 Azure VM 中所設定之 NFS 共用的 HANA 大型執行個體單位連結的磁碟區。 在後面一種情況下，客戶會在 Azure 中設定 Linux VM、將 Azure 儲存體連結至 VM，並透過該 VM 中所設定的 NFS 伺服器來共用儲存體。 如果對直接連結至 HANA 大型執行個體單位的磁碟區執行備份，就必須將備份複製到 Azure 儲存體帳戶 (在設定 Azure VM 以根據 Azure 儲存體匯出 NFS 共用後)，或使用 Azure 備份保存庫或 Azure 冷儲存體。 另一個選項是在備份複製到 Azure 儲存體帳戶之後，使用第三方資料保護工具來儲存備份。 針對因合規性和稽核目的而需要較長儲存期的資料，DIY 備份選項可能也有其必要性。 不論是哪種情況，備份都會複製到透過 VM 與 Azure 儲存體所表示的 NFS 共用中。
- 使用 SAP HANA on Azure (大型執行個體) 的底層基礎結構所提供的備份和還原功能。 此選項可滿足備份和快速還原的需要。 本節的其餘部分討論 HANA 大型執行個體隨附的這項備份與還原功能。 另外也會討論此功能與 HANA 大型執行個體所提供之災害復原功能的關聯性。

> [!NOTE]
> HANA 大型執行個體的底層基礎結構所使用的快照集技術相依於 SAP HANA 快照集。 到目前為止，SAP HANA 快照集還無法搭配 SAP HANA 多租用戶資料庫容器的多個租用戶一起使用。 因此，當您在 SAP HANA 多租用戶資料庫容器中部署多個租用戶時，將無法使用這種備份方法。 不過，如果是只部署一個租用戶的情況則可使用。

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 SAP HANA on Azure (大型執行個體) 的儲存體快照集

SAP HANA on Azure (大型執行個體) 底下的儲存體基礎結構支援磁碟區儲存體快照集的概念。 不論是備份還是還原磁碟區都受到支援，但有下列考量事項：

- 系統會經常建立存放磁碟區快照，而不是進行完整的資料庫備份。
- 針對 /hana/data、hana/log 和 /hana/shared (包括 /usr/sap) 磁碟區來觸發快照集時，儲存體快照集會先起始 SAP HANA 快照集，再執行儲存體快照集。 在儲存體快照集復原之後，此 SAP HANA 快照集是最後記錄還原的設定點。
- 在順利執行儲存體快照集後，系統會刪除 SAP HANA 快照集。
- 系統會經常建立交易記錄備份並儲存在 /hana/logbackups 磁碟區或 Azure 中。 您可以觸發含有交易記錄備份的 /hana/logbackups 磁碟區來對它個別擷取快照集。 在此情況下，不需要執行任何 HANA 快照集。
- 如果資料庫必須還原到特定的時間點，系統就會對「Microsoft Azure 支援」(生產環境中斷) 或「SAP HANA on Azure 服務管理」發出要求，以還原到特定的儲存體快照集 (例如，將沙箱系統還原到其原始狀態的計劃性還原)。
- 儲存體快照集所包含的 SAP HANA 快照集是一個位移點，用來套用在儲存體快照集建立後已執行和儲存的交易記錄備份。
- 建立這些交易記錄備份的目的是要將資料庫還原回特定的時間點。

身為客戶，您可以執行以三種不同的磁碟區類別為目標的儲存體快照集：

- 將針對 /hana/data、/hana/log 和 /hana/shared (包括 /usr/sap) 的快照集全部合併在一起。 此快照集需要執行 SAP HANA 快照集
- 針對 /hana/logbackups 的個別快照集
- OS 分割區 (只適用於 Type I 的 HANA 大型執行個體)


### <a name="storage-snapshot-considerations"></a>儲存體快照考量事項

>[!NOTE]
>儲存體快照集會取用已配置給 HANA 大型執行個體單位的儲存空間。 因此，您必須考慮以下有關排程儲存體快照集和保留儲存體快照集數目等方面的問題。 

SAP HANA on Azure (大型執行個體) 儲存體快照集的獨特技巧包括：

- 特定的儲存體快照集 (在建立快照集的時間點) 只會取用少量儲存體。
- 隨著在存放磁碟區上資料內容發生變更及 SAP HANA 資料檔中的內容發生變更，快照便需要儲存原始區塊內容。
- 儲存體快照的大小會增加。 快照存在的時間越長，儲存體快照就會變得越大。
- 在儲存體快照集的生命週期中對 SAP HANA 資料庫磁碟區進行的變更越多，儲存體快照集所耗用的空間就越大。

SAP HANA on Azure (大型執行個體) 隨附固定的磁碟區大小供 SAP HANA 資料和記錄磁碟區使用。 執行這些磁碟區的快照集會耗用磁碟區空間。 因此，您必須負責排程儲存體快照集、監視存放磁碟區的空間耗用量，並管理所儲存的快照集數目。 只有您可以決定為了匯入大量資料或對 HANA 資料庫執行其他重大變更，而在最終停用儲存體快照集。 


下列各節提供執行這些快照集的資訊，包括一般建議事項：

- 雖然硬體的每一磁碟區都可支援 255 個快照集，但強烈建議您讓快照集數目保持遠低於這個數字。
- 執行儲存體快照集之前，請監視並追蹤可用空間。
- 根據可用空間來降低儲存體快照的數目。 您可能需要降低保留的快照集數目，也可能需要擴充磁碟區  (您可以訂購額外的儲存空間，以 1 TB 為單位)。
- 在「使用 SAP 平台移轉工具 (R3load) 將資料移到 SAP HANA」或是「從備份還原 SAP HANA 資料庫」等活動進行期間，您必須在 /hana/data 磁碟區上停用儲存體快照集。 
- 在進行較大規模的 SAP HANA 資料表重組期間，應儘可能避免執行儲存體快照。
- 儲存體快照集也是使用 SAP HANA on Azure (大型執行個體) 之災害復原功能的必要條件。

### <a name="setting-up-storage-snapshots"></a>設定儲存體快照

使用 HANA 大型執行個體來設定儲存體快照集的概略步驟如下：
1. 確定 HANA (大型執行個體) 伺服器上的 Linux 作業系統中已安裝 Perl。
2. 修改 /etc/ssh/ssh\_config 以新增 _MACs hmac-sha1_ 這一行。
3. 針對您目前執行的每個 SAP HANA 執行個體，在主要節點上建立 SAP HANA 備份使用者帳戶 (如果適用)。
4. 所有 SAP HANA (大型執行個體) 伺服器上都必須安裝 SAP HANA HDB 用戶端。
5. 在每個區域的第一部 SAP HANA (大型執行個體) 伺服器上都必須建立一個公開金鑰，以便存取其底下控制快照集建立的儲存體基礎結構。
6. 將指令碼和組態檔從[這個 GitHub 位置](https://github.com/Azure/hana-large-instances-self-service-scripts)複製到 SAP HANA 安裝的 **hdbsql** 位置。
7. 視需要修改 HANABackupDetails.txt 檔案以符合適當的客戶規格。

### <a name="step-1-install-sap-hana-hdbclient"></a>步驟 1：安裝 SAP HANA HDBClient

安裝在 SAP HANA on Azure (大型執行個體) 上的 Linux 作業系統包含針對備份和災害復原目的執行 SAP HANA 儲存體快照集時所需的資料夾與指令碼。 檢查 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 中是否有更近期的版本。 最新的指令碼版本為 2.0。
不過，安裝 SAP HANA 時，是由您負責在 HANA 大型執行個體單位上安裝 SAP HANA HDBclient  (Microsoft 不會安裝 HDBclient 或 SAP HANA)。

### <a name="step-2-change-etcsshsshconfig"></a>步驟 2：變更 /etc/ssh/ssh\_config

在 /etc/ssh/ssh\_config 中新增 _MACs hmac-sha1_ 這一行，如下所示：
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

若要能夠存取 HANA 大型執行個體租用戶的儲存體快照集介面，您必須透過公開金鑰建立登入。 在租用戶的第一部 SAP HANA on Azure (大型執行個體) 伺服器上，建立用來存取儲存體基礎結構的公開金鑰，讓您能夠建立快照集。 此公開金鑰可確保不需密碼即可登入儲存體快照集介面，且不需維護密碼認證。 在 SAP HANA (大型執行個體) 伺服器上的 Linux 中，請執行下列命令來產生公開金鑰：
```
  ssh-keygen –t dsa –b 1024
```
新的位置是 _/root/.ssh/id\_dsa.pub。 請勿輸入實際的密碼，否則您每次登入時都需要輸入密碼。 改為按 **Enter** 兩次，在登入時就不需要輸入密碼。

將資料夾切換至 /root/.ssh/，然後執行 **ls** 命令，以確定已如預期地更正公開金鑰。 如果金鑰存在，您可以執行下列命令來複製它︰

![執行此命令即可複製公開金鑰](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

此時，請連絡「SAP HANA on Azure 服務管理」並提供公開金鑰。 服務代表會使用公開金鑰，將它註冊在針對 HANA 大型執行個體租用戶所開闢的底層儲存體基礎結構中。

### <a name="step-4-create-an-sap-hana-user-account"></a>步驟 4：建立 SAP HANA 使用者帳戶

若要能夠開始建立 SAP HANA 快照集，您必須在 SAP HANA 中建立使用者，以供儲存體快照集指令碼使用。 針對這些用途在 SAP HANA Studio 中建立 SAP HANA 使用者帳戶。 此帳戶必須具備下列權限：「備份管理」和「目錄讀取」。 在此範例中，建立的使用者名稱是 SCADMIN。 HANA Studio 中所建立的使用者帳戶名稱會區分大小寫。  請務必在「要求使用者於下一次登入時變更密碼」按一下 [否]。

![在 HANA Studio 中建立使用者](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>步驟 5：授權 SAP HANA 使用者帳戶

在此步驟中，您必須針對您所建立的 SAP HANA 使用者帳戶進行授權，讓指令碼不必在執行階段提交密碼。 SAP HANA 命令 `hdbuserstore` 能讓您建立儲存在一或多個 SAP HANA 節點上的 SAP HANA 使用者金鑰。 使用者金鑰可讓使用者存取 SAP HANA，而不需要從指令碼程序內管理密碼 (稍後討論)。

>[!IMPORTANT]
>以 `root` 身分執行下列命令。 否則，指令碼無法正常運作。

輸入 `hdbuserstore` 命令，如下所示︰

![輸入 hdbuserstore 命令](./media/hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

在使用者為 SCADMIN01 而主機名稱為 lhanad01 且執行個體號碼為 01 的以下範例中，命令是：
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
如果您有 SAP HANA 相應放大組態，請從單一伺服器管理所有指令碼。 在此範例中，必須針對每個主機改變 SAP HANA 金鑰 SCADMIN01，以反映與金鑰相關的主機。 也就是說，以 HANA DB 的執行個體數目修訂 SAP HANA 備份帳戶。 此金鑰必須具備所被指派主機上的系統管理權限，而相應放大的備份使用者則必須具備所有 SAP HANA 執行個體的存取權限。 假設有三個名稱分別為 lhanad01、lhanad02 和 lhanad03 的相應放大節點，命令順序會像這樣：

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad02:30215 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad03:30315 SCADMIN <password>
```

### <a name="step-6-get-snapshot-scripts-configure-snapshots-and-test-configuration-and-connectivity"></a>步驟 6：取得快照集指令碼、設定快照集並測試組態與連線能力

從[這裡](https://github.com/Azure/hana-large-instances-self-service-scripts)下載最新版本的指令碼。 將下載的指令碼和文字檔複製到 **hdbsql** 的工作目錄。 就目前的 HANA 安裝而言，這個目錄就像 /hana/shared/D01/exe/linuxx86\_64/hdb。
```
azure\_hana\_backup.pl
azure\_hana\_replication\_status.pl
azure\_hana\_snapshot\_details.pl
azure\_hana\_snapshot\_delelte.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```

不同指令碼和檔案的用途如下：

- **azure\_hana\_backup.pl**：這個指令碼必須使用 cron 來排程，讓儲存體快照集在 HANA 資料/記錄/共用磁碟區、/hana/logbackups 磁碟區或作業系統 (適用於 HANA 大型執行個體的 Type I SKU) 上執行
- **azure\_hana\_replication\_status.pl**：此指令碼的設計目的，是要提供有關從生產網站到災害復原網站的複寫狀態基本詳細資料。  此指令碼可用來監視正在進行的複寫，並顯示正在複寫的項目大小。  它也可提供複寫時間太久或連結可能中斷時的指引。
- **azure\_hana\_snapshot\_details.pl**：此指令碼的目的，是要為客戶提供每個磁碟區在環境中現存之所有快照集的基本詳細資料清單。 此指令碼可在主要伺服器或在災害復原位置的伺服器單位上執行。 此指令碼會提供下列以包含快照集的每個磁碟區來細分的資訊：磁碟區中所有快照集的總計大小，然後再針對該磁碟區的每個快照集提供下列詳細資料：快照集名稱、建立時間、快照集大小、快照集頻率以及與該快照集相關聯的 HANA 備份識別碼 (如有關聯)。
- **azure\_hana\_snapshot\_delelte.pl**：此指令碼的設計目的，是要使用可在 HANA Studio 中找到的 SAP HANA 備份識別碼或以儲存體快照集名稱本身，來刪除儲存體快照集或一組快照集。  備份識別碼目前只會與針對 HANA 資料/記錄/共用磁碟區所建立的快照集繫結。  否則，如果您輸入快照集識別碼，則指令碼會搜尋所有與輸入的快照集識別碼相符的快照集。  
- **testHANAConnection.pl**：此指令碼會測試 SAP HANA 執行個體的連線，需要有此連線才能設定儲存體快照集。
- **testStorageSnapshotConnection.pl**：此指令碼有兩種用途。 第一種用途是確保執行指令碼的 HANA 大型執行個體單位可存取指派的儲存體虛擬機器，進而能夠存取 HANA 大型執行個體的儲存體快照集介面。 第二種用途是針對您要測試的 HANA 執行個體建立暫時性的快照集。 您應該對伺服器上的每個 HANA 執行個體執行此指令碼，以確保備份指令碼可如預期般運作。
- **removeTestStorageSnapshot.pl**：此指令碼用來刪除以 testStorageSnapshotConnection.pl 指令碼所建立的測試快照集。 
- **HANABackupCustomerDetails.txt**：這是可修改的組態檔，請加以修改以適應您的 SAP HANA 組態

 
HANABackupCustomerDetails.txt 檔案是執行儲存體快照集之指令碼的控制及組態檔。 您必須調整該檔案以因應您的用途和設定。 當您的執行個體部署完成時，您應該會從「SAP HANA on Azure 服務管理」收到「儲存體備份名稱」和「儲存體 IP 位址」。 您不能修改此檔案中任何變數的順序、排序或間距。 否則指令碼將無法正常執行。 此外，您會從 Azure 服務管理上的 SAP HANA 收到相應放大節點或主要節點 (如果相應放大) 的 IP 位址。 安裝好 SAP HANA 之後，您也會知道 HANA 執行個體號碼，然後您必須在組態檔中新增備份名稱

若為相應增加或相應放大部署，在您填入儲存體備份名稱和儲存體 IP 位址之後，組態檔的樣貌如下。 您必須在組態檔中另外填入的資料有單一節點或主要節點的 IP 位址、 HANA 執行個體號碼，以及您可以選擇的備份名稱：
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA Backup Name:
```

>[!NOTE]
>目前，實際 HANA 儲存快照集指令碼中只使用節點 1 詳細資料。 我們建議您往返所有 HANA 節點來測試存取，以便如果主要備份節點變更，您就已確定任何其他節點可以修改節點 1 中的詳細資料，以接替此節點。

將所有組態資料放入 HANABackupCustomerDetails.txt 檔案後，您必須使用 testHANAConnection.pl 指令碼檢查有關 HANA 執行個體資料的組態是否正確。 此指令碼與 SAP HANA 相應增加或相應放大組態無關

```
testHANAConnection.pl
```

如果您有 SAP HANA 相應放大組態，請確定主要的 HANA 執行個體能夠存取所有必要的 HANA 伺服器和執行個體。 測試指令碼並無參數，但您必須將資料新增至 HANABackupCustomerDetails.txt 組態檔，指令碼才能正常執行。 因為只傳回殼層命令錯誤碼，所以指令碼無法檢查每個執行個體的錯誤。 即便如此，指令碼會提供一些實用的意見讓您再次檢查。

執行指令碼：
```
 ./testHANAConnection.pl
```
如果指令碼成功取得 HANA 執行個體的狀態，則會顯示 HANA 連接成功的訊息。


下一個測試步驟是根據您放入 HANABackupCustomerDetails.txt 組態檔的資料來檢查儲存體的連線，以及執行測試快照集。  在執行 azure\_hana\_backup.pl 指令碼之前，您必須先執行此指令碼。 如果磁碟區不含快照集，則無法判斷原因是磁碟區空白，還是 ssh 失敗而無法取得快照集詳細資料。 因此，指令碼會執行兩個步驟：

- 它會確認租用戶的儲存體虛擬機器和介面是否可供存取以便讓指令碼執行快照集。
- 依 HANA 執行個體，為每個磁碟區建立測試 (或虛設) 快照集。

基於這個理由，會包含 HANA 執行個體作為引數。 如果執行失敗，則無法提供儲存體連線錯誤檢查。 但指令碼會在這種情況下提供有用的提示。

指令碼的執行方式為：
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
在下一個步驟中，指令碼會嘗試使用之前在設定步驟中提供的公開金鑰和 HANABackupCustomerDetails.txt 中所設定的資料，來登入儲存體。 如果成功，則會顯示下列內容：

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

如果發生問題，請連線到儲存體主控台，其輸出會像這樣：

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

成功登入儲存體虛擬機器介面之後，指令碼會繼續進行階段 #2，並為 SAP HANA 的三節點相應放大組態建立如下所示的測試快照集：

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

如果已成功使用此指令碼執行測試快照集，您可以繼續設定實際的儲存體快照集。 如果沒有成功，請於調查問題後再繼續進行。 所建立的測試快照集應先留著，直到您完成第一個實際快照集。



### <a name="step-7-perform-snapshots"></a>步驟 7：執行快照集

當所有準備步驟都完成後，您就可以開始設定實際的儲存體快照集組態。 要排程的指令碼可與 SAP HANA 相應增加和相應放大組態搭配運作。 其概念是您要透過 cron 來排定指令碼的執行。 

可以建立的快照集備份有三種：
- HANA：合併的快照集備份，在此備份中，包含 /hana/data、/hana/log 和 /hana/shared (也包含 /usr/sap) 的磁碟區會遭到協調式快照集覆蓋。 您可以從這個快照集還原單一檔案。
- 記錄︰/hana/logbackups 磁碟區的快照集備份。 不會觸發 HANA 快照集來執行此儲存體快照集。 此儲存體磁碟區是為了包含 SAP HANA 交易記錄備份而建立的磁碟區，其會提高執行頻率以限制記錄成長，並防止資料遺失。 您可以從這個快照集還原單一檔案。 請勿將執行頻率降低到 5 分鐘以下。
- 開機：包含 HANA 大型執行個體開機 LUN 之磁碟區的快照集。 您只能透過 HANA 大型執行個體的 Type I SKU 來建立此快照集備份。 您無法從包含開機 LUN 之磁碟區的快照集還原單一檔案。  


這三種不同快照集的呼叫語法如下：
```
HANA backup covering /hana/data, /hana/log, /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot manual 30

```

您必須指定下列參數︰

- 要指定的第一個參數會描繪快照集備份的類型。 允許的值有 'hana'、'logs' 和 'boot'
- 第二個值是 HANA SID (例如 HM3)。 不必使用這個參數就能備份開機磁碟區。
- 第三個參數是屬於快照集類型之備份標籤的快照集。 此參數應該會和特定類型或頻率之已排程快照集的參數相同。
- 第四個參數會藉由定義要保留且具有相同快照集首碼 (標籤) 的快照集數目，來間接定義快照集的保留期。 對於透過 cron 來進行的已排程執行作業來說，這個參數很重要。 

在相應放大情況中，指令碼會先執行一些額外的檢查，以確定所有 HANA 伺服器皆可供存取，且所有 HANA 執行個體皆傳回適當的執行個體狀態，然後才繼續先後建立 SAP HANA 快照集以及儲存體快照集。

執行 azure\_hana\_backup.pl 指令碼會以下列三個不同的階段建立儲存體快照：

- 執行 SAP HANA 快照集。
- 執行儲存體快照。
- 先移除已建立的 SAP HANA 快照集，再執行儲存體快照集。

從指令碼複製到 HDB 可執行檔資料夾中，經由呼叫來執行指令碼。 

保留期取決於您執行指令碼時以參數送出的快照集數目 (例如上面所示的 30)。 因此，儲存體快照集所涵蓋的時間量是由執行期間以及在執行指令碼時提交作為參數的快照集數目所構成的函式。 如果所保留的快照集數目超出指令碼呼叫中以參數指定的數目，將會先刪除相同標籤的最舊儲存體快照集 (在我們前面的案例中為 manual)，然後才執行新的快照集。 這意謂著您所提供作為最後呼叫參數的數字，就是您可用來控制要保留之快照集數目的數字。 藉此，您也可以間接控制快照集所使用的磁碟空間。 

>只要您一變更標籤，就會重新開始計算。 也就是說，您必須嚴守標記方式。

### <a name="snapshot-strategies"></a>快照集策略
針對不同類型的快照集，其頻率取決於您是否使用 HANA 大型執行個體災害復原功能。 由於 HANA 大型執行個體的災害復原功能會依賴儲存體快照集，因此我們對於儲存體快照集的頻率和執行期間有提供一些特殊建議。 在下面的考量和建議中，我們假設您不會使用 HANA 大型執行個體所提供的災害復原功能。 相反地，您會使用儲存體快照集來作為建立備份的方法，而且您能夠提供過去 30 天的時間點復原。 在快照集和空間的數量有限的情況下，進行部署的客戶考量了這些類型的需求：

- 時間點復原的復原時間目標中的需求。
- 空間使用量。
- 潛在災害復原的復原點目標和復原時間目標中的需求
- 對磁碟執行的最終 HANA 完整資料庫備份。 每次對磁碟執行完整資料庫備份，或執行 _backint_ 介面時，執行儲存體快照集都會失敗。 如果除了儲存體快照集之外，您還打算執行完整資料庫備份，請確定在此期間停用儲存體快照集的執行。
- 每個磁碟區的快照集數目只能有 255 個。


對於不使用 HANA 大型執行個體災害復原功能的客戶，快照集期間較不頻繁。 在這種情況下，我們發現客戶會以 12 小時或 24 小時的期間來對 /hana/data、/hana/log、/hana/shared (包括 /usr/sap) 執行合併的快照集，並保留這些快照集以涵蓋一整個月。 記錄備份磁碟區的快照集也是如此。 至於記錄備份磁碟區的 SAP HANA 交易記錄備份，則會以 5 到 15 分鐘的期間來。

建議您使用 cron 來執行排定的儲存體快照集，也建議您針對所有備份和災害復原需求使用相同的指令碼 (修改指令碼輸入以符合所要求的不同備份時間)。 這些快照集在 cron 中都會根據其執行時間以不同方式排定：每小時、12 小時、每天或每週。 

/etc/crontab 中的 cron 排程範例會像這樣：
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
在上述範例中，有每小時的合併快照集會涵蓋包含 /hana/data、/hana/log、/hana/shared (包括 /usr/sap) 位置的磁碟區。 我們會使用這種快照集來加快兩天內的時間點復原速度。 此外，這些磁碟區會有每日快照集。 因此，您會獲得涵蓋兩天的每小時快照集以及涵蓋四週的每日快照集。 此外，每天會備份一次交易記錄備份磁碟區。 這些備份也會保存四週。 正如您在 crontab 第三行所看到的，系統會排定每五分鐘備份一次 HANA 交易記錄。 您也會發現用來執行儲存體快照集的不同 cron 作業，其開始時間會錯開，以免系統在特定時間點一次執行所有快照集。 

在下面的範例中，您會每小時執行一次合併的快照集，其所涵蓋的磁碟區會包含 /hana/data、/hana/log、/hana/shared (包括 /usr/sap) 位置。 您會將這些快照集保留兩天。 交易記錄備份磁碟區的快照集會每隔五分鐘執行一次，並保留四個小時。 和之前一樣，系統會將 HANA 交易記錄檔排定為每五分鐘備份一次。 在開始交易記錄備份後，系統會延後兩分鐘再執行交易記錄備份磁碟區的快照集。 一般情況下，SAP HANA 交易記錄備份應該能在這兩分鐘的時間內完成。 和之前一樣，儲存體快照集每天會將包含開機 LUN 的磁碟區備份一次，並保留四週。

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

若以圖形來表示，上述範例 (不包括開機 LUN) 的順序會像這樣：

![備份與快照集之間的關聯性](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot.PNG)

SAP HANA 會定期對 /hana/log 磁碟區執行寫入作業，以將認可的變更記錄至資料庫。 SAP HANA 會定期將儲存點寫入 /hana/data 磁碟區。 SAP HANA 交易記錄備份會依照 crontab 的指定每五分鐘執行一次。 您也會看到系統會因為針對 /hana/data、hana/log 和 /hana/shared 磁碟區觸發合併的儲存體快照集，而每小時執行一次 SAP HANA 快照集。 HANA 快照集成功之後，就會執行合併的儲存體快照集。 系統會依照 crontab 的指示，每隔五分鐘在 HANA 交易記錄備份完成大約兩分鐘後，在 /hana/logbackup 磁碟區上執行儲存體快照集。


>[!IMPORTANT]
> 只有在快照集與 SAP HANA 交易記錄備份一起執行時，使用儲存體快照集來進行 SAP HANA 備份才有價值。 這些交易記錄備份必須要能夠涵蓋儲存體備份之間的時段。 

如果您已對使用者承諾執行 30 天的時間點復原，則需具備下列條件︰

- 能夠存取針對 /hana/data、/hana/log 和 /hana/shared 所建立的合併儲存體快照集，而且在極端情況下可達 30 天之久。
- 連續交易記錄備份，涵蓋任何合併儲存體快照之間的時間。 因此，最舊的交易記錄備份磁碟區快照集必須達 30 天之久。 除非您已同時將交易記錄備份複製到位於 Azure 儲存體上的另一個 NFS 共用。 在這種情況下，您就可以從該 NFS 共用提取舊的交易記錄備份。

為了能夠受益於儲存體快照集並最終受益於交易記錄備份的儲存體複寫，您必須變更 SAP HANA 用來寫入交易記錄備份的目的地位置。 您可以在 HANA Studio 中完成這項變更，如下所示。 雖然 SAP HANA 會自動備份完整的記錄區段，但您應該指定記錄備份間隔來加以確定。 特別是在使用災害復原選項時，您通常會想要以確定的期間來執行記錄備份。 在下面的案例中，我們採用 15 分鐘的記錄備份間隔。

![在 SAP HANA Studio 中排定 SAP HANA 備份記錄](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

您可以選擇比每隔 15 分鐘更頻繁的備份。 特別是在搭配災害復原時。 有些客戶會每隔五分鐘執行一次交易記錄備份。  

如果資料庫從未進行過備份，則最後一個步驟是執行檔案型備份，以建立必須存在於備份資料目錄內的單一備份項目。 否則，SAP HANA 無法起始您指定的記錄備份。

![建立檔案型備份以建立單一備份項目](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


在成功執行第一個儲存體快照集之後，您也可以刪除步驟 6 所執行的測試快照集。 若要這樣做，請執行 removeTestStorageSnapshot.pl 指令碼，如下所示：
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>監視磁碟區上的快照集數目和大小

在特定的存放磁碟區上，您可以監視快照集的數目和這些快照集的儲存體耗用量。 `ls` 命令不會顯示快照集目錄或檔案。 不過，Linux 作業系統命令 `du` 會顯示這些儲存體快照集自儲存到相同磁碟區後的詳細資料。 此命令可與下列選項搭配使用：

- `du –sh .snapshot` 提供快照集目錄內所有快照集的總計。
- `du –sh --max-depth=1` 列出所有儲存在 '.snapshot' 資料夾中的快照集及每個快照集的大小。
- `du –hc` 提供所有快照集使用的總大小。

您可以使用這些命令來確定所建立和儲存的快照集並未耗用磁碟區上的所有儲存體。

>[!NOTE]
>使用上述命令無法看到開機 LUN 的快照集。

### <a name="getting-details-of-snapshots"></a>取得快照集的詳細資料
若要取得更多關於快照集的詳細資料，您也可以使用 azure\_hana\_snapshot\_details.pl 指令碼。 如果災害復原位置有作用中的伺服器，則可以在任一位置執行此指令碼。 此指令碼會提供下列以包含快照集的每個磁碟區來細分的資訊：磁碟區中所有快照集的總計大小，然後再針對該磁碟區的每個快照集提供下列詳細資料：快照集名稱、建立時間、快照集大小、快照集頻率以及與該快照集相關聯的 HANA 備份識別碼 (如有關聯)。 指令碼的執行語法如下：

```
./azure_hana_snapshot_details.pl 
```

由於指令碼會嘗試擷取 HANA 備份識別碼，因此它需要連線到 SAP HANA 執行個體，進而又需要正確設定 HANABackupCustomerDetails.txt 組態檔。 磁碟區上兩個快照集的輸出如下：

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


### <a name="file-level-restore-from-storage-snapshot"></a>從儲存體快照集所進行的檔案層級還原
針對快照集類型 'hana' 和 'logs'，您可以直接在 '.snapshot' 目錄中的磁碟區上存取快照集。 您會發現每個快照集都有子目錄。 您應該能夠將處於快照集製作時間點之狀態的快照集所涵蓋的每個檔案，從該子目錄複製到實際的目錄結構中。

>[!NOTE]
>單一檔案還原不適用於開機 LUN 的快照集。 '.snapshot' 目錄不會在開機 LUN 中顯示出來。 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>縮減伺服器上的快照數目

如先前所述，您可以針對您儲存的快照集，減少某些標籤的數目。 用來起始快照之命令的最後兩個參數是一個標籤及您想要保留的快照數目。

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

在前一個範例中，快照集標籤是 customer，而此標籤內要保留的快照集數目則是 30。 在您因應磁碟空間耗用量時，可能會想要縮減所儲存的快照集數目。 若要將快照集數目減少為 15 (舉例來說)，有個簡單的方法是將最後一個參數設定為 15 來執行指令碼：

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

以這項設定執行指令碼之後，快照集數目 (包括新的儲存體快照集) 會是 15。 15 個最新的快照集會保留下來，15 個較舊的快照集則會予以刪除。

 >[!NOTE]
 > 有存在時間超過一小時的快照集時，此指令碼才會減少快照集數目。 指令碼不會刪除過去一小時內的快照集。 這些限制與所提供的選擇性災害復原功能有關。

如果您不想再維護一組含有特定備份標籤 (即語法範例中的 hanadaily) 的快照集，您可以使用 _0_ 作為保留數字來執行指令碼，以移除所有符合該標籤的快照集。 不過，移除所有快照集可能會影響災害復原的功能。

若要刪除特定快照集，第二種可能性是使用 azure\_hana\_snapshot\_delelte.pl 指令碼。 此指令碼的設計目的，是要使用可在 HANA Studio 中找到的 HANA 備份識別碼或以快照集名稱本身，來刪除單一快照集或一組快照集。  備份識別碼目前只會與針對 'hana' 快照集類型所建立的快照集繫結。 'logs' 和 'boot' 類型的快照集備份不會執行 SAP HANA 快照集。 因此，您不會找到這些快照集的備份識別碼。 如果您輸入快照集名稱，則指令碼會尋找不同磁碟區上符合所輸入快照集名稱的所有快照集。 此指令碼的呼叫語法為：

```
./azure_hana_snapshot_delete.pl 

```

您必須以 root 使用者身分執行指令碼

如果您選取某個快照集，則會有功能供您個別刪除每個快照集。 系統會先詢問您包含快照集的磁碟區，再問您實際的快照集名稱。 如果該磁碟區上有您需要的快照集，而且存在時間超過一小時，系統就會將它刪除。 您可以藉由執行 azure_hana_snapshot_details 指令碼來尋找磁碟區名稱和快照集名稱。 

>[!IMPORTANT]
>如果有資料只存在於您要刪除的快照集上，則執行刪除作業就表示該資料會永久遺失。

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>復原到最近的 HANA 快照集

當您遇到生產環境停止運作的情況時，可以向「Microsoft Azure 支援」以客戶事件的形式起始從儲存體快照集復原的程序。 如果生產系統中的資料遭到刪除，而挽救資料的唯一方法是還原生產資料庫，則這種非預期的情況是高緊急性的事件。

若是其他情況，則時間點復原會是低緊急性，可提前幾天來規劃。 您可以使用「SAP HANA on Azure 服務管理」來規劃此復原，以避免引發高優先性的問題。 例如，您可能打算套用新的增強套件來嘗試 SAP 軟體升級，然後需要還原回代表 EHP 升級前狀態的快照集。

發出要求之前，您需要做一些準備工作。 接著，「SAP HANA on Azure 服務管理」小組就可以處理該要求，並提供已還原的磁碟區。 之後，由您負責根據快照集還原 HANA 資料庫。 以下是在要求之前如何做好準備︰

>[!NOTE]
>視您使用的 SAP HANA 版本而定，您的使用者介面可能與下列螢幕擷取畫面不同。

1. 決定要還原哪一個快照。 除非另有指示，否則只會還原 hana/data 磁碟區。 

2. 關閉 HANA 執行個體。

 ![關閉 HANA 執行個體](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 將每個 HANA 資料庫節點上的資料磁碟區卸載。 如果資料磁碟區仍掛接至作業系統，快照集還原會失敗。
 ![將每個 HANA 資料庫節點上的資料磁碟區卸載](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. 提出 Azure 支援要求，以取得還原特定快照集的指示。

 - 還原期間︰「SAP HANA on Azure 服務管理」可能會要求您參加電話會議，以確保您會協調、驗證和確認所還原的儲存體快照集是正確的。 

 - 還原之後：還原儲存體快照集之後，「SAP HANA on Azure 服務管理」會通知您。

5. 在還原程序完成後，重新掛接所有資料磁碟區。

 ![重新掛接所有資料磁碟區](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. 當您透過 SAP HANA Studio 重新連接到 HANA DB 時，如果沒有自動出現復原選項，請選取 SAP HANA Studio 內的復原選項。 以下範例顯示還原到最後一個 HANA 快照集。 儲存體快照集內嵌一個 HANA 快照集，如果您要復原到最近的儲存體快照集，它應該是最近的 HANA 快照集。 (如果您要復原到較舊的儲存體快照，您就必須根據儲存體快照的建立時間找出 HANA 快照)。

 ![選取 SAP HANA Studio 內的復原選項](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. 選取 [將資料庫復原到特定資料備份或儲存體快照集]。

 ![[指定復原類型] 視窗](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. 選取 [指定不含目錄的備份]。

 ![[指定備份位置] 視窗](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. 在 [目的地類型] 清單中，選取 [快照集]。

 ![[指定要復原的備份] 視窗](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. 按一下 [完成] 以開始復原程序。

 ![按一下 [完成] 以開始復原程序](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA 資料庫會還原並復原到儲存體快照集所包含的 HANA 快照集。

 ![HANA 資料庫會還原並復原到 HANA 快照集](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>復原到最近狀態

下列程序會還原儲存體快照集所包含的 HANA 快照集。 然後，將交易記錄備份還原至資料庫在還原儲存體快照集之前的最近狀態。

>[!IMPORTANT]
>繼續進行之前，請先確定您擁有一串完整且連續的交易記錄備份。 如果沒有這些備份，您就無法還原資料庫的目前狀態。

1. 完成「復原到最近的 HANA 快照集 」中先前程序的步驟 1-6。

2. 選取 [將資料庫復原到最近狀態]。

 ![選取 [將資料庫復原到最近狀態]。](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. 指定最近 HANA 記錄備份的位置。 此位置必須包含從 HANA 快照集到最近狀態的所有 HANA 交易記錄備份。

 ![指定最近 HANA 記錄備份的位置](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. 選取一個備份作為基礎來從中復原資料庫。 在我們的範例中，螢幕擷取畫面中的 HANA 快照集是儲存體快照集所包含的 HANA 快照集。 

 ![選取一個備份作為基礎來從中復原資料庫](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. 如果 HANA 快照集快時間與最近狀態之間沒有差異，請清除 [使用差異備份] 核取方塊。

 ![如果沒有差異，請清除 [使用差異備份] 核取方塊](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. 在摘要畫面上，按一下 [完成] 以開始還原程序。

 ![在摘要頁面上按一下 [完成]](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>復原到另一個時間點
若要復原到介於 HANA 快照集 (包含在儲存體快照集中) 與晚於 HANA 快照集時間點復原的快照集之間的時間點，請執行下列作業：

1. 確定您擁有包含從 HANA 快照到您想要復原之時間在內的所有 HANA 交易記錄備份。
2. 開始執行 [復原到最近狀態] 下的程序。
3. 在程序的步驟 2 中，在 [指定復原類型] 視窗中選取 [將資料庫復原到下列時間點]，指定時間點，然後完成步驟 3-6。

### <a name="monitoring-the-execution-of-snapshots"></a>監視快照集的執行

當您使用 HANA 大型執行個體的儲存體快照集時，您也需要監視這些儲存體快照集的執行情形。 執行儲存體快照集的指令碼會將輸出寫入檔案，然後儲存至與 Perl 指令碼相同的位置。 針對每個儲存體快照集都會寫入一個個別的檔案。 每個檔案的輸出會清楚顯示快照集指令碼所執行的各階段︰

- 尋找需要建立快照集的磁碟區
- 尋找從這些磁碟區建立的快照集
- 刪除最後存在的快照集以符合您指定的快照集數目
- 建立 SAP HANA 快照集
- 建立所有磁碟區的儲存體快照集
- 刪除 SAP HANA 快照集
- 將最近的快照集重新命名為 **.0**

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
您可以從此範例看到指令碼如何記錄 HANA 快照的建立。 在相應放大案例中，是在主要節點上起始此程序。 主要節點會在每個背景工作角色節點上開始同步建立 SAP HANA 快照集。 然後建立儲存體快照。 順利執行儲存體快照之後，就會刪除 HANA 快照。 然後再次從主要節點開始。


## <a name="disaster-recovery-principles"></a>災害復原原則
對於 HANA 大型執行個體，我們提供了能在不同 Azure 區域的 HANA 大型執行個體戳記之間執行的災害復原功能。 這表示如果您將 HANA 大型執行個體單位部署在 Azure 的美國西部區域，就可以利用美國東部區域的 HANA 大型執行個體單位來作為災害復原單位。 如先前所述，災害復原功能不會自動進行設定，因為如果要使用此功能，您還必須付費來使用 DR 區域中的另一個 HANA 大型執行個體單位。 災害復原設定適用於相應增加以及相應放大設定。 

在截至目前為止所部署的案例中，客戶會使用 DR 區域中的單位來執行非生產系統，這些系統會使用已安裝的 HANA 執行個體。 HANA 大型執行個體單位的 SKU 必須與生產用途所使用的 SKU 相同。 然後，您必須另外再訂購一些磁碟區，以便讓這些磁碟區作為從生產系統到災害復原網站的儲存體複寫目標。 實際複寫到 DR 網站的磁碟區會涵蓋：

- /hana/data
- /hana/log
- HANA/shared
- /hana/log/backup (包括 /usr/sap)


所提供之災害復原功能的基礎是 HANA 大型執行個體基礎結構所提供的儲存體複寫功能。 儲存體端所使用的這項功能不會在儲存體發生變更時，以非同步方式連續不斷地複寫變更。 相反地，這項機制要仰賴「系統會定期對這些磁碟區建立快照集」的事實。 已複寫的快照集和尚未複寫的新快照集之間的差異，接著會傳送至災害復原網站的目標磁碟區 (和生產磁碟區的大小相同)。 在第一次的時候，您必須在快照之間的差異資料量變得越來越小之前，將完整的磁碟區資料傳送出去。 因此，DR 網站中的磁碟區會包含生產網站中所執行之磁碟區快照集的每個人。 這一事實會讓您最終使用該 DR 系統來取得先前的狀態，以復原遺失的資料，而又不必復原生產系統。

若您在生產環境中使用 HANA 系統複寫來作為高可用性功能，則系統只會複寫第 2 層 (或複本) 執行個體的磁碟區。 這個組態可能會造成影響，以至於當您在此單位中維護或關閉次要複本 (第 2 層) 伺服器單位或 SAP HANA 執行個體時，導致儲存體複寫至 DR 網站的作業延遲。 

>[!IMPORTANT]
>和多層式 HANA 系統複寫一樣，當您使用 HANA 大型執行個體的災害復原功能時，關閉第 2 層 HANA 執行個體或伺服器單位就會封鎖複寫到災害復原網站的作業。


>[!NOTE]
>HANA 大型執行個體儲存體複寫功能會鏡像處理/複寫儲存體快照集。 因此，如果您未依照本文件的備份章節所說的方式來執行儲存體快照集，系統就完全不會複寫到災害復原網站。 若要執行儲存體複寫到災害復原網站的作業，就必須先執行儲存體快照集。

為了盡量減少復原點目標，HANA 大型執行個體服務中設定了下列複寫間隔：
- 合併的快照集 (快照集類型 = 'hana') 所涵蓋的磁碟區每隔 15 分鐘就會複寫到災害復原網站中對等的存放磁碟區目標。
- 交易記錄備份磁碟區 (快照集類型 = 'logs') 每隔 3 分鐘就會複寫到災害復原網站中對等的存放磁碟區目標。

為了盡量減少復原點目標，您必須設定下列間隔
- 每隔 30 分鐘到 1 小時執行一次 'hana' 類型的儲存體快照集 (請參閱步驟 7 - 執行快照集)。
- 每隔五分鐘執行一次 SAP HANA 交易記錄備份。
- 每隔 5 到 15 分鐘執行一次 'logs' 類型的儲存體快照集。 使用此一時間長度，您應該就能達到約 15 到 25 分鐘的 RPO。

若您使用此設定，HANA 交易記錄備份磁碟區和 /hana/data、/hana/log、/hana/shared (包括 /usr/sap) 的交易記錄備份、儲存體快照集與複寫的順序，將會如下圖所示。

 ![時間軸上交易記錄備份快照集與快照鏡像之間的關聯性](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

若要在災害復原案例中達成更好的復原點目標，您可以從 SAP HANA on Azure (大型執行個體) 將 HANA 交易記錄備份複製到其他 Azure 區域。 若要再縮減此復原點目標，請執行下列概略步驟：

1. 儘可能經常將 HANA 交易記錄複製到 /hana/logbackups。
2. 在交易記錄備份完成時，使用 rsync 將這些備份複製到 NFS 共用所裝載的 Azure 虛擬機器 (VM)，這些 VM 位於 Azure 生產區域和 DR 區域中的 Azure 虛擬網路內。 您必須將這兩個 Azure VNet 連線到 ExpressRoute 電路 (此電路會將生產 HANA 大型執行個體連線到 Azure) (請參閱本文件＜災害復原需求＞一節中的圖片)。 
3. 將交易記錄備份保留在該區域內 VM 所連結的 NFS 匯出儲存體。
4. 在災害容錯移轉案例中，為您在 /hana/logbackups 磁碟區所找到的交易記錄備份，補充您在災害復原網站的 NFS 共用上所擷取的較新交易記錄備份。 
5. 現在，您可以開始將交易記錄備份還原至最新的備份，並且能夠儲存到 DR 區域。

## <a name="disaster-recovery-fail-over-procedure"></a>災害復原容錯移轉程序
如果您想要或需要容錯移轉至 DR 網站，請看一下這個程序，在此程序中，您必須與「SAP HANA on Azure Operations」互動。 就目前來說，此程序的步驟大致如下：

- 由於您會在 HANA 大型執行個體的災害復原單位上執行非生產的 HANA 執行個體，因此請關閉此執行個體。 我們會假設您已預先安裝休眠中的 HANA 生產執行個體。
- 您必須確定不會再有任何 SAP HANA 程序處於執行狀態。 您可以使用下列命令進行這項檢查：/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList。 您應該會從輸出中看到 hdbdaemon 程序處於已停止狀態，而且已沒有其他 HANA 程序處於執行中或已啟動狀態。
- 現在，您必須確認要讓災害復原網站還原到哪個快照集名稱或 SAP HANA 備份識別碼。 在真實的災害復原案例中，此快照集通常會是最新的快照集。 不過，您可能會處於必須復原所遺失資料的情況。 因此，您必須挑選較早的快照集。
- 您必須透過高優先順序支援要求來與「SAP HANA on Azure 服務管理」連絡，並要求在 DR 網站上還原該快照集/HANA 備份識別碼。 在作業端，系統將會進行下列步驟：
    - 停止將快照集從生產磁碟區複寫至災害復原磁碟區。 生產網站中斷原因所造成的未發生 i。
    - 您所選擇的儲存體快照集/備份識別碼會還原到災害復原磁碟區上。
    - 還原後，災害復原磁碟區即可供掛接到災害復原區域的 HANA 大型執行個體單位。
- 身為客戶，您該進行的下一個步驟就是將災害復原磁碟區掛接至災害復原網站中的 HANA 大型執行個體單位。 
- 然後，您就可以啟動到目前為止處於休眠狀態的 SAP HANA 生產執行個體。
- 如果您另外選擇複製交易記錄備份的記錄以降低 RPO 時間，您必須將這些交易記錄備份合併到新掛接之 DR 的 /hana/logbackups 目錄。 請勿覆寫現有備份，只要複製儲存體尚未複寫的較新備份即可。

這些步驟的下一個順序涉及根據所還原的儲存體快照集和可用的交易記錄備份來復原 SAP HANA 生產執行個體。 步驟如下：

使用 SAP HANA Studio 將備份位置變更為 /hana/logbackups，如下所示 ![變更 DR 復原的備份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

SAP HANA 會掃描備份檔案位置，並建議可供作為還原標的的最新交易記錄備份。 掃描過程可能需要幾分鐘的時間，完成後會出現如下所示的畫面：![DR 復原的交易記錄備份清單](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

在下一個步驟中，您必須執行下列作業來調整某些預設值：

- 取消選取 [使用差異備份]
- 選取 [初始化記錄區域]，如下所示：

 ![設定初始化記錄區域](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

在下一個畫面中按 [完成]，如下所示：

 ![完成 DR 還原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

此時應該會出現進度視窗，如下所示。 請記住，這是具有 3 節點相應放大 SAP HANA 組態的災害復原還原範例。

 ![還原進度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果還原似乎卡在 [完成] 畫面，而且沒有出現進度畫面，請確認背景工作節點上的所有 SAP HANA 執行個體是否有在執行。 如有必要，請手動啟動 SAP HANA 執行個體。


### <a name="fail-back-from-dr-to-production-site"></a>從 DR 容錯回復到生產網站
我們假設您是因為生產 Azure 區域發生問題而容錯移轉至災害復原網站，而不是因為您需要取回遺失的資料。 這表示您現在已在生產環境中，或已在災害復原網站一陣子了。 由於生產網站中的問題已經解決，您想要容錯回復到生產網站。 您不能遺失資料，因此退回生產網站的作業會涉及許多步驟，並且要與「SAP HANA on Azure Operations」密切合作。 身為客戶，您必須在問題獲得解決後，觸發作業來啟動反向同步處理到生產網站的程序。

這些步驟的順序如下：

- 「SAP HANA on Azure Operations」取得觸發程序，以從災害復原存放磁碟區 (目前的生產狀態代表) 同步處理生產存放磁碟區。 處於此狀態時，生產網站中的 HANA 大型執行個體單位會關閉。
- 「SAP HANA on Azure Operations」監視複寫程序，並確保能趕上通知您的時間
- 您必須關閉應用程式，因為它會使用災害復原網站上的生產 HANA 執行個體。 然後執行 HANA 交易記錄備份，接著將災害復原網站的 HANA 大型執行個體單位上所執行的 HANA 執行個體停止
- 在災害復原網站的 HANA 大型執行個體單位上所執行的 HANA 執行個體關閉後，作業必須再次手動同步處理磁碟區。
- 「SAP HANA on Azure Operations」會再次啟動生產網站中的 HANA 大型執行個體單位，然後將它轉交給您。 請確定在您啟動 HANA 大型執行個體單位時，SAP HANA 執行個體是處於關閉狀態。
- 現在，您必須執行和先前容錯移轉至災害復原網站時所執行的相同資料庫還原步驟。

### <a name="monitoring-disaster-recovery-replication"></a>監視災害復原複寫

您可以藉由執行 azure\_hana\_replication\_status.pl 指令碼，監視儲存體複寫進度的狀態。 此指令碼必須從正在災害復原位置中執行的單位來執行，否則不會如預期般運作。 無論複寫是否正在作用中，指令碼都有作用。 您可以對災害復原位置中的租用戶所具有的每個 HANA 大型執行個體單位執行這個指令碼。 但不能將它用來取得開機磁碟區的詳細資料。

依下列方式呼叫指令碼：
```
./replication_status.pl <HANA SID>
```

輸出會依磁碟區細分成下列各區段：  

- 連結狀態
- 目前的複寫活動
- 所複寫的最新快照集 
- 最新快照集的大小
- 最後一次完成的快照式複寫和現在的時刻之間目前所延隔的時間。  

連結狀態會顯示為「作用中」(除非位置之間的連結已關閉)，或是顯示為目前正在進行容錯移轉事件。 複寫活動會指出資料目前是正在複寫、處於閒置，或者連結目前是否正發生其他活動。 最後一次複寫的快照集應該只會顯示為 'snapmirror...'。 然後會顯示最後一個快照集的大小。 最後則會顯示延隔時間。 延隔時間代表從排程的複寫時間到複寫完成時的時間。 資料複寫的延隔時間可能會顯示超過一個小時，特別是初始的第一次複寫，即使複寫已啟動亦然。 延隔時間會持續增加，直到進行中的複寫完成。

輸出範例如下：

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












