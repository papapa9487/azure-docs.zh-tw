---
title: "預先支付 Azure 虛擬機器以節省成本 - Azure | Microsoft Docs"
description: "深入了解 Azure 保留的虛擬機器執行個體以節省虛擬機器的成本。"
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 96e9cf2fed0b22fd7aa7b9ffeab0e94738ce510d
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>使用 Azure 保留的虛擬機器執行個體以節省虛擬機器的成本 
保留的虛擬機器執行個體可讓您預先支付一年或三年的計算容量，以獲得所使用之虛擬機器的折扣。 這樣可以大幅降低虛擬機器成本，一年或三年預先承諾最高可達隨用隨付價格的 72%。 保留的虛擬機器執行個體會以折扣價格計費，而且不會影響虛擬機器的執行階段狀態。

您可以在 [Azure 入口網站](https://aka.ms/reservations)購買保留的虛擬機器執行個體。 如需詳細資訊，請參閱[預付虛擬機器並且使用保留的虛擬機器執行個體來節省成本](https://go.microsoft.com/fwlink/?linkid=861721)。

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>為什麼應該購買保留的虛擬機器執行個體？
如果您有長時間執行的虛擬機器，購買保留的虛擬機器執行個體可給予您最實惠的價格。 例如，如果您持續在美國西部區域執行標準 D2 的四個執行個體，若未使用保留則會以隨用隨付費率計費。 如果您為這四個 VM 購買保留的虛擬機器執行個體，VM 會立即取得優惠計費。 它們不再以隨用隨付費率計費。 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>保留的虛擬機器執行個體涵蓋哪些費用？
保留僅涵蓋 Windows 或 Linux 虛擬機器的虛擬機器基礎結構費用。 保留不包含額外的軟體、網路或儲存體費用。 對於 Windows 虛擬機器，您可以利用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 以涵蓋 Windows 授權成本。

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>誰具備購買保留的虛擬機器執行個體的資格？
具有以下這些訂用帳戶類型的 Azure 客戶可以購買保留的虛擬機器執行個體：
-   Enterprise 合約訂用帳戶優惠類型 (MS-AZR-0017P)。
-   [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶優惠類型 (MS-AZR-003P)。
您必須具有訂用帳戶中的「擁有者」角色，才能購買保留的執行個體。 如果要在企業註冊中購買保留，企業系統管理員必須在 EA 入口網站啟用保留購買，此選項預設為啟用。

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>保留的虛擬機器執行個體購買如何計費？
保留購買會計費至與訂用帳戶繫結的付款方式。 如果您有 Enterprise 訂用帳戶，保留成本是從您的預付餘額扣除。 如果您的預付餘額未涵蓋保留的成本，您會支付超額部分。
如果您有隨用隨付訂用帳戶，會對您的帳戶所登記的信用卡立即計費。 如果是透過發票計費，您會在下一張發票上看到費用。

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>已購買保留的虛擬機器執行個體折扣如何套用？
保留的虛擬機器執行個體折扣，適用於符合當您購買保留時選取之屬性的虛擬機器。 屬性包含相符的 VM 執行所在的範圍。 例如，如果您想要美國西部區域四個標準 D2 虛擬機器之保留的 VM 執行個體折扣，選取 VM 執行所在的訂用帳戶。 如果虛擬機器是在註冊/帳戶內的不同訂用帳戶中執行，則選取共用的範圍。 共用的範圍可讓保留折扣套用到整個訂用帳戶。
購買保留的 VM 執行個體之後，您可以變更範圍。 若要變更範圍，請參閱如何管理保留的文件。

保留折扣僅適用於企業或隨用隨付優惠類型訂用帳戶中的虛擬機器。 在其他優惠類型訂用帳戶中執行的虛擬機器不會收到保留折扣。 針對企業註冊，企業開發/測試訂用帳戶沒有保留的執行個體權益的資格。

保留如何影響虛擬機器帳單會在[了解保留計費權益的應用](https://go.microsoft.com/fwlink/?linkid=863405)中說明。

## <a name="what-happens-when-the-reservation-term-expires"></a>保留期限到期時會發生什麼事？
在保留期限結束時，計費折扣會到期，虛擬機器基礎結構會以隨用隨付價格計費。 保留不會自動更新。 若要繼續獲得帳單折扣，您必須購買新的保留的虛擬機器執行個體。 

## <a name="sizes-and-regional-availability"></a>大小和區域可用性
保留適用於大部分 VM 大小，但有些例外狀況：
- 預覽 VM 大小 – 處於預覽狀態的任何大小都不適用於保留的虛擬機器執行個體購買。
- 雲端 – 保留的虛擬機器執行個體不適用於 Azure 美國政府、德國或中國區域中的購買。 
- 配額不足 – 範圍為單一訂用帳戶之保留的 VM 執行個體，必須在新 RI 訂用帳戶中有可用的 vCPU 配額。 例如，如果目標訂用帳戶具有 D 系列家族的 10 個 vCPU 配額限制，則您無法購買 11 個 Standard_D1 執行個體之保留的 VM 執行個體。 保留的配額檢查包含已在訂用帳戶中部署的 VM。 例如，如果訂用帳戶有 D 系列家族的 10 個 vCPU 配額。 如果此訂用帳戶已部署兩個 standard_D1 執行個體，您就可以在此訂用帳戶中購買 10 個 standard_D1 執行個體之保留的 VM 執行個體。 
- 容量限制 – 在極少數的情況下，Azure 會限制購買 VM 大小子集的新保留，因為區域中的低容量。

## <a name="next-steps"></a>後續步驟
購買[保留的虛擬機器執行個體](https://go.microsoft.com/fwlink/?linkid=861721)，開始節省虛擬機器的成本。 

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
