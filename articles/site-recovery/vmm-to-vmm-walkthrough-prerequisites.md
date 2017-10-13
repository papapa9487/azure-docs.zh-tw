---
title: "檢閱使用 Azure Site Recovery 將 Hyper-V 複寫至次要 VMM 站台的必要條件 | Microsoft Docs"
description: "說明使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要 VMM 站台的必要條件。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>步驟 2：檢閱將 Hyper-V VM 複寫至次要 VMM 站台的必要條件和限制


檢閱[案例架構](vmm-to-vmm-walkthrough-architecture.md)後，請閱讀本文以確定您了解部署必要條件，再使用 Azure 入口網站中的 [Azure Site Recovery](site-recovery-overview.md) 將 System Center Virtual Machine Manager (VMM) 雲端中管理的內部部署 Hyper-V 虛擬機器 (VM) 複寫至次要站台。

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。


## <a name="prerequisites-and-limitations"></a>必要條件和限制

**需求** | **詳細資料**
--- | ---
**Azure** | [Microsoft Azure](http://azure.microsoft.com/) 訂用帳戶。<br/><br/> 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。<br/><br/> [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。<br/><br/> 在 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)的＜各地區上市情況＞底下，查看 Site Recovery 的支援地區。
**VMM 伺服器** | 我們建議您在主要網站和次要網站中各有一部 VMM 伺服器。<br/><br/> 支援在單一 VMM 伺服器上的雲端之間進行複寫。<br/><br/> VMM 伺服器至少應執行含有最新更新的 System Center 2012 SP1。<br/><br/> VMM 伺服器需要網際網路存取。
**VMM 雲端** | 每部 VMM 伺服器都必須有一或多個雲端，而所有雲端都必須設定 Hyper-V 容量設定檔。 <br/><br/>雲端必須包含一或多個 VMM 主機群組。<br/><br/> 如果您只有一部 VMM 伺服器，則需要至少有兩個雲端，才能作為主要和次要網站。
**Hyper-V** | Hyper-V 伺服器必須至少執行具備 Hyper-V 角色並已安裝最新更新的 Windows Server 2012。<br/><br/> Hyper-V 伺服器應該包含一或多部 VM。<br/><br/>  Hyper-V 主機伺服器應該位於主要和次要 VMM 雲端的主機群組中。<br/><br/> 如果您在 Windows Server 2012 R2 上的叢集中執行 Hyper-V，請安裝[更新 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> 如果您在 Windows Server 2012 上的叢集中執行 Hyper-V，當您的叢集是靜態 IP 位址型叢集時，並不會自動建立叢集訊息代理程式。 您必須手動設定叢集訊息代理程式。 [閱讀更多資訊](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。<br/><br/> Hyper-V 伺服器需要網際網路存取。




## <a name="next-steps"></a>後續步驟

移至[步驟 3：規劃網路服務](vmm-to-vmm-walkthrough-network.md)。
