---
title: "在 Azure 區域之間移轉 Azure IaaS VM | Microsoft Docs"
description: "使用 Azure Site Recovery，將 Azure IaaS 虛擬機器從一個 Azure 區域移轉到另一個區域。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在 Azure 區域之間移轉 Azure IaaS 虛擬機器

如果您需要使用 [Site Recovery](../site-recovery-overview.md) 服務在 Azure 區域之間移轉 Azure 虛擬機器 (VM)，請使用這份文件。

## <a name="prerequisites"></a>必要條件

您需要您所要移轉的 IaaS VM。

## <a name="deployment-steps"></a>部署步驟

1. [建立保存庫](azure-to-azure-tutorial-enable-replication.md#create-a-vault)。
2. 針對您需要移轉的虛擬機器[啟用複寫](azure-to-azure-tutorial-enable-replication.md#enable-replication)，並選擇 Azure 作為來源。 目前不支援使用受控磁碟的 Azure VM 原生複寫。
3. [執行容錯移轉](../site-recovery-failover.md)。 初始複寫完成之後，您可以執行從一個 Azure 區域到另一個區域的容錯移轉。 (選擇性) 您可以建立復原計劃並執行容錯移轉，在區域與區域之間移轉多部虛擬機器。 [深入了解](../site-recovery-create-recovery-plans.md) 復原計劃。

## <a name="next-steps"></a>後續步驟
在 [什麼是 Azure Site Recovery？](../site-recovery-overview.md)中深入了解其他複寫情節
