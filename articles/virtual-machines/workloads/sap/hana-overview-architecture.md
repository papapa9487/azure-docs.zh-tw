---
title: "SAP HANA on Azure (大型執行個體) 的概觀和架構 | Microsoft Docs"
description: "如何部署 SAP HANA on Azure (大型執行個體) 的架構概觀。"
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
ms.date: 10/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63e1820033e051b72601291c5206772192e68769
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Azure 上的 SAP HANA (大型執行個體) 概觀和架構

## <a name="what-is-sap-hana-on-azure-large-instances"></a>什麼是 SAP HANA on Azure (大型執行個體)？

SAP HANA on Azure (大型執行個體) 是 Azure 獨有的解決方案。 除了提供 Azure 虛擬機器來部署和執行 SAP HANA 外，Azure 還可讓身為客戶的您在您專屬的裸機伺服器上執行並部署 SAP HANA。 SAP HANA on Azure (大型執行個體) 解決方案會建置在指派給身為客戶的您的非共用主機/伺服器裸機硬體上。 伺服器硬體會內嵌在更大的戳記中，該戳記會包含計算/伺服器、網路和儲存體基礎結構。 而這樣的組合已通過 HANA TDI 認證。 SAP HANA on Azure (大型執行個體) 的服務供應項目會提供各種不同的伺服器 SKU 或大小，小自擁有 72 個 CPU 和 768 GB 記憶體的單位，大至擁有 960 個 CPU 和 20 TB 記憶體的單位。

客戶在基礎結構戳記內是利用租用戶來加以區隔，詳情如下：

- 網路功能：透過每個客戶指派的租用戶所獲得的虛擬網路，在基礎結構堆疊內區隔客戶。 一個租用戶只會指派給單一客戶。 但一個客戶可以擁有多個租用戶。 租用戶的網路區隔會在基礎結構戳記層級禁止租用戶之間進行網路通訊。 即使這些租用戶屬於相同客戶也是如此。
- 儲存元件：透過已獲派存放磁碟區的存放虛擬機器來區隔。 存放磁碟區只能指派給一個存放虛擬機器。 一個存放虛擬機器只會指派給已通過 SAP HANA TDI 認證之基礎結構堆疊中的一個租用戶。 因此，指派給存放虛擬機器的存放磁碟區只能在一個特定且相關的租用戶中存取。 而無法在不同的已部署租用戶之間可見。
- 伺服器或主機：伺服器或主機單位無法讓不同客戶或不同租用戶共用。 部署給客戶的伺服器或主機是不可分割的裸機計算單位，並且只會指派給一個租用戶。 您**無法**使用任何硬體分割或軟體分割方式來讓身為客戶的您與其他客戶共用主機或伺服器。 指派給特定租用戶之存放虛擬機器的存放磁碟區會掛接到這類伺服器。 一個租用戶可以獨佔方式獲得不同 SKU 的一個到多個伺服器單位。
- SAP HANA on Azure (大型執行個體) 的基礎結構戳記內會部署許多不同的租用戶，並透過租用戶概念在網路、儲存體和計算層級將這些租用戶彼此區隔。 


這些裸機伺服器單位只支援用來執行 SAP HANA。 SAP 應用程式層或工作負載中介軟體層會在 Microsoft Azure 虛擬機器中執行。 執行 SAP HANA on Azure (大型執行個體) 單位的基礎結構戳記會連線到 Azure 網路骨幹，因此能在 SAP HANA on Azure (大型執行個體) 單位與 Azure 虛擬機器之間提供低延遲的連線。

本文件是五份文件的其中一份，將會說明 SAP HANA on Azure (大型執行個體) 的主題。 在本文件中，我們會講述基本架構、責任、所提供的服務，並概述解決方案的各項功能。 其他四份文件會詳述並深入探討大多數層面，例如網路和連線。 SAP HANA on Azure (大型執行個體) 的文件並未說明 SAP NetWeaver 安裝方面或在 Azure VM 中部署 SAP NetWeaver 方面的內容。 這方面的主題可在相同文件容器中的其他文件中找到。 


本指南的五個部分涵蓋下列主題：

- [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上 SAP HANA (大型執行個體) 的基礎結構和連接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [如何在 Azure 上安裝和設定 SAP HANA (大型執行個體)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上 SAP Hana (大型執行個體) 的高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上 SAP HANA (大型執行個體) 疑難排解和監視](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>定義

《架構和技術部署指南》中廣泛使用數個常見的定義。 請注意下列詞彙及其意義：

- **IaaS：**基礎結構即服務
- **PaaS：**平台即服務
- **SaaS：**軟體即服務
- **SAP 元件︰**個別的 SAP 應用程式，例如 ECC、BW、Solution Manager 或 EP。 SAP 元件可以傳統 ABAP 或 Java 技術為基礎，或以非 NetWeaver 應用程式 (例如商務物件) 為基礎。
- **SAP 環境︰**一或多個以邏輯方式分組的 SAP 元件，可執行像是開發、QAS、訓練、DR 或生產等商務功能。
- **SAP 架構︰**意指您 IT 架構中的整個 SAP 資產。 SAP 環境包含所有生產和非生產環境。
- **SAP 系統︰**SAP ERP 開發系統、SAP BW 測試系統、SAP CRM 生產系統等的 DBMS 層與應用程式層的組合。Azure 部署不支援在內部部署與 Azure 之間分割這兩個層。 表示 SAP 系統可以在內部部署或在 Azure 部署。 不過，您可以將 SAP 環境的不同系統部署到 Azure 或內部部署。 例如，您可以在 Azure 部署 SAP CRM 開發和測試系統，而在內部部署 SAP CRM 生產系統。 對於 SAP HANA on Azure (大型執行個體) 來說，預期您會在 Azure VM 中裝載 SAP 系統的 SAP 應用程式層，並在 HANA 大型執行個體戳記中的某個單位上裝載相關的 SAP HANA 執行個體。
- **大型執行個體戳記︰**經 SAP HANA TDI 認證並專門用來執行 Azure 內 SAP HANA 執行個體的硬體基礎結構堆疊。
- **SAP HANA on Azure (大型執行個體)：**Azure 中產品方案的正式名稱，此產品方案可在經 SAP HANA TDI 認證並部署在不同 Azure 區域之「大型執行個體」戳記中的硬體上執行 HANA 執行個體。 **HANA 大型執行個體**是 SAP HANA on Azure (大型執行個體) 的相關詞彙簡稱，並在本技術部署指南中廣泛使用。
- **跨單位：**描述將 VM 部署到 Azure 訂用帳戶的案例，該訂用帳戶在內部部署資料中心與 Azure 之間具有站對站、多站台或 ExpressRoute 連線能力。 在一般 Azure 文件中，這類部署也會描述為跨單位案例。 連線的原因是為了將內部部署網域、內部部署 Active Directory/OpenLDAP 和內部部署 DNS 延伸到 Azure。 內部部署架構會擴充到 Azure 訂用帳戶的 Azure 資產。 在此擴充下，VM 可以是內部部署網域的一部分。 內部部署網域的網域使用者可以存取伺服器，並可在這些 VM 上執行服務 (例如 DBMS 服務)， 但無法在內部部署的 VM 和 Azure 部署的 VM 之間進行通訊和名稱解析。 這是部署大部分 SAP 資產時的典型案例。 如需詳細資訊，請參閱[規劃與設計 VPN 閘道](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)和[使用 Azure 入口網站建立具有站對站連線的 VNet](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的指南。
- **租用戶：**部署於 HANA 大型執行個體戳記的客戶回隔離到「租用戶」中。 每個租用戶的網路、儲存體和計算層會彼此區隔開來。 因此，指派給不同租用戶的儲存體和計算單位無法看到彼此，也無法在 HANA 大型執行個體戳記層級上彼此通訊。 客戶可以選擇部署到不同的租用戶。 即使如此，租用戶彼此之間還是無法在 HANA 大型執行個體戳記層級上進行通訊。

針對在 Microsoft Azure 公用雲端上部署 SAP 工作負載的主題，已發佈了各種不同的額外資源。 強烈建議所有在 Azure 中規劃和執行 SAP HANA 部署的人都需有經驗，並且熟悉 Azure IaaS 的主體及 Azure IaaS 上的 SAP 工作負載部署。 下列資源提供詳細資訊，應在繼續進行之前先參考：


- [在 Microsoft Azure 虛擬機器上使用 SAP 解決方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>認證

除了 NetWeaver 認證之外，SAP 還需要特殊的 SAP HANA 認證，才能在特定的基礎結構 (例如 Azure IaaS) 上支援 SAP HANA。

NetWeaver (以及就某種程度而言 SAP HANA 認證) 的相關核心 SAP 附註是 [SAP 附註 #1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533)。

這個 [SAP 附註 #2316233 - SAP HANA on Microsoft Azure (大型執行個體)](https://launchpad.support.sap.com/#/notes/2316233/E) 也很重要。 它涵蓋本指南中所述的解決方案。 此外，也支援您在 GS5 VM 類型的 Azure 中執行 SAP HANA。 [此案例的資訊發佈在 SAP 網站上](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)。

SAP 附註 #2316233 中提及的 SAP HANA on Azure (大型執行個體) 解決方案讓 Microsoft 和 SAP 客戶能夠在 Azure 中部署大型 SAP Business Suite、SAP Business Warehouse (BW)、S/4 HANA、BW/4HANA 或其他 SAP HANA 工作負載。 這個解決方案是以經 SAP-HANA 認證的專用硬體戳記 ([SAP HANA 量身訂做的資料中心整合 - TDI](https://scn.sap.com/docs/DOC-63140) \(英文\)) 為基礎。 以 SAP HANA TDI 設定的解決方案執行，可讓您確知所有 SAP HANA 型應用程式 (包括 SAP Business Suite on SAP HANA、SAP Business Warehouse (BW) on SAP HANA、S4/HANA 及 BW4/HANA) 都能在硬體基礎結構上運作。

與在「Azure 虛擬機器」中執行 SAP HANA 相比，此解決方案有一個優點 - 它提供更大的記憶體磁碟區。 如果您想要使此解決方案能夠執行，需了解一些重要層面：

- SAP 應用程式層和非 SAP 應用程式是在裝載於一般 Azure 硬體戳記中的「Azure 虛擬機器」(VM) 中執行。
- 客戶內部部署基礎結構、資料中心及應用程式部署，均會透過 Azure ExpressRoute (建議使用) 或虛擬私人網路 (VPN) 連線到 Microsoft Azure 雲端平台。 Active Directory (AD) 和 DNS 也會延伸到 Azure。
- 用於 HANA 工作負載的 SAP HANA 資料庫執行個體會在 SAP HANA on Azure (大型執行個體) 上執行。 「大型執行個體」戳記會連接到 Azure 網路中，讓在 Azure VM 中執行的軟體能夠與在「HANA 大型執行個體」中執行的 HANA 執行個體互動。
- SAP HANA on Azure (大型執行個體) 的硬體是已預先安裝 SUSE Linux Enterprise Server 或 Red Hat Enterprise Linux 的「基礎結構即服務」(IaaS) 中所提供的專用硬體。 與使用「Azure 虛擬機器」一樣，進一步的作業系統更新和維護是由您負責。
- 不論是安裝 HANA 或任何在 HANA 大型執行個體的單位上執行 SAP HANA 所需的額外元件，還是 SAP HANA on Azure 的所有個別進行中作業和管理，都是由您負責。
- 除了這裡所述的解決方案之外，您也可以在連接到 SAP HANA on Azure (大型執行個體) 的 Azure 訂用帳戶中安裝其他元件。  例如，可啟用與 SAP HANA 資料庫 (跳板伺服器、RDP 伺服器、SAP HANA Studio、適用於 SAP BI 案例的 SAP Data Services，或網路監視解決方案) 之通訊和/或直接通訊功能的元件。
- 與在 Azure 中一樣，HANA 大型執行個體也會提供支援高可用性和災害復原功能。

## <a name="architecture"></a>架構

簡要說來，SAP HANA on Azure (大型執行個體) 解決方案是將 SAP 應用程式層放在 Azure VM 中，而將資料庫層放在 SAP TDI 設定的硬體上，此硬體是位於相同 Azure 區域中連接到 Azure IaaS 的「大型執行個體」戳記中。

> [!NOTE]
> 您需要在與 SAP DBMS 層相同的 Azure 區域中部署 SAP 應用程式層。 此規則在已發佈的 Azure 上 SAP 工作負載的相關資訊中有詳細記載。 

SAP HANA on Azure (大型執行個體) 的整體架構不僅提供一個經 SAP TDI 認證的硬體組態 (適用於 SAP HANA 資料庫的非虛擬化、裸機、高效能伺服器)，還提供 Azure 的能力與彈性來調整 SAP 應用程式層的資源以符合您的需求。

![SAP HANA on Azure (大型執行個體) 的架構概觀](./media/hana-overview-architecture/image1-architecture.png)

顯示的架構分成三個部分：

- **右邊：**一個執行資料中心內不同應用程式的內部部署基礎結構，其中使用者會存取 LOB 應用程式 (例如 SAP)。 在理想的情況下，這個內部部署基礎結構會接著以 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 連接到 Azure。

- **中間：**顯示 Azure IaaS，在此案例中會使用 Azure VM 來裝載 SAP 或其他利用 SAP HANA 作為 DBMS 系統的應用程式。 以 Azure VM 所提供的記憶體運作的較小 HANA 執行個體會與其應用程式層一起部署在 Azure VM 中。 深入了解[虛擬機器](https://azure.microsoft.com/services/virtual-machines/)。
<br />「Azure 網路」可用來將 SAP 系統及其他應用程式一起群集到「Azure 虛擬網路」(VNet) 中。 這些 VNet 既會連接到 SAP HANA on Azure (大型執行個體)，也會連接到內部部署系統。
<br />針對受支援在 Microsoft Azure 中執行的 SAP NetWeaver 應用程式和資料庫，請參閱 [SAP 支援附註 #1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533)。 如需在 Azure 上部署 SAP 解決方案的相關文件，請檢閱：

  -  [在 Windows 虛擬機器 (VM) 上使用 SAP](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [在 Microsoft Azure 虛擬機器上使用 SAP 解決方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左邊：**顯示「Azure 大型執行個體」戳記中經 SAP HANA TDI 認證的硬體。 「HANA 大型執行個體」單位是使用與從內部部署環境連線到 Azure 時相同的技術來連線到您訂用帳戶的 Azure VNet。

「Azure 大型執行個體」戳記本身結合了下列元件：

- **運算：**以 Intel Xeon E7-8890v3 或 Intel Xeon E7-8890v4 處理器為基礎的伺服器，提供必要的運算能力且經 SAP HANA 認證。
- **網路：**將運算、儲存體及 LAN 元件互連的整合式高速網路網狀架構。
- **儲存體：**可透過整合式網路網狀架構存取的儲存體基礎結構。 根據所要部署的特定 SAP HANA on Azure (大型執行個體) 組態，會提供特定的儲存體容量 (若要使用更多儲存體容量，則需每月額外付費)。

在「大型執行個體」戳記的多租用戶基礎結構內，是將客戶部署成隔離的租用戶。 在租用戶的部署中，您需要在您的 Azure 註冊內為 Azure 訂用帳戶命名。 這將會成為 Azure 訂用帳戶，HANA 大型執行個體將要據以付費。 這些租用戶與 Azure 訂用帳戶具有一對一關係。 網路智能可從分屬於不同 Azure 訂用帳戶之不同 Azure VNet 的某個 Azure 區域中，存取某一個租用戶中部署的 HANA 大型執行個體單位。 雖然這些 Azure 訂用帳戶必須屬於相同的 Azure 註冊。 

與 Azure VM 一樣，在多個 Azure 區域中都有提供 SAP HANA on Azure (大型執行個體)。 為了提供「災害復原」功能，您可以選擇加入。 某個地理政治區域內不同的大型執行個體戳記會彼此互連。 例如，美國西部和美國東部的 HANA 大型執行個體戳記會基於 DR 複寫目的，透過專用網路連結來連線。 

就像使用「Azure 虛擬機器」時您可以在不同的 VM 類型之間做選擇一樣，您也可以從針對不同 SAP HANA 工作負載類型量身打造的不同「HANA 大型執行個體」SKU 中做選擇。 SAP 會根據 Intel 處理器世代，為不同的工作負載套用記憶體與處理器插槽比例 - 提供四種不同的 SKU 類型：

自 2017 年 7 月起，SAP HANA on Azure (大型執行個體) 適用於美國西部和美國東部、澳大利亞東部、澳大利亞東南部、西歐及北歐等 Azure 區域中的數個設定：

| SAP 解決方案 | CPU | 記憶體 | 儲存體 | Availability |
| --- | --- | --- | --- | --- |
| 已針對 OLAP 最佳化：SAP BW、BW/4HANA<br /> 或 SAP HANA (一般 OLAP 工作負載) | SAP HANA on Azure S72<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 個 CPU 核心和 72 個 CPU 執行緒 |  768 GB |  3 TB | 可用 |
| --- | SAP HANA on Azure S144<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 個 CPU 核心和 144 個 CPU 執行緒 |  1.5 TB |  6 TB | 不再提供 |
| --- | SAP HANA on Azure S192<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 個 CPU 核心和 192 個 CPU 執行緒 |  2.0 TB |  8 TB | 可用 |
| --- | SAP HANA on Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  4.0 TB |  16 TB | 準備好接受訂購 |
| 已針對 OLTP 最佳化：SAP Business Suite<br /> on SAP HANA 或 S/4HANA (OLTP)<br /> (一般 OLTP) | SAP HANA on Azure S72m<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 個 CPU 核心和 72 個 CPU 執行緒 |  1.5 TB |  6 TB | 可用 |
|---| SAP HANA on Azure S144m<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 個 CPU 核心和 144 個 CPU 執行緒 |  3.0 TB |  12 TB | 不再提供 |
|---| SAP HANA on Azure S192m<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 個 CPU 核心和 192 個 CPU 執行緒  |  4.0 TB |  16 TB | 可用 |
|---| SAP HANA on Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  6.0 TB |  18 TB | 準備好接受訂購 |
|---| SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  8.0 TB |  22 TB |  準備好接受訂購 |
|---| SAP HANA on Azure S576<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 個 CPU 核心和 576 個 CPU 執行緒 |  12.0 TB |  28 TB | 準備好接受訂購 |
|---| SAP HANA on Azure S768<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 個 CPU 核心和 768 個 CPU 執行緒 |  16.0 TB |  36 TB | 準備好接受訂購 |
|---| SAP HANA on Azure S960<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 個 CPU 核心和 960 個 CPU 執行緒 |  20.0 TB |  46 TB | 準備好接受訂購 |

- CPU 核心 = 伺服器單位處理器總和的非超執行緒 CPU 核心總和。
- CPU 執行緒 = 伺服器單位處理器總和的超執行緒 CPU 核心所提供之計算執行緒的總和。 預設會將所有單位設定為使用超執行緒。


上述為「可用」或「不再提供」的各種不同設定在 [SAP 支援附註 #2316233 - SAP HANA on Microsoft Azure (大型執行個體)](https://launchpad.support.sap.com/#/notes/2316233/E) \(英文\) 中均有提到。 標示為「準備好接受訂購」的設定，很快就能在 SAP 附註中找到它們的項目。 不過，對於這六個可使用 HANA 大型執行個體服務的不同 Azure 區域來說，已經可以訂購那些執行個體 SKU。

選擇的特定組態取決於工作負載、CPU 資源及所需的記憶體。 OLTP 工作負載可以使用已針對 OLAP 工作負載最佳化的 SKU。 

適用於所有優惠的硬體基礎為經 SAP HANA TDI 認證。 不過，我們會區分兩個不同類別的硬體，將 SKU 分割為：

- S72、S72m、S144、S144m、S192 和 S192m，稱之為「類型 I 類別」的 SKU。
- S384、S384m、S384xm、S576、S768 和 S960，稱之為「類型 II 類別」的 SKU。

請務必注意，完整的 HANA 大型執行個體戳記並非僅配置給單一客戶使用。 這項事實也適用於透過 Azure 中所部署的網路網狀架構連線的計算與儲存資源的機架。 「HANA 大型執行個體」基礎結構 (例如 Azure) 會在下列三個層面部署彼此隔離的不同客戶&quot;租用戶&quot;：

- 網路：在 HANA 大型執行個體戳記內透過虛擬網路來區隔。
- 儲存體：透過已獲派存放磁碟區的存放虛擬機器來區隔，以及在租用戶之間隔開存放磁碟區。
- 計算：將伺服器單位專用指派給單一租用戶。 不對伺服器單位進行硬體分割或軟體分割。 不在租用戶之間共用單一伺服器或主機單位。 

因此，不同租用戶之間所部署的 HANA 大型執行個體單位不會讓彼此看到。 部署在不同租用戶的 HANA 大型執行個體單位也無法在 HANA 大型執行個體戳記層級上直接與其他單位進行通訊。 只有位在同一個租用戶內的 HANA 大型執行個體單位可以在 HANA 大型執行個體戳記層級上與其他單位進行通訊。
大型執行個體戳記中部署的租用戶會將計費智能指派給一個 Azure 訂用帳戶。 不過，網路智能可以從同一個 Azure 註冊內其他 Azure 訂用帳戶的 Azure VNet 中加以存取。 如果您使用同一個 Azure 區域中的另一個 Azure 訂用帳戶來部署，則也可以選擇尋求個別的 HANA 大型執行個體租用戶。

在「HANA 大型執行個體」上執行 SAP HANA 與在於 Azure 中部署的 Azure VM 上執行 SAP HANA 之間有顯著的不同：

- SAP HANA on Azure (大型執行個體) 沒有虛擬化層。 您會獲得基礎裸機硬體的效能。
- 與 Azure 不同，SAP HANA on Azure (大型執行個體) 伺服器是特定客戶專用。 您無法對伺服器單位或主機進行硬體分割或軟體分割。 因此，HANA 大型執行個體單位會整個指派給租用戶，並整體指派給身為客戶的您。 重新啟動或關閉伺服器並不會自動導致作業系統和 SAP HANA 被部署到另一部伺服器。 (針對類型 I 類別的 SKU，唯一的例外狀況是如果伺服器可能發生問題而必須在另一部伺服器上執行重新部署的情況)。
- 與 Azure 中配合最佳性價比來選取主機處理器類型的方式不同，為 SAP HANA on Azure (大型執行個體) 選擇的處理器類型是 Intel E7v3 和 E7v4 處理器系列中效能最佳的類型。


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>在一個 HANA 大型執行個體單位上執行多個 SAP HANA 執行個體
您可以在該 HANA 大型執行個體單位上，裝載多個作用中的 SAP HANA 執行個體。 為了仍能提供儲存體快照集和災害復原等功能，這類設定在每個執行個體上都需要一個磁碟區組。 目前可使用下列方式來細分 HANA 大型執行個體單位：

- S72、S72m、S144、S192：遞增量為 256 GB，最小起始單位為 256 GB。 不同的遞增量 (例如 256 GB、512 GB，依此類推) 可組合為該單位的記憶體最大值。
- S144m 和 S192m：遞增量為 256 GB，最小單位為 512 GB。 不同的遞增量 (例如 512 GB、768 GB，依此類推) 可組合為該單位的記憶體最大值。
- 類型 II 類別：遞增量為 512 GB，最小起始單位為 2 TB。 不同的遞增量 (例如 512 GB、1 TB、1.5 TB，依此類推) 可組合為該單位的記憶體最大值。

有一些執行多個 SAP HANA 執行個體的範例看起來如下：

| SKU | 記憶體大小 | 儲存體大小 | 具有多個資料庫的大小 |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768 GB HANA 執行個體<br /> 或 1x512 GB 執行個體 + 1x256 GB 執行個體<br /> 或 3x256 GB 執行個體 | 
| S72m | 1.5 TB | 6 TB | 3x512GB HANA 執行個體<br />或 1x512 GB 執行個體 + 1x1 TB 執行個體<br />或 6x256 GB 執行個體<br />或 1x1.5 TB 執行個體 | 
| S192m | 4 TB | 16 TB | 8x512 GB 執行個體<br />或 4x1 TB 執行個體<br />或 4x512 GB 執行個體 + 2x1 TB 執行個體<br />或 4x768 GB 執行個體 + 2x512 GB 執行個體<br />或 1x4 TB 執行個體 |
| S384xm | 8 TB | 22 TB | 4x2 TB 執行個體<br />或 2x4 TB 執行個體<br />或 2x3 TB 執行個體 + 1x2 TB 執行個體<br />或 2x2.5 TB 執行個體 + 1x3 TB 執行個體<br />或 1x8 TB 執行個體 |


您已經了解這個概念。 當然還有其他變化形式。 

### <a name="using-sap-hana-data-tiering-and-extension-nodes"></a>使用 SAP HANA 資料分層與擴充節點
對於不同 SAP NetWeaver 版本的 SAP BW 和 SAP BW/4HANA，SAP 可支援資料分層模型。 有關資料分層模型的詳細資料可以在本文件以及 SAP 所提供的下列文件內指出的部落格中找到：[AP BW/4HANA AND SAP BW ON HANA WITH SAP HANA EXTENSION NODES](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)。
透過 HANA 大型執行個體，您可以使用 SAP HANA 擴充節點的 option-1 設定，其詳情請見本常見問題集和 SAP 部落格文件。 option-2 設定可以搭配下列 HANA 大型執行個體 SKU 來進行設定：S72m、S192、S192m、S384 和 S384m。  
只看該文件您可能不會立即看出其優勢。 但只要看看 SAP 大小調整指導方針，您就可以使用 option-1 和 option-2 SAP HANA 擴充節點來看到其優勢。 範例如下：

- SAP HANA 大小調整指導方針通常需要兩倍的資料磁碟區數量來作為記憶體。 因此，當您使用熱資料來執行 SAP HANA 執行個體時，記憶體中最多只會填入 50% 的資料。 理論上，剩餘的記憶體會保留下來供 SAP HANA 執行其工作。
- 這表示，在具有 2 TB 記憶體且執行 SAP BW 資料庫的 HANA 大型執行個體 S192 單位中，只有 1 TB 會作為資料磁碟區。
- 如果您使用其他採用 option-1 的 SAP HANA 擴充節點 (同樣是 S192 HANA 大型執行個體 SKU)，該節點會另外提供您 2 TB 容量的資料磁碟區。 即使在 option-2 設定，也只會另外提供您 4 TB 的暖資料磁碟區。 相較於熱節點，「暖」擴充節點的記憶體容量全都可以用來儲存資料 (若為 option-1)，若為 option-2 的 SAP HANA 擴充節點設定，則可將記憶體兩倍的容量用於資料磁碟區。
- 因此，您最終會有 3 TB 的容量可供儲存資料，而且熱資料與暖資料的比率為 1:2 (若為 option-1)，若為 option-2 的擴充節點設定，則會有 5 TB 的資料容量和 1:4 的比率。

不過，相較於記憶體的資料磁碟區容量越高，您想要的暖資料越有可能會儲存在磁碟儲存體上。


## <a name="operations-model-and-responsibilities"></a>作業模型和職責

SAP HANA on Azure (大型執行個體) 提供的服務可與 Azure IaaS 服務合作。 您會獲得一個「HANA 大型執行個體」執行個體，其中已安裝針對 SAP HANA 最佳化的作業系統。 就像使用 Azure IaaS VM 時一樣，大多數強化 OS、安裝所需額外軟體、安裝 HANA、操作 OS 和 HANA 以及更新 OS 和 HANA 的工作都是由您負責。 Microsoft 不會強制您進行 OS 更新或 HANA 更新。

![SAP HANA on Azure (大型執行個體) 的職責](./media/hana-overview-architecture/image2-responsibilities.png)

如上圖所示，SAP HANA on Azure (大型執行個體) 是一個多租用戶的「基礎結構即服務」產品方案。 因此，大部分的職責劃分是以 OS 與基礎結構為界。 Microsoft 負責作業系統線以下的所有服務層面，而您則負責該線以上的部分，包括作業系統。 因此，您目前針對合規性、安全性、應用程式管理、基礎與 OS 管理可能採用的大多數內部部署方法都可繼續使用。 這些系統在所有方面都會顯得它們彷彿就在您的網路中一樣。

不過，這項服務已針對 SAP HANA 最佳化，因此有些部分您需要與 Microsoft 合作，才能使用根本基礎結構功能來獲得最佳結果。

以下清單提供有關每一層及您職責的更多詳細資料：

**網路：**用於執行 SAP HANA 之「大型執行個體」戳記、其對儲存體之存取、執行個體間之連線 (用於相應放大及其他功能)、對架構之連線以及對 SAP 應用程式層裝載於「Azure 虛擬機器」的 Azure 之連線的所有內部網路。 它也包含用於「災害復原」用途複寫的「Azure 資料中心」間 WAN 連線。 所有網路都是依租用戶分割並已套用 QOS。

**儲存體：**除了用於快照的虛擬化分割儲存體之外，也包括用於 SAP HANA 伺服器所需之所有磁碟區的虛擬化分割儲存體。 

**伺服器：**用以執行指派給租用戶之 SAP HANA DB 的專用實體伺服器。 類型 I 類別 SKU 的伺服器是抽象的硬體。 使用這些類型的伺服器，即會在設定檔中收集和維護伺服器設定，而您可以將其從一個實體硬體移至另一個實體硬體。 透過操作對設定檔進行這類 (手動) 移動，稍微可與 Azure 服務修復進行比較。 類型 II 類別 SKU 的伺服器不提供這類功能。

**SDDC：**可將資料中心當作一個軟體定義的實體來管理的管理軟體。 它可讓 Microsoft 基於延展、可用性及效能等理由將資源集中存放。

**O/S：**您所選擇在伺服器上執行的 OS (SUSE Linux 或 Red Hat Linux)。 您所提供的 OS 映像是個別 Linux 廠商提供給 Microsoft 來執行 SAP HANA 的映像。 您需要有 Linux 廠商的訂用帳戶，才能取得特定的 SAP HANA 最佳化映像。 您的責任包括向 OS 廠商註冊映像。 自 Microsoft 移交時開始，您也要負責對 Linux 作業系統採取進一步的修補措施。 這項修補也包含成功安裝 SAP HANA 可能需要的其他封裝 (請參閱 SAP 的 HANA 安裝文件和 SAP 注意事項)，而特定 Linux 廠商的 SAP HANA 最佳化 OS 映像並未包含這些封裝。 客戶的責任也包括與 OS 發生問題/最佳化相關的 OS 修補，以及與特定伺服器硬體相關的驅動程式。 或 OS 的任何安全性或功能性修補。 客戶也要負責監視和容量規劃︰

- CPU 資源耗用量
- 記憶體耗用量
- 與可用空間、IOPS 及延遲相關的磁碟區
- 「HANA 大型執行個體」與 SAP 應用程式層之間的網路磁碟區流量

「HANA 大型執行個體」的根本基礎結構提供備份和還原 OS 磁碟區的功能。 使用這項功能也是您的職責所在。

**中介軟體：**主要是「SAP HANA 執行個體」。 管理、操作及監視是您的職責。 有提供的功能可讓您使用儲存體快照來進行備份/還原及「災害復原」。 這些功能是由基礎結構所提供的。 不過，您的職責還包括使用這些功能來設計高可用性或災害復原、利用它們，以及監視儲存體快照集是否已順利執行。

**資料：**您受 SAP HANA 管理的資料，以及其他位於磁碟區或檔案共用上的資料 (例如備份檔案)。 您的職責包括監視磁碟可用空間和管理磁碟區上的內容，以及監視磁碟區和儲存體快照的備份是否已順利執行。 不過，將資料順利複寫到 DR 站台則是 Microsoft 的職責。

**應用程式：**SAP 應用程式執行個體，或就非 SAP 應用程式而言，則是這些應用程式的應用程式層。 您的職責包括部署、管理、操作及監視與下列各項之容量規劃相關的應用程式：CPU 資源耗用量、記憶體耗用量、Azure 儲存體耗用量，以及在 Azure VNet 內和從 Azure VNet 到 SAP HANA on Azure (大型執行個體) 的網路頻寬耗用量。

**WAN：**您為工作負載建立的從內部部署到 Azure 部署的連線。 所有具有 HANA 大型執行個體的客戶都會使用 Azure ExpressRoute 連線。 此連線不是 SAP HANA on Azure (大型執行個體) 解決方案的一部分，因此您需負責設定此連線。

**封存：**您可能會偏好使用自己的方法在儲存體帳戶中封存資料複本。 封存需要管理、合規性、成本及操作。 您需負責在 Azure 上產生封存複本和備份，並以符合規範的方式儲存它們。

請參閱 [SAP HANA on Azure (大型執行個體) SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)。

## <a name="sizing"></a>調整大小

「HANA 大型執行個體」的大小調整與 HANA 的大小調整大致上沒有差別。 針對您想要就現有的和已部署的系統從其他 RDBMS 移到 HANA 的情況，SAP 提供一些可在您現有 SAP 系統上執行的報告。 如果將資料庫移到 HANA，這些報告就會檢查資料並計算 HANA 執行個體的記憶體需求。 若要取得有關如何執行這些報告及如何取得其最新修補程式/版本的詳細資訊，請參閱下列 SAP 附註：

- [SAP 附註 #1793345 - SAP Suite on HANA 的大小調整 (英文)](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP 附註 #1872170 - Suite on HANA 與 S/4 HANA 大小調整報告 (英文)](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP 附註 #2121330 - 常見問題集：SAP BW on HANA 大小調整報告 (英文)](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP 附註 #1736976 - BW on HANA 的大小調整報告 (英文)](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP 附註 #2296290 - 新的 BW on HANA 大小調整報告 (英文)](https://launchpad.support.sap.com/#/notes/2296290)

針對嶄新的實作，可使用 SAP Quick Sizer 來計算在 HANA 上實作 SAP 軟體時的記憶體需求。

HANA 的記憶體需求會隨著資料量成長而增加，因此您會想要知道現在的記憶體耗用量，以便能夠預測未來的記憶體耗用量。 根據記憶體需求，您便可以接著將您的需求與其中一個「HANA 大型執行個體」SKU 對應。

## <a name="requirements"></a>需求

此清單彙編了執行 SAP HANA on Azure (大型執行個體) 的需求。

**Microsoft Azure：**

- 可連結到 SAP HANA on Azure (大型執行個體) 的 Azure 訂用帳戶。
- Microsoft 頂級支援合約。 如需有關在 Azure 中執行 SAP 的特定資訊，請參閱 [SAP 支援附註 #2015553 - Microsoft Azure 上的 SAP：支援的必要條件](https://launchpad.support.sap.com/#/notes/2015553)。 使用 HANA 大型執行個體單位搭配 384 和更多 CPU，您也需要擴充頂級支援合約以包含 Azure 快速回應 (ARR)。
- 在執行 SAP 大小調整演練之後，知道您所需的 HANA 大型執行個體 SKU。

**網路連線︰**

- 內部部署與 Azure 之間的 Azure ExpressRoute：若要將您的內部部署資料中心連線到 Azure，請務必向您的 ISP 訂購一條至少 1 Gbps 的連線。 

**作業系統︰**

- SUSE Linux Enterprise Server 12 for SAP Applications 的授權。

> [!NOTE] 
> Microsoft 提供的作業系統未向 SUSE 註冊，也未連接 SMT 執行個體。

- 部署在 Azure VM 上 Azure 中的 SUSE Linux Subscription Management Tool (SMT)。 這會提供可讓 SUSE 註冊 SAP HANA on Azure (大型執行個體) 並對其分別進行更新 (因為在「HANA 大型執行個體」資料中心內無法存取網際網路) 的功能。 
- Red Hat Enterprise Linux for SAP HANA 6.7 或 7.2 的授權。

> [!NOTE]
> Microsoft 提供的作業系統未向 Red Hat 註冊，也未連接至 Red Hat Subscription Manager 執行個體。

- 部署在 Azure VM 上 Azure 中的 Red Hat Subscription Manager。 Red Hat Subscription Manager 會提供可讓 Red Hat 註冊 SAP HANA on Azure (大型執行個體) 並對其分別進行更新 (因為從部署在 Azure 大型執行個體戳記上的租用戶內無法直接存取網際網路) 的功能。
- SAP 會要求您與 Linux 提供者之間有一份支援合約。 這項需求並不會因「HANA 大型執行個體」解決方案或您在 Azure 中執行 Linux 的事實而消除。 與使用一些 Linux Azure 資源庫映像時不同，服務費用並未包含在「HANA 大型執行個體」的解決方案內容中。 身為客戶的您必須負責滿足 SAP 對 Linux 散發者之支援合約的相關需求。   
   - 針對 SUSE Linux，請查閱 [SAP 附註 #1984787 - SUSE LINUX Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787) \(英文\) 和 [SAP 附註 #1056161 - SAP 應用程式的 SUSE 優先支援 (英文)](https://launchpad.support.sap.com/#/notes/1056161) \(英文\) 中的支援合約需求。
   - 針對 Red Hat Linux，您必須擁有包含支援和服務 (HANA 大型執行個體的作業系統更新) 的正確訂用帳戶層級。 Red Hat 建議取得「RHEL for SAP 商務應用程式」訂用帳戶。 如需支援與服務的詳細資訊，請查看 [SAP 附註 #2002167 - Red Hat Enterprise Linux 7.x：安裝和升級](https://launchpad.support.sap.com/#/notes/2002167) \(英文\) 和 [SAP 附註 #1496410 - Red Hat Enterprise Linux 6.x：安裝和升級](https://launchpad.support.sap.com/#/notes/1496410) \(英文\)。

**資料庫：**

- SAP HANA (平台或企業版) 的授權和軟體安裝元件。

**應用程式︰**

- 任何連接到 SAP HANA 及相關 SAP 支援合約之 SAP 應用程式的授權和軟體安裝元件。
- 所使用與 SAP HANA on Azure (大型執行個體) 環境及相關支援合約有關的任何非 SAP 應用程式的授權和軟體安裝元件。

**技能︰**

- Azure IaaS 及其元件的相關經驗與知識。
- 在 Azure 中部署 SAP 工作負載的相關經驗與知識。
- 合格的 SAP HANA 安裝人員。
- 可設計以 SAP HANA 為中心之「高可用性」和「災害復原」的 SAP 架構師技能。

**SAP：**

- 預期您是 SAP 客戶且與 SAP 有支援合約
- 特別是針對在類型 II 類別的 HANA 大型執行個體 SKU 上的實作，強烈建議您洽詢 SAP，以取得 SAP HANA 的版本以及大型調升規模硬體的最終設定。


## <a name="storage"></a>儲存體

SAP HANA on Azure (大型執行個體) 的儲存體配置是由 SAP HANA on Azure Service Management 透過 SAP 建議的指引進行設定，請參閱 [SAP HANA 儲存體需求](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) \(英文\) 技術白皮書。

類型 I 類別的 HANA 大型執行個體通常隨附存放磁碟區四倍的記憶體磁碟區。 針對類型 II 類別的 HANA 大型執行個體單位，存放裝置將不會超過四倍。 這些單位會隨附一個用來儲存 HANA 交易記錄備份的磁碟區。 如需更多詳細資料，請參閱[如何在 Azure 上安裝和設定 SAP HANA (大型執行個體)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

關於儲存區配置，請參閱下表。 該表格大致列出了利用不同 HANA 大型執行個體單位提供之不同磁碟區的容量。

| HANA 大型執行個體 SKU | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384 | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12,000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16,000 GB | 2050 GB | 2050 GB | 2040 GB |
| S576 | 20,000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768 | 28,000 GB | 3100 GB | 2050 GB | 3100 GB |
| S960 | 36,000 GB | 4100 GB | 2050 GB | 4100 GB |


實際部署的磁碟區可能會因為部署和用來顯示磁碟區大小的工具而有些微不同。

如果您要細分 HANA 大型執行個體 SKU，以下提供一些可能的劃分片段範例：

| 記憶體磁碟分割，以 GB 為單位 | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


這些大小是粗略的磁碟區數目，會根據部署和用來查看磁碟區的工具而有些微不同。 此外，還有其他的磁碟分割大小可納入考量，例如 2.5 TB。 這些儲存體大小會使用類似上述磁碟分割所使用的公式來計算。 「磁碟分割」一詞不表示作業系統、記憶體或 CPU 資源會以任何方式進行磁碟分割。 它只表示適用於您可能想要在單一 HANA 執行個體單位上部署之不同 HANA 執行個體的儲存體磁碟分割。 

身為客戶的您可能需要更多儲存空間，而您可以選擇購買以 1 TB 為單位的額外儲存體來新增儲存空間。 這個額外的儲存體可以新增為額外的磁碟區，或者可用來擴充一或多個現有的磁碟區。 您無法將磁碟區的大小降低為原始部署的大小以及上述表格中多數記載的大小。 您也無法變更磁碟區的名稱或掛接的名稱。 上述的存放磁碟區已連接至 HANA 大型執行個體單位以作為 NFS4 磁碟區。

身為客戶的您可以選擇使用儲存體快照集來進行備份/還原及災害復原。 如需有關此主題的更多詳細資料，請參閱 [Azure 上 SAP Hana (大型執行個體) 的高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="encryption-of-data-at-rest"></a>待用資料加密
用於「HANA 大型執行個體」的儲存體可允許在資料儲存於磁碟上時，對其進行透明加密。 部署 HANA 大型執行個體單位時，您可以選擇啟用這類加密。 您也可以在已部署完成之後，選擇變更成已加密的磁碟區。 從非加密到已加密磁碟區的轉移過程是透明的，並不需要停機。 

利用類型 I 類別的 SKU，就會將儲存開機 LUN 的磁碟區加密。 如果是類型 II 類別的 HANA 大型執行個體 SKU，您就需要使用 OS 方法來將開機 LUN 加密。 


## <a name="networking"></a>網路

Azure 網路的架構是在 HANA 大型執行個體上成功部署 SAP 應用程式的關鍵元件。 一般而言，SAP HANA on Azure (大型執行個體) 部署具有較大的 SAP 架構，其中包含數個擁有不同資料庫大小、CPU 資源耗用量及記憶體使用量的不同 SAP 解決方案。 很可能不是那些所有的 SAP 系統都是以 SAP HANA 為基礎，因此您的 SAP 架構可能是使用下列各項的混合式架構：

- 部署在內部部署環境中的 SAP 系統。 由於其大小的緣故，這些系統目前無法裝載於 Azure 中；典型的例子是在 Microsoft SQL Server (作為資料庫) 上執行的生產環境 SAP ERP 系統，其需要 Azure VM 能夠提供更多的 CPU 和記憶體。
- 部署在內部部署環境中以 SAP HANA 為基礎的 SAP 系統。
- 部署在 Azure VM 中的 SAP 系統。 根據資源耗用量與記憶體需求，這些系統可以是任何可在 Azure (在 VM 上) 順利部署之 SAP NetWeaver 型應用程式的開發、測試、沙箱或生產環境執行個體。 這些系統也可利用資料庫為根據，例如 SQL Server (請參閱 [SAP 支援附註 #1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533/E) \(英文\)) 或 SAP HANA (請參閱 [SAP HANA 認證的 IaaS 平台](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) \(英文\))。
- 部署在 Azure (在 VM 上) 中並利用「Azure 大型執行個體」戳記中 SAP HANA on Azure (大型執行個體) 的 SAP 應用程式伺服器。

儘管混合式 SAP 架構 (含有四個以上的不同部署案例) 為典型架構，還是有許多在 Azure 中完成 SAP 架構執行的客戶案例。 由於 Microsoft Azure VM 的功能變得更強大，因此，在 Azure 上移動其所有 SAP 解決方案的客戶數目正逐漸增加。

在部署於 Azure 中之 SAP 系統內容中的 Azure 網路並不複雜。 它是根據下列原則：

- 「Azure 虛擬網路」(VNet) 必須連接到會連接到內部部署網路的 Azure ExpressRoute 線路。
- 將內部部署連線至 Azure 的 ExpressRoute 線路通常應有 1 Gbps 或更高的頻寬。 這個最低頻寬允許有足夠的頻寬，可供在內部部署系統與在 Azure VM 上執行的系統之間傳輸資料 (以及從內部部署的使用者連線到 Azure 系統)。
- Azure 中的所有 SAP 系統都必須在 Azure VNet 中設定妥當，才能彼此通訊。
- 裝載於內部部署環境中的 Active Directory 與 DNS 會透過 ExpressRoute 從內部部署環境延伸到 Azure。


> [!NOTE] 
> 從計費的角度來看，單一 Azure 訂用帳戶只能連結到特定 Azure 區域中某個大型執行個體戳記內的單一租用戶，相反地，單一大型執行個體戳記租用戶也只能連結到一個 Azure 訂用帳戶。 這項事實與 Azure 中任何其他可計費物件一樣

將 SAP HANA on Azure (大型執行個體) 部署在多個不同的 Azure 區域中，會導致在大型執行個體戳記中部署個別的租用戶。 不過，只要這些執行個體都是相同 SAP 架構的一部分，您便可以在同一個 Azure 訂用帳戶下執行兩者。 

> [!IMPORTANT] 
> 使用 SAP HANA on Azure (大型執行個體) 時，僅支援「Azure 資源管理」部署。

 

### <a name="additional-azure-vnet-information"></a>其他 Azure VNet 資訊

為了將 Azure VNet 連接到 ExpressRoute，必須建立 Azure 閘道 (請參閱[關於 ExpressRoute 的虛擬網路閘道](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 Azure 閘道可以與連到 Azure 外部基礎結構 (或連到「Azure 大型執行個體」戳記) 的 ExpressRoute 搭配使用，或用來在 Azure VNet 之間做連接 (請參閱[使用 PowerShell 設定 Resource Manager 的 VNet 對 VNet 連線](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 您可以將 Azure 閘道最多連線到四個不同的 ExpressRoute 連線，只要這些連線來自不同的 MS Enterprise Edges (MSEE) 路由器即可。  如需詳細資料，請參閱 [Azure 上 SAP HANA (大型執行個體) 的基礎結構和連接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> Azure 閘道為這兩個使用案例提供的輸送量是不同的 (請參閱[關於 VPN 閘道](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 在使用 ExpressRoute 連線的情況下，VNet 閘道可達到的最大輸送量是 10 Gbps。 請記住，在位於 Azure VNet 中的 Azure VM 與內部部署系統之間複製檔案 (以單一複製串流的形式) 並不會達到不同閘道 SKU 的最大輸送量。 若要利用 VNet 閘道的全部頻寬，您必須使用多個串流，或是以單一檔案的平行串流複製不同的檔案。


### <a name="networking-architecture-for-hana-large-instances"></a>適用於 HANA 大型執行個體的網路架構
適用於 HANA 大型執行個體的網路架構如下所示，可區分為四個不同的部分：

- 內部部署網路和連至 Azure 的 ExpressRoute 連線。 這個部分是客戶網域，並透過 ExpressRoute 連線到 Azure。 這是下圖中右下角的部分。
- Azure 網路，如同上述對於 Azure VNet 的簡短討論，再次提醒，其中具有閘道。 這是您需要針對應用程式需求、安全性及合規性需求尋找適當設計的區域。 使用 HANA 大型執行個體，是基於要從中選擇之 VNet 和 Azure 閘道 SKU 數目的另一個考量點。 這是下圖中右上角的部分。
- HANA 大型執行個體透過 ExpressRoute 技術連線至 Azure。 這部分已部署且會由 Microsoft 來處理。 身為客戶，您唯一要做的是提供一些 IP 位址範圍，然後在 HANA 大型執行個體中部署您的資產之後，將 ExpressRoute 線路連線至 Azure VNet (請參閱 [Azure 上 SAP HANA (大型執行個體) 的基礎結構和連接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 
- HANA 大型執行個體中的網路功能，對於身為客戶的您而言，大部分是透明的。

![連接到 SAP HANA on Azure (大型執行個體) 與內部部署環境的 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

您使用 HANA 大型執行個體的事實不會變更取得透過 ExpressRoute 連線至 Azure 之內部部署資產的需求。 它也不會變更具有一或多個執行 Azure VM 之 VNet 的需求，這些 VM 會裝載連線至裝載於 HANA 大型執行個體單位中之 HANA 執行個體的應用程式層。 

與在純 Azure 中部署 SAP 的差異隨附下列事實：

- 客戶租用戶的 HANA 大型執行個體單位會透過另一個 ExpressRoute 線路連線到您的 Azure VNet。 為了分隔負載狀況，內部部署到 Azure VNet ExpressRoute 連結以及 Azure VNet 與 HANA 大型執行個體之間的連結不會共用相同的路由器。
- SAP 應用程式層和 HANA 執行個體之間的工作負載設定檔是不同性質的許多小型要求與高載，例如從 SAP HANA 到應用程式層的資料傳輸 (結果集)。
- 相較於在內部部署與 Azure 之間交換資料的典型案例，SAP 應用程式架構對於網路延遲更敏感。
- VNet 閘道至少會有兩個 ExpressRoute 連線，而這兩個連線會共用 VNet 閘道之連入資料的最大頻寬。

在 Azure VM 和 HANA 大型執行個體單位之間經歷的網路延遲，可能大於典型的 VM 對 VM 網路來回延遲。 相依於 Azure 區域，測量到的值可超過 0.7 毫秒的來回延遲，這在 [SAP 附註 #1100926 - 常見問題集：網路效能](https://launchpad.support.sap.com/#/notes/1100926/E) \(英文\) 中已歸類為低於平均值。 不過，客戶很順利在 SAP HANA 大型執行個體上部署以 SAP HANA 為基礎的生產環境 SAP 應用程式。 部署的客戶藉由使用 HANA 大型執行個體單位，在 SAP HANA 上執行其 SAP 應用程式，回報表示有很顯著的改善。 不過，您應該在 Azure HANA 大型執行個體中徹底測試您的商務程序。
 
為了提供 Azure VM 和 HANA 大型執行個體之間具決定性的網路延遲，Azure VNet 閘道 SKU 的選擇是不可或缺的。 不同於內部部署與 Azure VM 之間的流量模式，Azure VM 和 HANA 大型執行個體之間的流量模式可以開發很小但高載的要求和資料磁碟區來進行傳輸。 為了能妥善處理這類高載，我們強烈建議使用 UltraPerformance 閘道 SKU。 針對類型 II 類別的 HANA 大型執行個體 SKU，使用 UltraPerformance 閘道 SKU 作為 Azure VNet 閘道是必要的。  

> [!IMPORTANT] 
> 考慮到 SAP 應用程式與資料庫層之間的整體網路流量，因此僅支援使用 VNet 的 HighPerformance 或 UltraPerformance 閘道 SKU 來連接到 SAP HANA on Azure (大型執行個體)。 針對 HANA 大型執行個體類型 II SKU，只支援使用 UltraPerformance 閘道 SKU 作為 Azure VNet 閘道。



### <a name="single-sap-system"></a>單一 SAP 系統

上面顯示的內部部署基礎結構是透過 ExpressRoute 連接到 Azure，而 ExpressRoute 線路則會連接到 Microsoft Enterprise Edge (MSEE) 路由器 (請參閱 [ExpressRoute 技術概觀](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 建立該路由之後，它會連接到 Microsoft Azure 骨幹，而讓所有 Azure 區域都可供存取。

> [!NOTE] 
> 若要在 Azure 中執行 SAP 架構，請連接到距離 SAP 架構中的 Azure 區域最近的 MSEE。 「Azure 大型執行個體」戳記是透過專用 MSEE 裝置連接，可將 Azure IaaS 中的 Azure VM 與「大型執行個體」戳記之間的網路延遲降到最低。

Azure VM (裝載 SAP 應用程式執行個體) 的 VNet 閘道會連接到該 ExpressRoute 線路，而該相同 VNet 會連接到專門用來連接到「大型執行個體」戳記的個別 MSEE 路由器。

這是一個單一 SAP 系統的簡單範例，其中 SAP 應用程式層是裝載在 Azure 中，而 SAP HANA 資料庫則是在 SAP HANA on Azure (大型執行個體) 上執行。 假設 VNet 閘道頻寬 2 Gbps 或 10 Gbps 輸送量並不代表瓶頸。

### <a name="multiple-sap-systems-or-large-sap-systems"></a>多個 SAP 系統或大型 SAP 系統

如果將多個 SAP 系統或大型 SAP 系統部署成連線到 SAP HANA on Azure (大型執行個體)，就可以合理假設 VNet 閘道的輸送量可能成為瓶頸。 在這種情況下，您需要將應用程式層分成多個 Azure VNet。 在類似以下的情況下，建立連接到「HANA 大型執行個體」的特殊 VNet 也可能是建議的做法：

- 直接從「HANA 大型執行個體」中的「HANA 執行個體」備份到 Azure 中裝載 NFS 共用的 VM
- 將大型備份或其他檔案從「HANA 大型執行個體」單位複製到在 Azure 中管理的磁碟空間。

使用個別的 VNet 來裝載管理儲存體的 VM，可避免在透過 VNet 閘道 (此閘道可為執行 SAP 應用程式層的 VM 提供服務) 將大型檔案或資料從 HANA 大型執行個體傳輸到 Azure 時所帶來的影響。 

讓網路架構更具彈性：

- 針對單一的較大 SAP 應用程式層，運用多個 Azure VNet。
- 相較於將所部署的 SAP 系統結合在相同 VNet 下的個別子網路中，為這些 SAP 系統中的每一個系統部署一個個別的 Azure VNet。

 適用於 SAP HANA on Azure (大型執行個體) 的更有彈性網路架構：

![跨多個 Azure VNet 部署 SAP 應用程式層](./media/hana-overview-architecture/image4-networking-architecture.png)

如上面所示，跨多個 Azure VNet 部署 SAP 應用程式層或元件時，會在這些 Azure VNet 所裝載的應用程式之間進行通訊的期間，產生無法避免的延遲額外負荷。 根據預設，在此設定中，位於不同 VNet 中的 Azure VM 之間的網路流量會透過 MSEE 路由器路由傳送。 不過，自 2016 年 9 月起，可將這個路由傳送最佳化。 若要最佳化並縮短兩個 VNet 之間的通訊延遲，做法是將相同區域內的 Azure VNet 對等互連。 即使那些 VNet 在不同的訂用帳戶中也一樣。 如果使用 Azure VNet 對等互連，兩個不同 Azure VNet 中 VM 間的通訊就可以使用 Azure 網路骨幹來彼此直接通訊。 因此，所顯示的延遲會類似於 VM 就位於相同 VNet 中一樣。 反之，以透過 Azure VNet 閘道器連線的 IP 位址範圍來定址的流量，將透過 VNet 的個別 VNet 閘道器進行路由傳送。 您可以在 [VNet 對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)一文中取得 Azure VNet 對等互連的詳細資訊。


### <a name="routing-in-azure"></a>Azure 中的路由

SAP HANA on Azure (大型執行個體) 有兩個重要的網路路由考量：

1. SAP HANA on Azure (大型執行個體) 僅供 Azure VM 以專用 ExpressRoute 連線存取；無法從內部部署環境直接存取。 有些系統管理用戶端及所有需要直接存取權的應用程式 (例如在內部部署環境中執行的 SAP Solution Manager) 會無法連接到 SAP HANA 資料庫。

2. SAP HANA on Azure (大型執行個體) 單位有一個指派的 IP 位址，此位址來自身為客戶的您所提交的「伺服器 IP 集區」位址範圍 (如需詳細資料，請參閱 [Azure 上 SAP HANA (大型執行個體) 的基礎結構和連接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。  透過 Azure 訂用帳戶和將 Azure VNet 連線到 HANA on Azure (大型執行個體) 的 ExpressRoute，即可存取此 IP 位址。 從該「伺服器 IP 集區」位址範圍指派的 IP 位址會直接指派給硬體單位，且「不再」經過 NAT 處理，因為這是此解決方案的第一次部署才會有的情況。 

> [!NOTE] 
> 如果在「資料倉儲」案例中您需要連接到 SAP HANA on Azure (大型執行個體)，其中應用程式和 (或) 使用者需要連接到 SAP HANA 資料庫 (直接執行)，您就必須使用另一個網路功能元件：一個可將資料正反方向路由傳送的反向 Proxy。 例如，搭配部署在 Azure 中作為虛擬防火牆/流量路由解決方案之「流量管理員」的 F5 BIG-IP、NGINX。

### <a name="internet-connectivity-of-hana-large-instances"></a>HANA 大型執行個體的網際網路連線
「HANA 大型執行個體」無法直接連線到網際網路。 這會限制您的一些能力，例如直接向 OS 廠商註冊 OS 映像。 因此，您可能需要與本機 SLES SMT 伺服器或 RHEL Subscription Manager 搭配運作

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Azure VM 與 HANA 大型執行個體之間的資料加密
「HANA 大型執行個體」與 Azure VM 之間的資料傳輸並未加密。 不過，您可以純粹針對 HANA DBMS 端與 JDBC/ODBC 型應用程式之間的交換，啟用流量加密。 請參考 [SAP 提供的這份文件](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false) \(英文\)

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>在多個區域中使用 HANA 大型執行個體單位

除了災害復原之外，您可能還有其他理由促使您在多個 Azure 區域中部署 SAP HANA on Azure (大型執行個體)。 也許您想要從部署在這些區域之不同 VNet 中的每個 VM 存取「HANA 大型執行個體」。 由於指派給不同「HANA 大型執行個體」單位的 IP 位址不會傳播到 Azure VNet (這些 VNet 是透過其閘道直接連接到執行個體) 之外，因此對上面介紹的 VNet 設計有些微變更：Azure VNet 閘道可以處理四個來自不同 MSEE 的不同 ExpressRoute 線路，而每個連接到其中一個「大型執行個體」戳記的 VNet 都可連接到另一個 Azure 區域中的「大型執行個體」戳記。

![連接到不同 Azure 區域中「Azure 大型執行個體」戳記的 Azure VNet](./media/hana-overview-architecture/image8-multiple-regions.png)

上圖顯示兩個區域中的不同 Azure VNet 如何連接到兩個不同的 ExpressRoute 線路，而這些線路是用來連接到該兩個 Azure 區域中的 SAP HANA on Azure (大型執行個體)。 新導入的連線是矩形的紅線。 有了這些來自 Azure VNet 的連線，在那其中一個 VNet 中執行的 VM 便可存取部署在這兩個區域中每一個不同的 HANA 大型執行個體單位。 如上圖所示，這是假設您有兩條從內部部署環境連到該兩個 Azure 區域的 ExpressRoute 連線；如果是為了「災害復原」，則建議使用此做法。

> [!IMPORTANT] 
> 如果使用多個 ExpressRoute 線路，就應該在前面加上「AS 路徑」和使用「本機喜好 BGP」設定來確保流量路由正確。


