---
title: "使用 Azure Site Recovery 檢閱 Hyper-V 對 Azure 複寫的必要條件 (不含 System Center VMM) | Microsoft Docs"
description: "描述使用 Azure Site Recovery 設定內部部署 Hyper-V VM 對 Azure 的複寫、容錯移轉和復原之必要條件"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7ef3fb46-52f5-4c8a-b1a1-658c2305762a
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: cbb5d3598ef91512991d7d1e9f854eb12980752b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-without-vmm-to-azure-replication"></a>步驟 2：檢閱 Hyper-V (不含 VMM) 對 Azure 複寫的必要條件

資料表中會摘要列出必要條件。


**必要條件** | **詳細資料** 
--- | --- 
**Azure** | 了解 [Azure 需求](site-recovery-prereq.md#azure-requirements)。
**內部部署伺服器** | [深入了解](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm)內部部署 Hyper-V 主機的需求。
**內部部署 Hyper-V VM** | 您想要複寫的 VM 應該執行[支援的作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，也要符合 [Azure 必要條件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
**Azure URL** | Hyper-V 主機需要存取這些 URL：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> 如果您有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。<br/></br> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br/></br> 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。



## <a name="next-steps"></a>後續步驟

- 如果您要進行完整部署，請移至[步驟 3：規劃容量](hyper-v-site-walkthrough-capacity.md)
- 如果您要進行簡單的測試部署，請移至[步驟 4：規劃網路](hyper-v-site-walkthrough-network.md)。
