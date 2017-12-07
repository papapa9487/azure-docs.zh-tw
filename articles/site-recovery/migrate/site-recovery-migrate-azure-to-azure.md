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
ms.date: 11/27/2017
ms.author: raynew
ms.openlocfilehash: f645150aa7b93ebbd98899cc8fdd495dfeaf3076
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在 Azure 區域之間移轉 Azure IaaS 虛擬機器

歡迎使用 Azure Site Recovery！ 如果您想要在 Azure 區域之間移轉 Azure VM，請使用本文。
>[!NOTE]
>
> 如需將 Azure VM 複寫至另一個區域以供災害復原和移轉需求，請參閱[這份文件](../site-recovery-azure-to-azure.md)。 Azure 虛擬機器的 Site Recovery 複寫目前為預覽狀態。


## <a name="prerequisites"></a>必要條件
以下是您針對此部署所需要的項目︰

* **IaaS 虛擬機器**：您想要移轉的 VM。 您可以將那些 VM 視為實體機器來移轉它們。

## <a name="deployment-steps"></a>部署步驟
本節描述新 Azure 入口網站中的部署步驟。

1. [建立保存庫](../site-recovery-azure-to-azure.md#create-a-recovery-services-vault)。
2. 針對您需要移轉的虛擬機器 [啟用複寫]../(site-recovery-azure-to-azure.md)，並選擇 Azure 作為來源。
  >[!NOTE]
  >
  > 目前不支援使用受管理磁碟的 Azure VM 原生複寫。 您可以使用[這份文件](../site-recovery-vmware-to-azure.md)中的「實體至 Azure」選項，使用受管理的磁碟來移轉 VM。
3. [執行容錯移轉](../site-recovery-failover.md)。 初始複寫完成之後，您可以執行從一個 Azure 區域到另一個區域的容錯移轉。 (選擇性) 您可以建立復原計劃並執行容錯移轉，在區域與區域之間移轉多部虛擬機器。 [深入了解](../site-recovery-create-recovery-plans.md) 復原計劃。

## <a name="next-steps"></a>後續步驟
在 [什麼是 Azure Site Recovery？](../site-recovery-overview.md)
