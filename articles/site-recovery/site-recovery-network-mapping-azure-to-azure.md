---
title: "Azure Site Recovery 之中兩個 Azure 區域間的網路對應 | Microsoft Docs"
description: "Azure Site Recovery 可協調虛擬機器和實體伺服器的複寫、容錯移轉及復原作業。 了解如何容錯移轉到 Azure 或次要資料中心。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: 9d6a806ec533259797080fbfee2c38f918ebd8a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>兩個 Azure 區域間的網路對應


本文說明如何彼此對應兩個 Azure 區域的 Azure 虛擬網路。 網路對應可確保在目標 Azure 區域中建立複寫的虛擬機器時，將在與來源虛擬機器之虛擬網路對應的虛擬網路上建立。  

## <a name="prerequisites"></a>必要條件
對應網路之前，請確定您已在來源和目標 Azure 區域中建立 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。

## <a name="map-networks"></a>對應網路

若要將一個 Azure 區域中的 Azure 虛擬網路對應至另一個地區中的另一個虛擬網路，請移至 [Site Recovery 基礎結構] -> [網路對應] \(適用於 Azure 虛擬機器)，並建立網路對應。

![網路對應](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


在下列範例中，我的虛擬機器在東亞地區執行，而且正在複寫至東南亞。

選取來源和目標網路，然後按一下確定 建立從東亞到東南亞的網路對應。

![網路對應](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


以同樣的方式建議從東南亞到東亞的網路對應。  
![網路對應](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>啟用複寫時對應網路

第一次從一個 Azure 區域將虛擬機器複寫到另一個區域時，如果未完成網路對應，您可以在同一個程序中選擇目標網路。 Site Recovery 會根據此選項，建立從來源地區到目標區域以及從目標區域到目標區域的網路對應。   

![網路對應](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

根據預設，Site Recovery 會在目標區域中建立與來源網路相同的網路，並在來源網路的名稱後面加上「-asr」的尾碼。 您可以按一下 [自訂] 選擇已建立的網路。

![網路對應](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


如果已完成網路對應，您無法在啟用複寫時變更目標虛擬網路。 若要變更，請修改現有的網路對應。  

![網路對應](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![網路對應](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> 如果將網路對應從 region-1 修改為 region-2，請確定網路對應也從 region-2 修改為 region-1。
>
>


## <a name="subnet-selection"></a>子網路選取項目
目標虛擬機器的子網路，是根據來源虛擬機器的子網路名稱來選取。 如果目標網路中可用的來源虛擬機器有相同名稱的子網路，則會對於目標虛擬機器選擇該子網路。 如果目標網路沒有相同名稱的子網路，則會依字母順序選擇第一個子網路做為目標子網路。 您可以移至虛擬機器的 [計算和網路設定] 修改此子網路。

![修改子網路](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP 位址

對於目標虛擬機器的每個網路介面，均按照下列方式選擇 IP 位址：

### <a name="dhcp"></a>DHCP
如果來源虛擬機器的網路介面使用 DHCP，則也會將目標虛擬機器的網路介面設定為 DHCP。

### <a name="static-ip"></a>靜態 IP
如果來源虛擬機器的網路介面使用靜態 IP，則也會將目標虛擬機器的網路介面設定為使用靜態 IP。 選擇靜態 IP 的方式如下所示：

#### <a name="same-address-space"></a>相同的位址空間

如果來源的子網路和目標子網路具有相同的位址空間，則目標 IP 會設定為與來源虛擬機器的網路介面的 IP 相同。 如果找不到相同的 IP，則將設定其他可用的 IP 做為目標 IP。

#### <a name="different-address-space"></a>不同的位址空間

如果來源子網路和目標子網路具有不同的位址空間，則目標 IP 會設定為與目標子網路中的任何可用 IP 相同。

您可以移至虛擬機器的 [計算和網路設定] 修改各個網路介面上的目標 IP。

## <a name="next-steps"></a>後續步驟

- 深入了解[複寫 Azure VM 的網路指引](site-recovery-azure-to-azure-networking-guidance.md)。
