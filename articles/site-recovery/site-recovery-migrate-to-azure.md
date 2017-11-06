---
title: "使用 Site Recovery 將內部部署 VM 和實體伺服器移轉至 Azure | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 將內部部署 VM 和實體伺服器移轉至 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>使用 Site Recovery 移轉至 Azure

閱讀本文以了解如何使用 [Azure Site Recovery](site-recovery-overview.md) 服務來將內部部署虛擬機器 (VM) 和實體伺服器移轉至 Azure Vm。

## <a name="before-you-start"></a>開始之前

觀賞此視訊以快速瀏覽移轉至 Azure 時所需步驟的概觀。
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>移轉的意思為何？

您可以部署 Site Recovery 來複寫內部部署 VM 和實體伺服器，並且進行移轉。

- 複寫時，將內部部署機器設定為定期複寫至 Azure。 之後如果發生運行中斷，您就會將該機器從內部部署網站容錯移轉至 Azure，並從該處進行存取。 當內部部署網站恢復可用狀態時，您就可以從 Azure 進行容錯回復。
- 當您使用 Site Recovery 進行移轉時，您會將內部部署機器複寫至 Azure。 然後將這些機器從內部部署網站容錯移轉至 Azure，並完成移轉程序。 其中不包含容錯回復。  

## <a name="what-can-site-recovery-migrate"></a>Site Recovery 可以移轉什麼項目？

您可以：

- **從內部部署移轉**：將內部部署 Hyper-V VM、VMware VM 和實體伺服器移轉至 Azure。 移轉之後，內部部署機器上執行的工作負載將在 Azure VM 上執行。 
- **在 Azure 中進行移轉**：在 Azure 區域之間移轉 Azure VM。 
- **移轉 AWS**：將 AWS Windows 執行個體移轉至 Azure IaaS VM。 

## <a name="migrate-from-on-premises-to-azure"></a>從內部部署移轉至 Azure

若要移轉內部部署 VMware VM、Hyper-V VM 和實體伺服器，您所需遵循的步驟幾乎和完整複寫時所使用的步驟相同。 


## <a name="migrate-between-azure-regions"></a>在不同的 Azure 地區之間移轉

若要在區域之間移轉 Azure VM，您所需遵循的步驟幾乎和完整複寫時所使用的步驟相同。

1. 針對您想要移轉的機器[啟用複寫](azure-to-azure-tutorial-enable-replication.md))。
2. 您可以[執行快速的容錯移轉測試](azure-to-azure-tutorial-dr-drill.md)，以確定一切都沒問題。
3. 然後使用**完成移轉**選項執行[非計劃性容錯移轉](azure-to-azure-tutorial-failover-failback.md)。
4. 完成移轉之後，您可以[設定災害復原的複寫](site-recovery-azure-to-azure-after-migration.md)，從移轉的目的地 Azure 區域複寫到次要區域。



## <a name="migrate-aws-to-azure"></a>將 AWS 移轉至 Azure

您可以將 AWS 執行個體移轉至 Azure VM。
- 此案例只支援移轉。 換句話說，您可以複寫 AWS 執行個體，並將它們容錯移轉至 Azure，但您無法容錯回復。
- 在進行移轉時，AWS 執行個體的處理方式和實體伺服器相同。 您要設定復原服務保存庫、部署用來管理複寫的內部部署組態伺服器、將它新增至保存庫，以及指定複寫設定。
- 為您想要移轉的機器啟用複寫，並執行快速的測試容錯移轉。 然後使用**完成移轉**選項執行非計劃性容錯移轉。






## <a name="next-steps"></a>後續步驟

- [將內部部署機器移轉至 Azure](tutorial-migrate-on-premises-to-azure.md)
- [在 Azure 區域間移轉 VM](site-recovery-migrate-azure-to-azure.md)
- [將 AWS 移轉至 Azure](tutorial-migrate-aws-to-azure.md)
