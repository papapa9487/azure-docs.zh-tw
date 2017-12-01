---
title: "了解 Azure 保留執行個體的折扣應用 |Microsoft 文件"
description: "了解 Azure 保留執行個體的折扣如何套用至執行中的虛擬機器。"
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: vikdesai
ms.openlocfilehash: 3fd12bd3c51eeef57c896da030a83e447dc3e8ff
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>了解保留執行個體折扣如何套用
購買保留執行個體之後，保留折扣會自動套用至符合屬性和保留數量的虛擬機器。 保留會涵蓋您虛擬機器的基礎結構成本。 下表說明您購買保留後的虛擬機器成本。 在所有情況下，您將依標準費率支付儲存體和網路功能的費用。

| 虛擬機器類型  | 使用保留的費用 |    
|-----------------------|--------------------------------------------| 
|沒有包含其他軟體的 Linux 虛擬機器 | 保留可涵蓋您虛擬機器的基礎結構成本。|
|包含軟體 (例如 Red Hat) 費用的 Linux 虛擬機器 | 保留可涵蓋基礎結構成本。 您必須支付額外的軟體費用。|
|沒有包含其他軟體的 Windows 虛擬機器 |保留可涵蓋基礎結構成本。 您必須支付 Windows 軟體的費用。|
|包含其他軟體 (例如 SQL server) 的 Windows 虛擬機器 | 保留可涵蓋基礎結構成本。 您必須支付 Windows 軟體和其他軟體的費用。|
|具有 [Azure Hybrid Benefit](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing) 的 Windows 虛擬機器 | 保留可涵蓋基礎結構成本。 Azure Hybrid Benefit 所涵蓋的 Windows 軟體成本。 任何額外的軟體都是分開收費。| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>非 Windows 虛擬機器的保留折扣應用
 保留折扣會套用到執行的虛擬機器執行個體 (以小時計算)。 您購買的保留會與執行中虛擬機器發出的使用量進行比對，以便套用保留折扣。 下圖說明可計費虛擬機器使用量的保留應用。 圖例是以一個購買的保留和兩個對應的虛擬機器執行個體為基礎。

![保留執行個體的應用](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  任何保留執行個體行上的使用量，都會依一般預付型方案的費率進行收費。 您不用支付保留執行個體這一條線下方的費用，因為此費用在購買保留時已涵蓋在內。
2.  在第一個 1 小時內，執行個體 1 執行了 0.75 個小時，而執行個體 2 執行了 0.5 小時。 第一個 1 小時的使用量總計為 1.25 小時。 您必須為剩餘的 0.25 小時支付預付型方案費率的費用。
3.  針對第 2 小時和第 3 小時，兩個執行個體各執行 1 小時。 一個執行個體涵蓋在保留內，而另一個執行個體須依預付型方案的費率計費。
4.  針對第 4 個小時，執行個體 1 執行了 0.5 個小時，而執行個體 2 執行了 1 小時。 保留可完全涵蓋執行個體 1，並涵蓋了執行個體 2 的 0.5 個小時。 您必須為剩餘的 0.5 小時支付預付型方案費率的費用。

若要在計費使用量報告中了解及檢視保留的應用，請參閱[了解保留執行個體使用量](https://go.microsoft.com/fwlink/?linkid=862757)。

## <a name="application-of-reservation-discount-to-windows-vms"></a>Windows 虛擬機器的保留折扣應用
當您執行 Windows 虛擬機器執行個體時，系統會套用保留以涵蓋基礎結構成本。 對於 Windows 虛擬機器，其虛擬機器基礎結構成本的保留應用與非 Windows 虛擬機器相同。 您必須為以每個 vCPU 為單位計算的 Windows 軟體個別支付費用。 請參閱 [使用保留的 Windows 軟體成本](https://go.microsoft.com/fwlink/?linkid=862756)。 您可以使用 [Azure Hybrid Benefit for Windows Server] 來涵蓋 Windows 授權成本 (https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
