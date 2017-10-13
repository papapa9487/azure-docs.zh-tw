---
title: "準備 System Center VMM 以將 Hyper-V 複寫至 Azure | Microsoft Docs"
description: "描述如何準備 System Center VMM 伺服器，以使用 Azure Site Recovery 將 Hyper-V 複寫至 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: afcd81ae-d192-4013-a0af-3dac45b3c7e9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ec118ed837dbf140083b3ae1e4ecd41c81562018
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-vmm-servers-and-hyper-v-hosts-for-hyper-v-replication-to-azure"></a>步驟 6：準備 VMM 伺服器和 Hyper-V 主機以將 Hyper-V 複寫至 Azure

設定好部署所需的 [Azure 元件](vmm-to-azure-walkthrough-prepare-azure.md)後，請使用本文中的指示來準備內部部署 VMM 伺服器和 Hyper-V 主機，以和 Azure Site Recovery 進行互動。

閱讀本文之後，在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。


## <a name="prepare-vmm-servers"></a>準備 VMM 伺服器

- 您至少需要一部符合 Site Recovery 複寫支援需求的 VMM 伺服器 (site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)。
- 請確定您已備妥的 VMM 伺服器[網路對應](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure)。
- 請確定 VMM 伺服器可以存取這些 URL

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- 如果您有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。
- 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。
- 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。

在 Site Recovery 部署期間，您要在每部 VMM 伺服器上下載並安裝 Site Recovery Provider。 VMM 伺服器會在復原服務保存庫中註冊。




## <a name="next-steps"></a>後續步驟

移至[步驟 7：建立保存庫](vmm-to-azure-walkthrough-create-vault.md)

