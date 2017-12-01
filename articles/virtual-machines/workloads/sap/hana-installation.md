---
title: "在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA | Microsoft Docs"
description: "如何在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA。"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
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
ms.openlocfilehash: 8ef85c098058c97e5ec6d758fcf1dab5b1a87786
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>如何安裝和設定 Azure 上的 SAP HANA (大型執行個體)

以下是閱讀本指南之前要知道的重要定義。 我們在 [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中引進了兩種不同類別的 HANA 大型執行個體單位：

- S72、S72m、S144、S144m、S192 和 S192m，稱之為「類型 I 類別」的 SKU。
- S384、S384m、S384xm、S576、S768 和 S960，稱之為「類型 II 類別」的 SKU。

在整個 HANA 大型執行個體文件中都會使用類別規範，最終以 HANA 大型執行個體 SKU 為基礎指向不同的功能和需求。

其他常用定義包括：
- **大型執行個體戳記︰**經 SAP HANA TDI 認證並專門用來執行 Azure 內 SAP HANA 執行個體的硬體基礎結構堆疊。
- **SAP HANA on Azure (大型執行個體)：**Azure 中產品方案的正式名稱，此產品方案可在經 SAP HANA TDI 認證並部署在不同 Azure 區域之「大型執行個體」戳記中的硬體上執行 HANA 執行個體。 **HANA 大型執行個體**是 SAP HANA on Azure (大型執行個體) 的相關詞彙簡稱，並在本技術部署指南中廣泛使用。


安裝 SAP HANA 是您的責任，您可以在遞交新的 SAP HANA on Azure (大型執行個體) 伺服器之後開始此活動。 在 Azure VNet 和 HANA 大型執行個體單位之間建立連線之後。 

> [!Note]
> 根據 SAP 原則，SAP HANA 安裝必須由通過 SAP HANA 安裝執行認證的使用者執行。 通過 SAP 相關技術認證檢定、SAP HANA 安裝認證檢定的人員，或 SAP 認證系統整合者 (SI)。

再次檢查，尤其是在打算安裝 HANA 2.0、[SAP 支援附註 #2235581 - SAP HANA：支援的作業系統](https://launchpad.support.sap.com/#/notes/2235581/E)時，才能確保 OS 受您決定安裝的 SAP HANA 版本支援。 您要了解 HANA 2.0 支援的作業系統所受到的限制比 HANA 1.0 支援的作業系統更多。 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>收到 HANA 大型執行個體單位之後的前幾個步驟

在收到 HANA 大型執行個體並建立對執行個體的存取和連線之後，**第一步**是向您的作業系統提供者註冊執行個體的 OS。 這個步驟會包括需要在已部署的 SUSE SMT 執行個體的 Azure VM 中註冊 SUSE Linux OS。 HANA 大型執行個體單位可以連接到這個 SMT 執行個體 (請參閱本文件後文)。 或是必須向您必須連接的 Red Hat Subscription Manager 註冊 RedHat OS。 另請參閱這份[文件](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的備註。 此步驟也是能否修補作業系統的必要項目。 這是一項屬於客戶職責的工作。 有關 SUSE，請在[這裡](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)尋找安裝和設定 SMT 的文件。

**第二步**是檢查是否有特定 OS 版次/版本的新修補程式和修正。 請檢查「HANA 大型執行個體」的修補程式等級是否為最新狀態。 根據作業系統修補程式/版次和 Microsoft 可部署對映像所做之變更的時機，可能會有未包含最新修補程式的情況。 因此，在接管 HANA 大型執行個體單位之後，必須檢查特定 Linux 廠商是否已在這期間發行與安全性、功能、可用性及效能相關的修補程式而需要套用。

**第三步**是查看相關的「SAP 附註」，以在特定的 OS 版次/版本上安裝及設定 SAP HANA。 因為依據個別的安裝情況，建議事項會有所變更或是對「SAP 附註」或組態所做的變更會有所不同，所以 Microsoft 將無法總是完美地設定「HANA 大型執行個體」單位。 因此，身為客戶的您必須閱讀與實際 Linux 版本 SAP HANA 有關的 SAP 附註。 亦請檢查作業系統版次/版本所需的組態，並在尚未完成的部分套用組態設定。

具體而言，請檢查下列參數並最後調整成：

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

從 SLES12 SP1 和 RHEL 7.2 開始，必須在 /etc/sysctl.d 目錄的組態檔中設定這些參數。 例如，必須建立名為 91-NetApp-HANA.conf 的組態檔。 如果是舊版 SLES 和 RHEL，則必須 /etc/sysctl.conf 中設定這些參數。

針對所有 RHEL 版次以及從 SLES12 開始的版本， 
- sunrpc.tcp_slot_table_entries = 128

參數必須在 /etc/modprobe.d/sunrpc-local.conf 中設定。 如果該檔案不存在，則必須先透過新增下列項目來建立它： 
- options sunrpc tcp_max_slot_table_entries=128

**第四步**是檢查您「HANA 大型執行個體單位」的系統時間。 系統會使用代表 HANA 大型執行個體戳記所在 Azure 區域位置的系統時區來部署執行個體。 您可以自由變更您所擁有執行個體的系統時間或時區。 如果您這麼做並為您的租用戶訂購更多執行個體，請做好準備，您將必須調整新交付之執行個體的時區。 在移交執行個體之後，Microsoft 作業即無法洞察您為執行個體設定的系統時區。 因此，為新部署的執行個體設定的時區可能不會與您變更後的時區相同。 如此一來，身為客戶，您就必須負責檢查並視需要調整已移交之執行個體的時區。 

**第五步**是檢查 etc/hosts。 在移交刀鋒伺服器時，會針對不同的用途為它們指派不同的 IP 位址 (請參閱下一節)。 檢查 etc/hosts 檔案。 如果將單位新增到現有的租用戶，請勿期望系統會使用先前交付之系統的 IP 位址來正確維護新部署之系統的 etc/hosts。 因此，必須倚賴身為客戶的您來檢查設定是否正確，如此新部署的執行個體才能進行互動並解析您租用戶中先前所部署單位的名稱。 

## <a name="networking"></a>網路
我們假設您已如下列文件所述，依照建議設計 Azure VNet，並將這些 VNet 連接到 HANA 大型執行個體：

- [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 上 SAP HANA (大型執行個體) 的基礎結構和連接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

關於單一單位的網路功能，有一些值得一提的細節。 每個「HANA 大型執行個體」單位都隨附兩個或三個 IP 位址，這些位址會指派給該單位的兩個或三個 NIC 連接埠。 在 HANA 向外延展組態與「HANA 系統複寫」案例中會使用三個 IP 位址。 其中一個指派給單位之 NIC 的 IP 位址是來自 [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中所述的伺服器 IP 集區。

在獲指派兩個 IP 位址的情況下，單位的分配應該會看起來如下：

- 指派給 eth0.xx 的 IP 位址應該來自您向 Microsoft 提交的「伺服器 IP 集區」位址範圍。 此 IP 位址將用來在 OS 的 /etc/hosts 中進行維護。
- 指派給 eth1.xx 的 IP 位址應該用於對 NFS 的通訊。 因此，「不」需要在 etc/hosts 中維護這些位址，即可允許租用戶內的執行個體對執行個體流量。

就 HANA 系統複寫或 HANA 向外延展部署案例而言，並不適合使用有兩個指派之 IP 位址的刀鋒伺服器組態。 如果僅指派兩個 IP 位址且想要部署這類組態，請連絡 Azure 服務管理的 SAP HANA 在第三個 VLAN 中指派第三個 IP 位址。 在三個 NIC 連接埠上指派三個 IP 位址的 HANA 大型執行個體單位，適用下列使用規則：

- 指派給 eth0.xx 的 IP 位址應該來自您向 Microsoft 提交的「伺服器 IP 集區」位址範圍。 因此，此 IP 位址將不用來在 OS 的 /etc/hosts 中進行維護。
- 指派給 eth1.xx 的 IP 位址應該用於對 NFS 儲存體的通訊。 因此，不應該在 etc/hosts 中維護此類型的位址。
- eth2.xx 應該專門用來在 etc/hosts 中維護，以供不同執行個體之間的通訊使用。 這些位址也會是需要在向外延展 HANA 組態中維護的 IP 位址，以作為 HANA 用於節點間組態的 IP 位址。



## <a name="storage"></a>儲存體

SAP HANA on Azure (大型執行個體) 的儲存體配置是由 SAP HANA on Azure Service Management 透過 SAP 建議的指引進行設定，請參閱 [SAP HANA 儲存體需求](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)技術白皮書。 有不同 HANA 大型執行個體 SKU 的不同磁碟區的約略大小記載於 [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

下表列出的存放磁碟區的命名慣例：

| 儲存體使用量 | 掛接名稱 | 磁碟區名稱 | 
| --- | --- | ---|
| HANA 資料 | /hana/data/SID/mnt0000<m> | 儲存體 IP：/hana_data_SID_mnt00001_tenant_vol |
| HANA 記錄檔 | /hana/log/SID/mnt0000<m> | 儲存體 IP：/hana_log_SID_mnt00001_tenant_vol |
| HANA 記錄備份 | /hana/log/backups | 儲存體 IP：/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA 共用 | /hana/shared/SID | 儲存體 IP：/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | 儲存體 IP：/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

其中 SID = HANA 執行個體的系統識別碼 

tenant = 部署租用戶時的內部作業列舉。

如您所見，HANA 共用和 usr/sap 共用相同的磁碟區。 掛接點的專門用語確實包含 HANA 執行個體的系統識別碼及掛接編號。 在相應增加部署中，只有一個掛接，像是 mnt00001。 而在向外延展部署中，您會看到許多掛接，因為有背景工作角色和主要節點。 在向外延展環境中，資料、記錄檔和記錄備份磁碟區是共用的，且會附加至向外延展組態中的每個節點上。 若是執行多個 SAP 執行個體的組態，會建立一組不同的磁碟區，並附加到 HANA 大型執行個體單位。

當您閱讀文件及了解 HANA 大型執行個體單位後，您會發現這些單位有著相當大的 HANA/data 磁碟區，而且還有一個 HANA/log/backup 磁碟區。 我們之所以將 HANA/data 的大小設定成如此大，是因為我們為身為客戶的您提供的儲存體快照集就是使用同一個磁碟區。 這表示您執行的儲存體快照集愈多，您在指派存放磁碟區中的快照集所使用的空間也愈多。 HANA/log/backup 磁碟區不視為放入資料庫備份的磁碟區。 它會調整大小以用為 HANA 交易記錄備份的備份磁碟區。 在未來的儲存體快照集自助服務版本中，我們會讓這個特定的磁碟區以提高快照集頻率為目標。 而如果您想要加入 HANA 大型執行個體基礎結構所提供的災害復原功能，也可將複寫到災害復原站台的頻率提高。 詳細資訊請參閱 [Azure 上 SAP Hana (大型執行個體) 的高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

除了所提供的儲存體之外，您還可以購買額外的儲存體容量 (增量單位為 1 TB)。 此額外儲存體可以做為新的磁碟區新增至 HANA 大型執行個體。

在 Azure 服務管理的 SAP HANA 上架期間，客戶會為 sidadm 使用者和 sapsys 群組指定使用者識別碼 (UID) 和群組識別碼 (GID) (例如：1000,500)。安裝 SAP HANA 系統時，請務必使用這些相同的值。 當您想要在一個單元上部署多個 HANA 執行個體時，您會取得多個磁碟區集合 (每個執行個體一組)。 如此一來，在部署期間您需要定義：

- 不同的 (sidadm 衍生來源的) HANA 執行個體 SID。
- 不同 HANA 執行個體的記憶體大小。 因為每個執行個體的記憶體大小會定義每個個別磁碟區集合中的磁碟區大小。

根據儲存提供者建議，已針對所有掛接的磁碟區設定下列掛接選項 (不包括開機 LUN)：

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

這些掛接點設定在類似 /etc/fstab 的位置，如下圖所示：

![HANA 大型執行個體單位中掛接磁碟區的 fstab](./media/hana-installation/image1_fstab.PNG)

S72m HANA 大型執行個體單元的 df -h 命令輸出應該像這樣：

![HANA 大型執行個體單位中掛接磁碟區的 fstab](./media/hana-installation/image2_df_output.PNG)


儲存體控制器和大型執行個體戳記中的節點會同步處理至 NTP 伺服器。 當您針對 NTP 伺服器同步處理 SAP HANA on Azure (大型執行個體) 單位和 Azure VM 時，Azure 或大型執行個體戳記中的基礎結構與計算單位之間應該不會耗費太多時間。

為了將 SAP HANA 到底層所使用的儲存體都最佳化，您應該一併設定下列 SAP HANA 組態參數：

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
針對從 SAP HANA 1.0 到 SPS12 的版本，您可以在安裝 SAP HANA 資料庫時設定這些參數，如 [SAP 附註 #2267798 - SAP HANA 資料庫組態 (英文)](https://launchpad.support.sap.com/#/notes/2267798) 所述

您也可以透過使用 hdbparam 架構，在安裝 SAP HANA 資料庫之後設定這些參數。 

在 SAP HANA 2.0，hdbparam 架構已被取代。 因此，必須使用 SQL 命令來設定參數。 如需詳細資料，請參閱 [SAP 附註 #2399079：在 HANA 2 中已刪除 hdbparam (英文)](https://launchpad.support.sap.com/#/notes/2399079)。


## <a name="operating-system"></a>作業系統

根據 [SAP 支援附註 #1999997 - 常見問題集：SAP HANA 記憶體](https://launchpad.support.sap.com/#/notes/1999997/E)，已傳遞作業系統映像的交換空間會設為 2 GB。 您必須以客戶身分設定所需的任何不同設定。

[SUSE Linux Enterprise Server 12 SP1 for SAP 應用程式](https://www.suse.com/products/sles-for-sap/hana)是針對 SAP HANA on Azure (大型執行個體) 安裝的 Linux 的分佈。 這個特定的分佈提供&quot;現成&quot; SAP 特有功能，(包括預先設定的參數，有效地在 SLES 上執行 SAP)。

請參閱 SUSE 網站上的[資源程式庫/技術白皮書](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)和 SAP Community Network (SCN) 上的 [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)，以取得與在 SLES 上部署 SAP HANA 相關的有用資源 (包括高可用性的設定、SAP 作業特定的安全性強化等等)。

額外和有用的 SAP on SUSE 相關連結︰

- [SUSE Linux 網站上的 SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP 的最佳做法：加入佇列複寫 – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)。
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (包括 SLES 12 for SAP 應用程式)。

SAP 支援附註適用於在 SLES 12 上實作 SAP HANA︰

- [SAP 支援附註 #1944799 – SLES 作業系統安裝的 SAP HANA 指南](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)。
- [SAP 支援附註 #2205917 – 針對 SLES 12 for SAP 應用程式的 SAP HANA DB 建議作業系統設定](https://launchpad.support.sap.com/#/notes/2205917/E)。
- [SAP 支援附註 #1984787 – SUSE Linux Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787)。
- [SAP 支援附註 #171356 – 在 Linux 上的 SAP 軟體︰一般資訊](https://launchpad.support.sap.com/#/notes/1984787)。
- [SAP 支援附註 #1391070 – Linux UUID 解決方案](https://launchpad.support.sap.com/#/notes/1391070)。

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) 是可供在 HANA Large Instances 上執行 SAP HANA 的另一個供應項目。 版本 RHEL 6.7 和 7.2 均可使用。 請注意，與僅支援 RHEL 7.2 和更新版本的原生 Azure VM 不同，HANA 大型執行個體也支援 RHEL 6.7。 不過，我們建議使用 RHEL 7.x 版本。

額外和有用的 SAP on Red Hat 相關連結︰
- [Red Hat Linux 網站上的 SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)。

SAP 支援附註適用於實作 SAP HANA on Red Hat︰

- [SAP 支援附註 #2009879 - 適用於 Red Hat Enterprise Linux (RHEL) 作業系統的 SAP HANA 指南](https://launchpad.support.sap.com/#/notes/2009879/E)。
- [SAP 支援附註 #2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2292690)。
- [SAP 支援附註 #2247020 - SAP HANA DB：適用於 RHEL 6.7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2247020)。
- [SAP 支援附註 #1391070 – Linux UUID 解決方案](https://launchpad.support.sap.com/#/notes/1391070)。
- [SAP 支援附註 #2228351 - Linux：RHEL 6 或 SLES 11 上的 SAP HANA Database SPS 11 修訂版 110 (或更新版本)](https://launchpad.support.sap.com/#/notes/2228351)。
- [SAP 支援附註 #2397039 - 常見問題集：SAP on RHEL](https://launchpad.support.sap.com/#/notes/2397039)。
- [SAP 支援附註 #1496410 - Red Hat Enterprise Linux 6.x：安裝和升級](https://launchpad.support.sap.com/#/notes/1496410)。
- [SAP 支援附註 #2002167 - Red Hat Enterprise Linux 7.x：安裝和升級](https://launchpad.support.sap.com/#/notes/2002167)。

## <a name="time-synchronization"></a>時間同步處理

建立在 SAP NetWeaver 架構上的 SAP 應用程式，對構成 SAP 系統的各種元件時間差異很敏感。 具有錯誤標題 ZDATE\_LARGE\_TIME\_DIFF 的 SAP ABAP 簡短傾印可能很眼熟，因為這些簡短傾印會在不同伺服器或 VM 的系統時間離開太遠時顯示。

針對 SAP HANA on Azure (大型執行個體)，在 Azure 中完成的時間同步處理不適用於大型執行個體戳記中的計算單位。 這項同步處理並不適用於在原生 Azure VM 中執行 SAP 應用程式，因為 Azure 可確保正確同步處理系統時間。 如此一來，必須設定不同時間伺服器，可供 Azure VM 上執行的 SAP 應用程式伺服器與 HANA 大型執行個體上執行的 SAP HANA 資料庫執行個體使用。 大型執行個體戳記中的儲存體基礎結構與 NTP 伺服器進行時間同步處理。

## <a name="setting-up-smt-server-for-suse-linux"></a>設定 SUSE Linux 的 SMT 伺服器
SAP HANA 大型執行個體無法直接連線到網際網路。 因此它不是可向作業系統提供者註冊此類單元，並且下載與套用修補程式的簡單程序。 如果是 SUSE Linux，其中一種解決方案可能是在 Azure VM 中設定 SMT 伺服器。 而 Azure VM 需要裝載於 Azure VNet 中，連接到 HANA 大型執行個體。 有了這類的 SMT 伺服器，HANA 大型執行個體單位才可能註冊並下載修補程式。 

SUSE 提供大量有關 [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) (SLES 12 SP2 訂用帳戶管理工具) 的指南。 

安裝 SMT 伺服器的先決條件是完成 HANA 大型執行個體的工作，您需要：

- 連接到 HANA 大型執行個體 ER 線路的 Azure VNet。
- 與組織相關聯的 SUSE 帳戶。 而組織會需要一些有效的 SUSE 訂用帳戶。

### <a name="installation-of-smt-server-on-azure-vm"></a>在 Azure VM 上安裝 SMT 伺服器

在此步驟中，您要在 Azure VM 上安裝 SMT 伺服器。 第一個量值是登入 [SUSE 客戶中心](https://scc.suse.com/)

當您登入後，請移至 [組織]--> [Organization Credentials] \(組織認證)。 在該區段中，您應該會找到設定 SMT 伺服器所需的認證。

第三個步驟是在 Azure VNet 中安裝 SUSE Linux VM。 若要部署 VM，請使用 Azure 的 SLES 12 SP2 資源庫映像。 在部署程序中，不要定義 DNS 名稱，也不要使用這個螢幕擷取畫面中的靜態 IP 位址。

![SMT 伺服器的 VM 部署](./media/hana-installation/image3_vm_deployment.png)

已部署的 VM 是較小的 VM，會取得 Azure VNet 的內部 IP 位址 10.34.1.4。 VM 的名稱為 smtserver。 安裝之後，已檢查 HANA 大型執行個體單元的連線。 HANA 大型執行個體單位可能需要以 Azure VM 的 etc/hosts 設定解析，視您如何安排名稱解析而定。 在要用來保存修補程式的 VM 中新增額外的磁碟。 開機磁碟本身可能太小。 在範例中，磁碟已掛接到 /srv/www/htdocs，如下列螢幕擷取畫面所示。 100 GB 的磁碟應已足夠。

![SMT 伺服器的 VM 部署](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

登入 HANA 大型執行個體單位、維護 /etc/hosts，並檢查是否可以連線到應該要透過網路執行 SMT 伺服器的 Azure VM。

順利完成這項檢查之後，您需要登入應該執行 SMT 伺服器的 Azure VM。 如果您要使用 putty 登入 VM，您需要在 bash 視窗中執行這一系列的命令：

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

執行這些命令之後，重新啟動您的 bash 來啟動設定。 然後啟動 YAST。

在 YAST 中，移至軟體維護並搜尋 smt。 選取 smt，會自動切換至 yast2-smt，如下所示。

![yast 中的 SMT](./media/hana-installation/image5_smt_in_yast.PNG)


接受 smtserver 上的安裝選項。 安裝之後，請移至 SMT 伺服器組態，並輸入之前從 SUSE 客戶中心擷取的組織認證。 亦請輸入 Azure VM 主機名稱當作 SMT 伺服器 URL。 在本例中，它是 https://smtserver，如下張圖所示。

![SMT 伺服器組態](./media/hana-installation/image6_configuration_of_smtserver1.png)

下一個步驟，您需要測試 SUSE 客戶中心的連線是否作用。 如您在下圖中所見，示範案例中的連線可以運作。

![測試 SUSE 客戶中心連線](./media/hana-installation/image7_test_connect.png)

一旦 SMT 安裝程式啟動，您就需要提供資料庫密碼。 因為是新的安裝，所以您需要定義該密碼，如下張圖中所示。

![定義資料庫密碼](./media/hana-installation/image8_define_db_passwd.PNG)

下個互動是建立憑證。 如下所示完成對話方塊，執行後續步驟。

![建立 SMT 伺服器憑證](./media/hana-installation/image9_certificate_creation.PNG)

組態結尾的「執行同步處理檢查」步驟可能需要幾分鐘。 安裝和設定 SMT 伺服器之後，您應該會在掛接點 /srv/www/htdocs/ 下發現目錄儲存機制及儲存機制下的子目錄。 

使用這些命令重新啟動 SMT 伺服器及其相關服務。

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>將套件下載到 SMT 伺服器

重新啟動所有服務之後，在使用 Yast 的 SMT 管理中選取適當的套件。 套件選項取決於 HANA 大型執行個體伺服器的作業系統映像，不是執行 SMT 伺服器的 VM SLES 版次或版本。 選項畫面範例如下所示。

![選取套件](./media/hana-installation/image10_select_packages.PNG)

一旦完成套件選取，您就需要啟動所設定之 SMT 伺服器的選取套件初始複本。 如下所示，在 shell 中使用命令 smt-mirror 觸發這個複本。


![將套件下載到 SMT 伺服器](./media/hana-installation/image11_download_packages.PNG)

如上文中所見，套件應該複製到建立在掛接點 /srv/www/htdocs 下的目錄。 此程序需要一會兒。 視選取的套件數目而定，可能會超過一個小時或更久。
當此程序完成時，您需要移至 SMT 用戶端安裝程式。 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>在 HANA 大型執行個體單位上設定 SMT 用戶端

本例中的用戶端是 HANA 大型執行個體單位。 SMT 伺服器安裝程式已將指令碼 clientSetup4SMT.sh 複製到 Azure VM。 將該指令碼複製到您想與 SMT 伺服器連線的 HANA 大型執行個體單位。 使用 -h 選項啟動指令碼，並提供 SMT 伺服器的名稱當做參數。 在此範例 smtserver 中。

![設定 SMT 用戶端](./media/hana-installation/image12_configure_client.PNG)

可能有用戶端從伺服器成功載入憑證，但註冊失敗的案例，如下所示。

![用戶端註冊失敗](./media/hana-installation/image13_registration_failed.PNG)

如果註冊失敗，請參閱本 [SUSE 支援文件](https://www.suse.com/de-de/support/kb/doc/?id=7006024)並執行文中所述步驟。

> [!IMPORTANT] 
> 伺服器名稱需要使用 VM 名稱，在本例的 smtserver 中，沒有完整的網域名稱。 只有 VM 名稱作用。 

在執行這些步驟之後，您需要在 HANA 大型執行個體單位上執行下列命令。

```
SUSEConnect –cleanup
```

> [!Note] 
> 在我們的測試中，該步驟後一律必須等候幾分鐘。 在 SUSE 一文所述的更正措施之後，立即執行 clientSetup4SMT.sh，最後可能會出現憑證目前無效的訊息。 通常等 5-10 分鐘後再執行 clientSetup4SMT.sh，就可以順利完成用戶端組態。

如果碰到需要根據 SUSE 一文步驟修正的問題，您需要在 HANA 大型執行個體單位上重新啟動 clientSetup4SMT.sh。 現在它應該如下所示成功完成。

![用戶端註冊成功](./media/hana-installation/image14_finish_client_config.PNG)

在這個步驟中，您設定了 HANA 大型執行個體單位的 SMT 用戶端連線到安裝在 Azure VM 中的 SMT 伺服器。 您現在可以利用 'zypper up' 或 'zypper in' 將作業系統修補程式安裝至 HANA 大型執行個體，或安裝其他套件。 您應該了解，您只能取得之前下載到 SMT 伺服器的修補程式。


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>範例：在 HANA 大型執行個體上安裝 SAP HANA
本節示範如何在 HANA 大型執行個體單位上安裝 SAP HANA。 開始的狀態看起來像：

- 您向 Microsoft 提供所有資料以部署 SAP HANA 大型執行個體。
- 您從 Microsoft 收到 SAP HANA 大型執行個體。
- 您建立了連線到內部部署網路的 Azure VNet。
- 您將 HANA 大型執行個體的 ExpressRotue 線路連線到相同的 Azure VNet。
- 您安裝了 Azure VM 用做為 HANA 大型執行個體的跳躍方塊。
- 您很確定可以從跳躍方塊連線到 HANA 大型執行個體單位，反之亦然。
- 您已確認是否已安裝所有必要的套件及修補程式。
- 您閱讀了在所用作業系統上安裝 HANA 的相關 SAP 附註和文件，並確定作業系統版本支援 HANA 版本選擇。

接下來顯示的內容是將 HANA 安裝套件下載到跳躍方塊 VM，本例是在 Windows 作業系統中執行，HANA 大型執行個體單位的套件複本和安裝順序。

### <a name="download-of-the-sap-hana-installation-bits"></a>下載 SAP HANA 安裝位元組
因為 HANA 大型執行個體單位不直接連線到網際網路，所以您無法直接從 SAP 將安裝套件下載到 HANA 大型執行個體 VM。 為解決沒有直接網際網路連線的問題，您需要跳躍方塊。 您將套件下載到跳躍方塊 VM。

為下載 HANA 安裝套件，您需要 SAP S-user 或其他使用者，讓您存取 SAP Marketplace。 登入之後，請完成這一系列的畫面：

移至 [SAP Service Marketplace](https://support.sap.com/en/index.html) > 按一下 [下載軟體] > [Installations and Upgrade] \(安裝與升級) > [By Alphabetical Index] \(依字母順序排列索引) > 在 H – SAP HANA 平台版本下 > SAP HANA 平台版本 2.0 > [安裝] > 下載下列檔案

![下載 HANA 安裝](./media/hana-installation/image16_download_hana.PNG)

在示範案例中，我們下載了 SAP HANA 2.0 安裝套件。 在 Azure 跳躍方塊 VM 中，將自我解壓縮的封存展開到目錄中，如下所示。

![解壓縮 HANA 安裝](./media/hana-installation/image17_extract_hana.PNG)

解壓縮封存後，將 HANA 大型執行個體單位的解壓縮目錄 (上例中為 51052030)，複製到您所建目錄的 /hana/shared 磁碟區。

> [!Important]
> 安裝套件「請勿」複製到根或開機 LUN，因為空間有限，而且必須供其他處理序使用。


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>在 HANA 大型執行個體單位上安裝 SAP HANA
為安裝 SAP HANA，您需要以根使用者身分登入。 只有根才有足夠的權限安裝 SAP HANA。
您需要做的第一件事就是設定複製的 /hana/shared 目錄權限。 需要設定的權限類似

```
chmod –R 744 <Installation bits folder>
```

如果您想要使用圖形化設定安裝 SAP HANA，需要將 gtk2 套件安裝在 HANA 大型執行個體上。 檢查是否已使用命令安裝

```
rpm –qa | grep gtk2
```

在後續步驟中，我們會示範使用圖形化使用者介面安裝 SAP HANA 的過程。 在下個步驟中，移至安裝目錄並瀏覽至子目錄 HDB_LCM_LINUX_X86_64。 Start

```
./hdblcmgui 
```
從該目錄。 現在您要跟著指引完成一連串的畫面，並提供安裝資料。 在所示範例中，我們要安裝 SAP HANA 資料庫伺服器和 SAP HANA 用戶端元件。 因此我們選取 [SAP HANA 資料庫]，如下所示。

![在安裝中選取 [HANA]](./media/hana-installation/image18_hana_selection.PNG)

在下一個畫面中選擇 [Install New System] \(安裝新系統) 選項

![新安裝選取 [HANA]](./media/hana-installation/image19_select_new.PNG)

這個步驟之後，您需要在數個不同的其他元件中選取，它們都可以另外安裝至 SAP HANA 資料庫伺服器。

![選取其他的 HANA 元件](./media/hana-installation/image20_select_components.PNG)

為達成本文件的目標，我們選擇 SAP HANA 用戶端和 SAP HANA Studio。 我們也安裝了相應增加執行個體。 因此在下一個畫面中，您需要選擇 [Single-Host System] \(單一主機系統)。 

![選取相應增加安裝](./media/hana-installation/image21_single_host.PNG)

在下一個畫面中，您需要提供一些資料。

![提供 SAP HANA SID](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> 您需要提供在訂購 HANA 大型執行個體部署時提供給 Microsoft 的相同 SID，當作 HANA 系統識別碼 (SID)。 選擇不同的 SID，安裝會因為不同磁碟區上的存取權限問題而失敗。

使用 /hana/shared 目錄當做安裝目錄。 在下一個步驟中，您需要提供 HANA 資料檔和 HANA 記錄檔的位置。


![提供 HANA 記錄檔位置](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> 您應該將隨附於掛接點、包含前一個畫面所選 SID 的磁碟區，定義為資料和記錄檔磁碟區。 如果 SID 與您鍵入的不相符，請在前一個畫面中，返回並調整 SID，使與掛接點上的值相合。

在下一個步驟中，檢閱主機名稱並加以更正。 

![檢閱主機名稱](./media/hana-installation/image24_review_host_name.PNG)

在下一個步驟中，您也需要擷取在訂購 HANA 大型執行個體部署時提供給 Microsoft 的資料。 

![提供系統使用者 UID 與 GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> 您需要提供在訂購單位部署時提供給 Microsoft 的相同系統使用者識別碼和使用者群組識別碼。 如果無法提供完全一致的識別碼，在 HANA 大型執行個體單位上安裝 SAP HANA 就會失敗。

在接下來的兩個畫面中 (本文不顯示)，您需要提供 SAP HANA 資料庫的系統使用者密碼和 sapadm 使用者密碼，以將 SAP 主機代理程式安裝為 SAP HANA 資料庫執行個體的一部分。

定義密碼之後，會出現確認畫面。 請檢查所有列出的資料列，再繼續進行安裝。 您到達記錄安裝進度的進度畫面，類似下面這個：

![檢查安裝進度](./media/hana-installation/image27_show_progress.PNG)

當安裝完成後，您應該會看到類似下圖的畫面。

![安裝完成](./media/hana-installation/image28_install_finished.PNG)

此時，SAP HANA 執行個體應該啟動並執行，隨時可供使用。 您應該能夠從 SAP HANA Studio 連接到它。 也請務必要檢查並套用最新的 SAP HANA 修補程式。
























































 







 




