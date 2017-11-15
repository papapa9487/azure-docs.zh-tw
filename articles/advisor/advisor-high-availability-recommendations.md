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
ms.openlocfilehash: e1cd7948e1969cd4ddb926e428c09b559190a805
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-high-availability-recommendations"></a>建議程式高可用性建議

Azure Advisor 可協助您確保和改善業務關鍵應用程式的持續性。 您可以從 [建議程式] 儀表板的 [高可用性] 索引標籤，利用建議程式取得高可用性建議。

## <a name="ensure-virtual-machine-fault-tolerance"></a>確保虛擬機器的容錯

若要為應用程式提供備援，建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 建議程式會識別不屬於可用性設定組的虛擬機器，並建議將它們移至某個可用性設定組中。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 Azure 虛擬機器 SLA。 您可以選擇為虛擬機器建立可用性設定組，或是將虛擬機器新增至現有的可用性設定組。

> [!NOTE]
> 如果您選擇建立可用性設定組，您必須至少再將一個虛擬機器新增至該可用性設定組。 我們建議讓兩部或多部虛擬機器組成一個可用性設定組，以確保至少有一部機器可用於中斷期間。

## <a name="ensure-availability-set-fault-tolerance"></a>確保可用性設定組的容錯 

若要為應用程式提供備援，建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 Advisor 會識別包含單一虛擬機器的可用性設定組，並建議將一部或多部虛擬機器新增至該可用性設定組。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 Azure 虛擬機器 SLA。 您可以選擇建立虛擬機器，或是將現有的虛擬機器新增至可用性設定組。  

## <a name="ensure-application-gateway-fault-tolerance"></a>確保應用程式閘道的容錯
為確保應用程式閘道所支援的關鍵任務應用程式擁有商務持續性，Advisor 會識別未設定容錯功能的應用程式閘道執行個體，並建議可行的修復動作。 Advisor 可識別中型或大型的單一執行個體應用程式閘道，並建議再新增至少一個執行個體。 它也可識別單一或多重執行個體的小型應用程式閘道，並建議您移轉至中型或大型的 SKU。 Advisor 會建議這些動作，以確保應用程式閘道執行個體已設定為能夠符合這些資源目前的 SLA 需求。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>改善虛擬機器磁碟的效能和可靠性

Advisor 會識別使用標準磁碟的虛擬機器，並建議升級為進階磁碟。
 
針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用進階儲存體帳戶的虛擬機器磁碟會將資料儲存在固態硬碟 (SSD) 上。 為了讓應用程式發揮最佳效能，建議您將任何需要高 IOPS 的虛擬機器磁碟移轉到進階儲存體。 

如果您的磁碟不需要高 IOPS，您可以讓磁碟留在標準儲存體中以節省成本。 標準儲存體會將虛擬機器磁碟資料儲存在硬碟機 (HDD) 而非 SSD 上。 您可以選擇將虛擬機器磁碟移轉到進階磁碟。 大部分的虛擬機器 SKU 都支援進階磁碟。 不過在某些情況下，如果您想要使用進階磁碟，您可能還需要升級虛擬機器 SKU。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>防止意外刪除虛擬機器的資料
設定虛擬機器備份可確保業務關鍵資料的可用性，並防止資料意外刪除或損毀。  Advisor 會識別未啟用備份的虛擬機器，並建議啟用備份。 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>如何存取建議程式中的高可用性建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [高可用性] 索引標籤。

## <a name="next-steps"></a>後續步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure 建議程式簡介](advisor-overview.md)
* [開始使用建議程式](advisor-get-started.md)
* [建議程式成本建議](advisor-performance-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [建議程式安全性建議](advisor-security-recommendations.md)

