---
title: "準備 Hyper-V 主機 (不含 System Center VMM) 以複寫至 Azure | Microsoft Docs"
description: "描述如何使用 Azure Site Recovery 準備 Hyper-V 主機以複寫至 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>步驟 6：準備 Hyper-V 主機複寫至 Azure

使用本文中的指示來準備內部部署 Hyper-V 主機與 Azure Site Recovery 進行互動。

閱讀本文之後，在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。


## <a name="prepare-hosts"></a>準備主機

- 確定 Hyper-V 主機符合[必要條件](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm)。
- 請確定主機可以存取必要的 URL：

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- 如果您有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。
- 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。
- 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。

在 Site Recovery 部署期間，您可以新增 Hyper-V 主機，當中包含您想要複寫到 Hyper-V 網站的 VM。 Site Recovery 提供者和復原服務代理程式會安裝在每一部主機上。 Hyper-V 站台會在復原服務保存庫中註冊。

## <a name="next-steps"></a>後續步驟

移至[步驟 7：建立保存庫](hyper-v-site-walkthrough-create-vault.md)

