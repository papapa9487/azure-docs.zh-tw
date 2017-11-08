---
title: "SAP HANA on Azure (大型執行個體) 的基礎結構和連線 | Microsoft Docs"
description: "設定必要的連線基礎結構以使用 SAP HANA on Azure (大型執行個體)。"
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
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7a44fdbfb973d75c21aa87e9b9d0eea8fb2b3392
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Azure 上 SAP HANA (大型執行個體) 的基礎結構和連接 

閱讀本指南之前請先了解某些預先定義。 我們在 [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中引進了兩種不同類別的 HANA 大型執行個體單位：

- S72、S72m、S144、S144m、S192 和 S192m，稱之為「類型 I 類別」的 SKU。
- S384、S384m、S384xm、S576、S768 和 S960，稱之為「類型 II 類別」的 SKU。

在整個 HANA 大型執行個體文件中都會使用類別規範，最終以 HANA 大型執行個體 SKU 為基礎指向不同的功能和需求。

其他常用定義包括：
- **大型執行個體戳記︰**經 SAP HANA TDI 認證並專門用來執行 Azure 內 SAP HANA 執行個體的硬體基礎結構堆疊。
- **SAP HANA on Azure (大型執行個體)：**Azure 中產品方案的正式名稱，此產品方案可在經 SAP HANA TDI 認證並部署在不同 Azure 區域之「大型執行個體」戳記中的硬體上執行 HANA 執行個體。 **HANA 大型執行個體**是 SAP HANA on Azure (大型執行個體) 的相關詞彙簡稱，並在本技術部署指南中廣泛使用。
 

在您與 Microsoft 企業帳戶小組之間的 SAP HANA on Azure (大型執行個體) 購買程序完成之後，Microsoft 需要下列資訊才能部署「HANA 大型執行個體單位」：

- 客戶名稱
- 業務連絡人資訊 (包括電子郵件地址和電話號碼)
- 技術連絡人資訊 (包括電子郵件地址和電話號碼)
- 技術網路連絡人資訊 (包括電子郵件地址和電話號碼)
- Azure 部署區域 (自 2017 年 7 月起：美國西部、美國東部、澳大利亞東部、澳大力亞東南部、西歐和北歐)
- 確認 SAP HANA on Azure (大型執行個體) SKU (組態)
- 如「HANA 大型執行個體」的＜概觀與架構＞文件中所述，針對每個作為部署目的地的「Azure 區域」：
    - 將 Azure VNet 連接到「HANA 大型執行個體」之 ER-P2P 連線的 /29 IP 位址
    - 用於「HANA 大型執行個體伺服器 IP 集區」的 /24 CIDR 區塊
- 每個連接到 HANA 大型執行個體之 Azure VNet 的 Vnet 位址空間屬性中所使用的 IP 位址範圍值
- 每個「HANA 大型執行個體」系統的資料：
  - 所需的主機名稱 - 最好是完整的網域名稱。
  - 來自伺服器 IP 集區位址範圍的所需 HANA 大型執行個體單位 IP 位址 - 請記住，伺服器 IP 集區位址範圍中的前 30 個 IP 位址，是保留給在 HANA 大型執行個體中供內部使用的 IP 位址。
  - SAP HANA 執行個體的 SAP HANA SID 名稱 (這是建立必要 SAP HANA 相關磁碟區所需的名稱)。 在 NFS 磁碟區上建立 <sidadm> 權限需要有 HANA SID，這些磁碟區會附加至 HANA 大型執行個體單位。 也會用做為掛接磁碟區的其中一個名稱元件。 如果您想要在單位上執行多個 HANA 執行個體，您需要列出多個 HANA SID。 每一個都會分別指派一組磁碟區。
  - 必須要有 hana-sidadm 使用者在 Linux OS 中所擁有的 groupid，才能建立必要的 SAP HANA 相關磁碟區。 SAP HANA 安裝通常會建立群組識別碼為 1001 的 sapsys 群組。 hana-sidadm 使用者為該群組的成員
  - 必須要有 hana-sidadm 使用者在 Linux OS 中所擁有的 userid，才能建立必要的 SAP HANA 相關磁碟區。 如果要在單位上執行多個 HANA 執行個體，您需要列出所有的 <sid> adm 使用者。 
- Azure HANA 大型執行個體上 SAP HANA 要直接連接的 Azure 訂用帳戶的 Azure 訂用帳戶識別碼。 此訂用帳戶識別碼參考 Azure 訂用帳戶，會要支付 HANA 大型執行個體單位的費用。

在您提供資訊之後，Microsoft 就會佈建 SAP HANA on Azure (大型執行個體)，並且會傳回將 Azure VNet 連接到「HANA 大型執行個體」及存取「HANA 大型執行個體」單位所需的資訊。

## <a name="connecting-azure-vms-to-hana-large-instances"></a>將 Azure VM 連接到 HANA 大型執行個體

如 [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中所述，Azure 中搭配 SAP 應用程式層的「HANA 大型執行個體」最基本部署看起來如下：

![連接到 SAP HANA on Azure (大型執行個體) 與內部部署環境的 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

更仔細審視 Azure VNet 端之後，我們發現有下列需求：

- Azure VNet 的定義，SAP 應用程式層 VM 會部署在此。
- 這自動意謂著已在 Azure Vnet 中定義真正用來作為 VM 部署目的地的預設子網路。
- 所建立的 Azure VNet 必須至少有一個 VM 子網路和一個「ExpressRoute 閘道」子網路。 您應該依下列各節所指定及討論的，將 IP 位址範圍指派給這些子網路。

因此，讓我們更進一步了解如何為「HANA 大型執行個體」建立 Azure VNet

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>為 HANA 大型執行個體建立 Azure VNet

>[!Note]
>您必須使用 Azure Resource Manager 部署模型來為「HANA 大型執行個體」建立 Azure VNet。 HANA 大型執行個體解決方案不支援舊版的 Azure 部署模型 (通常稱為傳統部署模型)。

您可以使用 Azure 入口網站、PowerShell、Azure 範本或 Azure CLI 來建立 VNet (請參閱[使用 Azure 入口網站建立虛擬網路](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 在下例中，我們將探討透過 Azure 入口網站建立的 VNet。

如果要透過 Azure 入口網站來探討 Azure VNet 的定義，讓我們來研究一下其中的某些定義，以及這些定義與我們列出的不同 IP 位址範圍如何關聯。 當我們提到**位址空間**時，指的是 Azure VNet 獲允許使用的位址空間。 此位址空間也是 VNet 用來傳播 BGP 路由的位址範圍。 您可以在下列位置看到此「位址空間」：

![Azure 入口網站中顯示的 Azure VNet 位址空間](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

在前例中，使用了 10.16.0.0/16 來賦予 Azure VNet 更大、更廣的可用 IP 位址範圍。 這意謂著此 VNet 內後續子網路之所有 IP 位址範圍的範圍都可在該「位址空間」內。 通常針對 Azure 中的單一 VNet，我們並不建議使用這麼大的位址範圍。 但為了進一步了解，讓我們來研究一下 Azure VNet 中定義的子網路：

![Azure VNet 子網路及其 IP 位址範圍](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

如您所見，我們看到的是含有第一個 VM 子網路 (這裡稱為 'default') 和稱為 'GatewaySubnet' 之子網路的 VNet。
在下節中，我們將圖中名為 'default' 之子網路的 IP 位址範圍稱為 **Azure VM 子網路 IP 位址範圍**。 在接下來的小節中，我們將「閘道子網路」(Gateway Subnet) 的 IP 位址範圍稱為「VNet 閘道子網路 IP 位址範圍」。 

在上述兩張圖所示範的案例中，您會看到「VNet 位址空間」同時涵蓋「Azure VM 子網路 IP 位址範圍」和「VNet 閘道子網路 IP 位址範圍」。 

在您需要節約使用或有明確 IP 位址範圍的其他案例中，您可以將 VNet 的 **VNet 位址空間**限制成每個子網路所使用的特定範圍。 在此情況下，您可以將 VNet 的「VNet 位址空間」定義成多個特定範圍，如以下所示：

![含有兩個空間的「Azure VNet 位址空間」](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

在本例中，**VNet 位址空間**定義了兩個空間。 這兩個空間與為 **Azure VM 子網路 IP 位址範圍**和 **VNet 閘道子網路 IP 位址範圍**定義的 IP 位址範圍相同。

您可以將您喜歡的任何命名標準用於這些租用戶子網路 (VM 子網路)。 不過，**每個 VNet 都必須要有、且只有一個閘道子網路**連接至 Azure (大型執行個體) ExpressRoute 線路上的 SAP HANA。 而且，**這個閘道子網路必須一律名為 "GatewaySubnet"**，以確保正確放置 ExpressRoute 閘道。

> [!WARNING] 
> 將閘道子網路一律命名為 "GatewaySubnet" 非常重要。

您可以使用多個 VM 子網路，甚至是使用非連續的位址範圍。 但如先前所述，VNet 的「VNet 位址空間」必須涵蓋這些位址範圍，不論是以彙總形式涵蓋，還是以 VM 子網路與閘道子網路的精確範圍清單涵蓋。

總結連接到「HANA 大型執行個體」之 Azure VNet 的重要事實：

- 執行 HANA 大型執行個體初始部署時，您需要向 Microsoft 提交 **VNet 位址空間**。 
- 「VNet 位址空間」可以是一個涵蓋「Azure VM 子網路 IP 位址範圍」和「VNet 閘道子網路 IP 位址範圍」之範圍的較大範圍。
- 或者，您可以用「VNet 位址空間」多重範圍的形式提交，這些範圍涵蓋「VM 子網路 IP 位址範圍」和「VNet 閘道子網路 IP 位址範圍」的不同 IP 位址範圍。
- 已定義的「VNet 位址空間」會用來傳播 BGP 路由。
- 閘道子網路的名稱必須是：**"GatewaySubnet"**。
- 在「HANA 大型執行個體」端會使用「VNet 位址空間」作為篩選條件，以允許或拒絕從 Azure 到「HANA 大型執行個體」單位的流量。 如果 Azure VNet 的 BGP 路由資訊與針對在 HANA 大型執行個體端上進行篩選而設定的 IP 位址範圍不符，就會發生連線問題。
- ＜將 VNet 連接到 HANA 大型執行個體 ExpressRoute＞一節將進一步討論閘道子網路的一些相關詳細資料



### <a name="different-ip-address-ranges-to-be-defined"></a>要定義的不同 IP 位址範圍 

前幾節中已介紹一些部署 HANA 大型執行個體所需的 IP 位址範圍。 但仍有一些其他重要的 IP 位址。 讓我們來瀏覽一些進一步的詳細資料。 傳送初始部署要求之前，必須定義下列 IP 位址，這些 IP 位址並非全都必須提交給 Microsoft：

- **VNet 位址空間：**如前文所介紹，這是您在連接到 SAP HANA 大型執行個體環境的 Azure 虛擬網路 (VNet) 中，已指派 (或打算指派) 給位址空間參數的 IP 位址範圍。 建議此位址空間參數採用由 Azure VM 子網路範圍和 Azure 閘道子網路範圍所組成的多行值，如前文圖中所示。 此範圍「不得」與您的內部部署或伺服器 IP 集區或 ER-P2P 位址範圍重疊。 如何取得 IP 位址範圍？ 您的公司網路小組或服務提供者應該提供一或多個網路內未使用的 IP 位址範圍。 範例：如果您的 Azure VM 子網路 (參見前文) 是 10.0.1.0/24，而 Azure 閘道子網路 (參見後方) 是 10.0.2.0/28，則建議 Azure VNet 位址空間採用兩行，即 10.0.1.0/24 和 10.0.2.0/28。 雖然位址空間值可以彙總，但建議讓它們符合子網路範圍，以避免日後在網路中的其他地方，不小心重複使用在較大位址範圍內未使用的 IP 位址範圍。 **VNET 位址範圍是要求初始部署時，所必須提交給 Microsoft 的 IP 位址範圍**

- **Azure VM 子網路 IP 位址空間：**如前文所述，這是您在連接到 SAP HANA 大型執行個體環境的 Azure VNET 中，已指派 (或打算指派) 給 Azure VNet 子網路參數的 IP 位址範圍。 此 IP 位址範圍可用來將 IP 位址指派給您的 Azure VM。 超出此範圍的 IP 位址可以連接到您的 SAP HANA 大型執行個體伺服器。 如果需要，可以使用多個 Azure VM 子網路。 Microsoft 建議為每個「Azure VM 子網路」指定 /24 CIDR 區塊。 此位址範圍必須是「Azure VNet 位址空間」中所使用值的一部分。 如何取得此 IP 位址範圍？ 您的公司網路小組或服務提供者應該提供一個目前網路內未使用的 IP 位址範圍。

- **VNet 閘道子網路 IP 位址範圍︰**根據您打算使用的功能，建議的大小為︰
   - 強效 ExpressRoute 閘道：/26 位址區塊 - 類型 II 類別的 SKU
   - 與使用高效能 ExpressRoute 閘道 (或更小) 的 VPN 和 ExpressRoute 共存：/27 位址區塊
   - 所有其他情況：/28 位址區塊。 此位址範圍必須是「VNet 位址空間」值中所使用值的一部分。 此位址範圍必須是您需要提交給 Microsoft 之「Azure VNet 位址空間」值中所使用值的一部分。 如何取得此 IP 位址範圍？ 您的公司網路小組或服務提供者應該提供一個目前網路內未使用的 IP 位址範圍。 

- **ER-P2P 連線的位址範圍：**這是您 SAP HANA 大型執行個體 ExpressRoute (ER) P2P 連線的 IP 範圍。 此 IP 位址範圍必須是 /29 CIDR IP 位址範圍。 此範圍「不得」與您的內部部署或其他 Azure IP 位址範圍重疊。 此 IP 位址可用來設定從 Azure VNet ExpressRoute 閘道到 SAP HANA 大型執行個體伺服器的 ER 連線。 如何取得此 IP 位址範圍？ 您的公司網路小組或服務提供者應該提供一個目前網路內未使用的 IP 位址範圍。 **此範圍是要求初始部署時，需要提交給 Microsoft 的 IP 位址範圍**
  
- **伺服器 IP 集區位址範圍︰**此 IP 位址範圍可用來將個別 IP 位址指派給 HANA 大型執行個體伺服器。 建議的子網路大小是 /24 CIDR 區塊 - 但如果需要，它也可以小到最小僅提供 64 個 IP 位址。 在這個範圍中，前 30 個 IP 位址要保留供 Microsoft 使用。 選擇範圍的大小時，請確定已將此實況納入考量。 此範圍「不得」與您的內部部署或其他 Azure IP 位址重疊。 如何取得此 IP 位址範圍？ 您的公司網路小組或服務提供者應該提供一個目前網路內未使用的「IP 位址範圍」。 要用於指派 SAP HANA on Azure (大型執行個體) 所需之特定 IP 位址的 /24 (建議使用) 唯一 CIDR 區塊。 **此範圍是要求初始部署時，需要提交給 Microsoft 的 IP 位址範圍**
 
雖然您需要定義和規劃上述的 IP 位址範圍，但並非全都需要傳送給 Microsoft。 總結以上所述，您必須列舉給 Microsoft 的 IP 位址範圍包括：

- Azure VNet 位址空間
- 用於 ER-P2P 連線的位址範圍
- 伺服器 IP 集區位址範圍

若要新增其他需要連接到「HANA 大型執行個體」的 VNet，您必須將新的「Azure VNet 位址空間」提交給 Microsoft。 

以下是不同範圍的範例，以及一些您需要設定並最後提供給 Microsoft 的範例範圍。 如您所見，在第一個範例中，Azure VNet 位址空間的值並未經過彙總，而是從第一個 Azure VM 子網路 IP位址範圍和 VNet 閘道子網路 IP 位址範圍的範圍定義而來。 在 Azure VNet 內使用多個 VM 子網路時，會根據隨「Azure VNet 位址空間」一併設定並提交之額外 VM 子網路的額外 IP 位址範圍運作。

![SAP HANA on Azure (大型執行個體) 最基本部署中所需的 IP 位址範圍](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

您也可以彙總要提交給 Microsoft 的資料。 在該情況下，Azure VNet 的位址空間只會包含一個空間。 使用前例中所使用的 IP 位址範圍。 此彙總的 VNet 位址空間可能看起來像這樣：

![SAP HANA on Azure (大型執行個體) 最基本部署中所需 IP 位址範圍的第二個可能性](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

如您在上面所見，我們並未採用兩個定義 Azure VNet 位址空間的較小範圍，而是採用一個涵蓋 4096 個 IP 位址範圍的較大範圍。 定義這樣大的「位址空間」會使得相當大的一些範圍沒有被使用。 由於「VNet 位址空間」值會用來傳播 BGP 路由，因此使用內部部署環境或您網路中其他地方未使用的範圍會造成路由問題。 所以，建議讓「位址空間」與所使用的實際子網路位址空間保持完全一致。 您一律可以在稍後視需要新增新的位址空間值，這不會導致 VNet 停機。
 
> [!IMPORTANT] 
> ER-P2P、伺服器 IP 集區、Azure VNet 位址空間的每個 IP 位址都「不得」彼此重疊或與網路上其他地方使用的任何其他範圍重疊；每個 IP 必須是個別的，並且如前兩圖所示，不是任何其他範圍的子網路。 如果兩個範圍之間發生重疊，Azure VNet 可能無法連接到 ExpressRoute 線路。

### <a name="next-steps-after-address-ranges-have-been-defined"></a>定義位址範圍之後的後續步驟
定義 IP 位址範圍之後，需要進行下列活動：

1. 將用於「Azure VNet 位址空間」、ER-P2P 連線及「伺服器 IP 集區位址範圍」的 IP 位址範圍與本文開頭所列其他資料一起提交。 此時，您也可以開始建立 VNet 和 VM 子網路。 
2. 由 Microsoft 在您的 Azure 訂用帳戶與「HANA 大型執行個體」戳記之間建立 ExpressRoute 線路。
3. 由 Microsoft 在「大型執行個體」戳記上建立租用戶網路。
4. Microsoft 會設定 SAP HANA on Azure (大型執行個體) 基礎結構中的網路功能，以接受來自您 Azure VNet 位址空間之將與 HANA 大型執行個體通訊的 IP 位址。
5. 依據所購買的特定 SAP HANA on Azure (大型執行個體) SKU，Microsoft 會在租用戶網路中指派一個計算單位、配置並掛接儲存體，以及安裝作業系統 (SUSE 或 Red Hat Linux)。 這些單位的 IP 位址是取自您提交給 Microsoft 的「伺服器 IP 集區位址範圍」。

在部署程序結束時，Microsoft 會將下列資料傳送給您：
- 將您的 Azure VNet 連接到 ExpressRoute 線路 (此線路會將 Azure VNet 連接到「HANA 大型執行個體」) 所需的資訊：
     - 授權金鑰
     - ExpressRoute PeerID
- 在您建立 ExpressRoute 線路和 Azure VNet 之後，用以存取「HANA 大型執行個體」的資料。

您也可以在 [SAP HANA 大型執行個體的端對端安裝](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/)一文中，找到連接 HANA 大型執行個體的序列。 該文件的範例部署會示範下列許多步驟。 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>將 VNet 連接到 HANA 大型執行個體 ExpressRoute

由於您已定義所有 IP 位址範圍，並且現在已取得 Microsoft 傳回的資料，因此可以開始將以前建立的 VNet 連接到「HANA 大型執行個體」。 建立 Azure VNet 之後，必須在該 VNet 上建立 ExpressRoute 閘道，以將 VNet 連結到會連接到「大型執行個體」戳記上客戶租用戶的 ExpressRoute 線路。

> [!NOTE] 
> 此步驟最多可能需要 30 分鐘才能完成，因為會在指定的 Azure 訂用帳戶中建立新閘道，然後連接到指定的 Azure VNet。

如果閘道已經存在，請檢查它是否是 ExpressRoute 閘道。 如果不是，就必須刪除該閘道，然後重新建立成 ExpressRoute 閘道。 如果已經建立 ExpressRoute 閘道，則由於 Azure VNet 已經連接到用於內部部署環境連線的 ExpressRoute 線路，因此請繼續進行下面的＜連結 VNet＞小節。

- 使用 (新版) [Azure 入口網站](https://portal.azure.com/) 或 PowerShell 來建立連接到您 VNet 的 ExpressRoute VPN 閘道。
  - 如果您使用 Azure 入口網站，請新增新的 [虛擬網路閘道]，然後選取 [ExpressRoute] 作為閘道類型。
  - 如果您改為選擇 PowerShell，則請先下載並使用 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)，以確保獲得最佳體驗。 下列命令會建立 ExpressRoute 閘道。 前面有 _$_ 的文字是使用者定義的變數，必須更新成您的特定資訊。

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

在此範例中，使用了 HighPerformance 閘道 SKU。 您的選項為 HighPerformance 或 UltraPerformance，因為 SAP HANA on Azure (大型執行個體) 僅支援這些閘道 SKU。

> [!IMPORTANT]
> 若為 SKU 類型 S384、S384m、S384xm、S576、S768 和 S960 (類型 II 類別的 SKU) 的 HANA 大型執行個體，強制使用強效閘道 SKU。

### <a name="linking-vnets"></a>連結 VNet

既然 Azure VNet 已有 ExpressRoute 閘道，您便可以使用 Microsoft 所提供的授權資訊，將 ExpressRoute 閘道連接到為此連線建立的 SAP HANA on Azure (大型執行個體) ExpressRoute 線路。 您可以使用 Azure 入口網站或 PowerShell 來執行這個步驟。 建議使用入口網站，不過，以下提供 PowerShell 指示。 

- 您需針對每個連線使用不同的 AuthGUID，來為每個 VNet 閘道執行下列命令。 下列指令碼顯示的前兩個項目來自 Microsoft 所提供的資訊。 此外，AuthGUID 對每個 VNet 及其閘道來說都是特定的。 這意謂著如果您想要新增另一個 Azure VNet，就必須為將 HANA 大型執行個體連接到 Azure 的 ExpressRoute 線路取得另外一個 AuthID。 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

如果您想要將閘道連接到與訂用帳戶關聯的多個 ExpressRoute 線路，可能會需要執行此步驟多次。 例如，您可能要將相同的 VNet 閘道連接到 ExpressRoute 線路，而此線路會將 VNet 連接到內部部署網路。

## <a name="adding-more-ip-addresses-or-subnets"></a>新增更多 IP 位址或子網路

新增更多 IP 位址或子網路時，您可以使用 Azure 入口網站、PowerShell 或 CLI。

在此情況下，建議您將新的 IP 位址範圍新增到 VNet 位址空間中作為新的範圍，而不要產生新的彙總範圍。 不論是哪一種情況，您都必須向 Microsoft 提交這項變更，以允許從該新 IP 位址範圍連線到您用戶端中的「HANA 大型執行個體」單位。 您可以開啟 Azure 支援要求，以新增新的 VNet 位址空間。 收到確認之後，請執行後續步驟。

若要從 Azure 入口網站建立額外的子網路，請參閱[使用 Azure 入口網站建立虛擬網路](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文，若要從 PowerShell 建立，則請參閱[使用 PowerShell 建立虛擬網路](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="adding-vnets"></a>新增 VNet

在一開始連接到一或多個 Azure VNet 之後，您可能會想要新增可存取 SAP HANA on Azure (大型執行個體) 的額外 VNet。 首先，請提交 Azure 支援要求，在該要求中同時包含識別特定 Azure 部署的具體資訊，以及「Azure VNet 位址空間」的 IP 位址空間範圍。 接著，「SAP HANA on Azure 服務管理」就會提供您連接額外 VNet 和 ExpressRoute.所需的必要資訊。 針對每個 VNet，您將需要一個唯一的授權金鑰，才能連接到 ExpressRoute 線路，再連接到 HANA 大型執行個體。

新增新 Azure VNet 的步驟：

1. 完成上架程序中的第一個步驟，請參閱_建立 Azure VNet_ 一節。
2. 完成上架程序中的第二個步驟，請參閱_建立閘道子網路_一節。
3. 使用新 VNet 的相關資訊來開啟 Azure 支援要求，並要求一個新的授權金鑰以將額外的 VNet 連接到 HANA 大型執行個體 ExpressRoute 線路。
4. 收到授權完成的通知之後，請使用 Microsoft 提供的授權資訊來完成上架程序中的第三個步驟，請參閱_連結 VNet_ 一節。

## <a name="increasing-expressroute-circuit-bandwidth"></a>增加 ExpressRoute 線路頻寬

請洽詢「SAP HANA on Azure 服務管理」。 如果系統建議您增加 SAP HANA on Azure (大型執行個體) ExpressRoute 線路的頻寬，請建立 Azure 支援要求。 (針對單一線路頻寬，您最多可以要求增加到 10 Gbps)。作業完成後，您便會收到通知；不需要進行任何額外動作，即可在 Azure 中啟用這個較高的速度。

## <a name="adding-an-additional-expressroute-circuit"></a>新增額外的 ExpressRoute 線路

如果系統告知您需要額外的 ExpressRoute 線路，請洽詢「SAP HANA on Azure 服務管理」，請提出 Azure 支援要求來建立新的 ExpressRoute 線路 (並取得可連線到此線路的授權資訊)。 在提出要求之前，必須先定義將在 VNet 上使用的位址空間，如此 SAP HANA on Azure 服務管理才能提供授權。

建立新線路且 SAP HANA on Azure 服務管理設定完成之後，您將會收到通知，當中含有您繼續作業所需的資訊。 請依照上面提供的步驟來建立新 VNet 並將其連接到這個額外的線路。 如果 Azure VNet 已經連接到同一 Azure 區域的另一個 SAP HANA on Azure (大型執行個體) ExpressRoute 線路，您就無法將它們連接到這個額外的線路。

## <a name="deleting-a-subnet"></a>刪除子網路

若要移除 VNet 子網路，您可以使用 Azure 入口網站、PowerShell 或 CLI。 如果您的 Azure VNet IP 位址範圍/Azure VNet 位址空間是一個彙總的範圍，後續就沒有需要追蹤 Microsoft 的事項。 唯一的例外是，VNet 仍然會傳播包含已刪除之子網路的 BGP 路由位址空間。 如果您將 Azure VNet IP 位址範圍/Azure VNet 位址空間定義成多個 IP 位址範圍，而其中一個已指派給已刪除的子網路，則您應該從「VNet 位址空間」中刪除該範圍，並接著通知「SAP HANA on Azure 服務管理」將它從允許 SAP HANA on Azure (大型執行個體) 進行通訊的範圍中移除。

雖然尚未有關於移除子網路的特定、專用 Azure.com 指導方針，但移除子網路的程序就是新增子網路的相反程序。 如需建立子網路的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文。

## <a name="deleting-a-vnet"></a>刪除 VNet

刪除 VNet 時，您可以使用 Azure 入口網站、PowerShell 或 CLI。 「SAP HANA on Azure 服務管理」會移除 SAP HANA on Azure (大型執行個體) ExpressRoute 線路上的現有授權，並移除用來與「HANA 大型執行個體」通訊的 Azure VNet IP 位址範圍/Azure VNet 位址空間。

移除 VNet 之後，請開啟 Azure 支援要求來提供要移除的 IP 位址空間範圍。

雖然尚未有關於移除 VNet 的特定、專用 Azure.com 指導方針，但移除 VNet 的程序就是新增 VNet (已在上面敘述) 的相反程序。 如需有關建立 VNet 的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文和[使用 PowerShell 入口網站建立虛擬網路](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文。

為了確保移除所有項目，請刪除下列項目：

- ExpressRoute 連線、VNet 閘道、VNet 閘道公用 IP 及 VNet

## <a name="deleting-an-expressroute-circuit"></a>刪除 ExpressRoute 線路

若要移除額外的 SAP HANA on Azure (大型執行個體) ExpressRoute 線路，請向 SAP HANA on Azure 服務管理開啟 Azure 支援要求，然後要求務必刪除該線路。 您可以視需要在 Azure 訂用帳戶內刪除或保留 VNet。 不過，您必須將「HANA 大型執行個體」ExpressRoute 線路與所連結 VNet 閘道之間的連線刪除。

如果您也想要移除 VNet，請依照上面＜刪除 VNet＞一節中的指導方針操作。


