---
title: "Azure 建議程式高可用性建議 | Microsoft Docs"
description: "使用 Azure 建議程式來改善 Azure 部署的高可用性。"
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b63de2453180e562596c211d40cebe85b95bd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-high-availability-recommendations"></a>建議程式高可用性建議

Azure Advisor 可協助您確保和改善業務關鍵應用程式的持續性。 您可以從 [建議程式] 儀表板的 [高可用性] 索引標籤，利用建議程式取得高可用性建議。

![Advisor 儀表板上的 [高可用性] 按鈕](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>確保虛擬機器的容錯

建議程式會識別不屬於可用性設定組的虛擬機器，並建議將它們移至某個可用性設定組中。 若要為應用程式提供備援，建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 Azure 虛擬機器 SLA。 您可以選擇建立虛擬機器的可用性設定組，或將虛擬機器新增至現有的可用性設定組。

> [!NOTE]
> 如果您選擇建立可用性設定組，您必須至少再將一個虛擬機器新增至該可用性設定組。 我們建議讓兩部或多部虛擬機器組成一個可用性設定組，以確保至少有一部機器可用於中斷期間。

![Advisor 建議︰為了獲得虛擬機器備援功能，請使用可用性設定組](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>確保可用性設定組的容錯 

Advisor 會識別包含單一虛擬機器的可用性設定組，並建議將一部或多部虛擬機器新增至該可用性設定組。 若要為應用程式提供備援，建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 Azure 虛擬機器 SLA。 您可以選擇建立虛擬機器或使用現有的虛擬機器，並將它新增至可用性設定組。  

![Advisor 建議︰在這個可用性設定組中新增一或多個 VM](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>確保應用程式閘道的容錯
為確保應用程式閘道所支援的關鍵任務應用程式擁有商務持續性，Advisor 會識別未設定容錯功能的應用程式閘道執行個體，並建議可行的修復動作。 Advisor 可識別中型或大型的單一執行個體應用程式閘道，並建議再新增至少一個執行個體。 它也可識別單一或多重執行個體的小型應用程式閘道，並建議您移轉至中型或大型的 SKU。 Advisor 會建議這些動作，以確保應用程式閘道執行個體已設定為能夠符合這些資源目前的 SLA 需求。

![Advisor 建議︰部署兩個以上的中型或大型應用程式閘道執行個體](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>改善虛擬機器磁碟的效能和可靠性

Advisor 會識別使用標準磁碟的虛擬機器，並建議升級為進階磁碟。
 
針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用進階儲存體帳戶的虛擬機器磁碟會將資料儲存在固態硬碟 (SSD) 上。 為了讓應用程式發揮最佳效能，建議您將任何需要高 IOPS 的虛擬機器磁碟移轉到進階儲存體。 

如果您的磁碟不需要高 IOPS，您可以讓磁碟留在標準儲存體中以節省成本。 標準儲存體會將虛擬機器磁碟資料儲存在硬碟機 (HDD) 而非 SSD 上。 您可以選擇將虛擬機器磁碟移轉到進階磁碟。 大部分的虛擬機器 SKU 都支援進階磁碟。 不過在某些情況下，如果您想要使用進階磁碟，您可能還需要升級虛擬機器 SKU。

![Advisor 建議︰升級為進階磁碟以改善虛擬機器磁碟的可靠性](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>防止意外刪除虛擬機器的資料
Advisor 會識別未啟用備份的虛擬機器，並建議啟用備份。 設定虛擬機器備份可確保業務關鍵資料的可用性，並防止資料意外刪除或損毀。

![Advisor 建議︰設定虛擬機器備份以保護關鍵任務資料](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>在 Advisor 中取得高可用性建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側窗格中，按一下 [更多服務]。

3. 在服務功能表窗格中，於 [監視和管理] 底下，按一下 [Azure Advisor]。  
 隨即會顯示 Advisor 儀表板。

4. 在 [Advisor] 儀表板上，按一下 [高可用性] 索引標籤，然後選取您想要接收建議的訂用帳戶。

> [!NOTE]
> 若要存取 Advisor 建議，您必須先向 Advisor「註冊您的訂用帳戶」。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，您能以訂用帳戶、資源群組或特定資源的 [擁有者]、[參與者] 或 [讀取者] 身分存取 Advisor 建議。

## <a name="next-steps"></a>後續步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure 建議程式簡介](advisor-overview.md)
* [開始使用建議程式](advisor-get-started.md)
* [建議程式成本建議](advisor-performance-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [建議程式安全性建議](advisor-security-recommendations.md)

