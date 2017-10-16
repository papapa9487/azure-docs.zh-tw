---
title: "使用 Site Recovery 移轉至 Azure | Microsoft Docs"
description: "本文概述如何使用 Azure Site Recovery 將 VM 與實體伺服器移轉至 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2017
ms.author: raynew
ms.openlocfilehash: f4dfe430fba51bd009431ca72279a21be55e3a40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>使用 Site Recovery 移轉至 Azure

請閱讀本文，以概略了解如何使用 Azure Site Recovery 服務來移轉虛擬機器和實體伺服器。

Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。 當您的主要位置發生故障時，您容錯移轉至次要位置，讓應用程式和工作負載保持可用。 當它恢復正常作業時，容錯回復至您的主要位置。 深入了解 [什麼是 Site Recovery？](site-recovery-overview.md) 您也可以使用 Site Recovery，將現有的內部部署工作負載移轉至 Azure，以加速您的雲端旅程，並使用 Azure 提供的功能陣列。

如需如何執行移轉的快速概觀，請觀看這段影片。
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]

本文說明 [Azure 入口網站](https://portal.azure.com)中的部署作業。 [Azure 傳統入口網站](https://manage.windowsazure.com/)可用來維護現有的 Site Recovery 保存庫，但無法建立新的保存庫。

若有任何意見，請張貼於文末。 請在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)提出技術問題。


## <a name="what-do-we-mean-by-migration"></a>移轉的意思為何？

您可以將用來複寫內部部署 VM 和實體伺服器的 Site Recovery 部署至 Azure 或次要站台。 您會複寫機器、發生中斷時從主要站台容錯移轉它們，然後在主要站台復原時將它們容錯移轉回到該站台。 除此之外，您還可以使用 Site Recovery 將 VM 和實體伺服器移轉到 Azure，讓使用者可以存取它們做為 Azure VM。 移轉作業需要進行複寫、從主要站台容錯移轉至 Azure，以及完整的移轉軌跡。

## <a name="what-can-site-recovery-migrate"></a>Site Recovery 可以移轉什麼項目？

您可以：

- 將在內部部署 Hyper-V VM、VMware VM 和實體伺服器上執行的工作負載，移轉到 Azure VM 上來執行。 在此案例中，您也可以執行完整的複寫和容錯回復。
- 在 Azure 區域之間移轉 [Azure IaaS VM](site-recovery-migrate-azure-to-azure.md)。 此案例目前只支援移轉，亦即不支援容錯回復。
- 將 [AWS Windows 執行個體](site-recovery-migrate-aws-to-azure.md)移轉到 Azure IaaS VM。 此案例目前只支援移轉，亦即不支援容錯回復。

## <a name="migrate-on-premises-vms-and-physical-servers"></a>移轉內部部署 VM 和實體伺服器

若要移轉內部部署 Hyper-V VM、VMware VM 和實體伺服器，您所需遵循的步驟幾乎和一般複寫時所使用的步驟相同。

1. 設定復原服務保存庫
2. 設定所需的管理伺服器 (VMware、VMM、Hyper-V - 視您要移轉的項目而定)、將它們新增至保存庫，並指定複寫設定。
3. 針對您想要移轉的電腦啟用複寫
4. 在初始移轉之後，執行快速的測試容錯移轉，以確保一切運作正常。
5. 確認複寫環境有用之後，您需要根據您的案例[所支援的項目](site-recovery-failover.md)使用計劃性或非計劃性容錯移轉。 我們建議您儘可能使用規劃的容錯移轉。
6. 若要進行移轉，您不需要認可容錯移轉或刪除它。 相反地，您要為所要移轉的每一部機器選取**完成移轉**選項。
     - 在 [複寫的項目] 中，以滑鼠右鍵按一下 VM，然後按一下 [完成移轉]。 按一下 [確定] 以完成。 您可以在 [Site Recovery 作業] 中監視 [完成移轉] 作業，以在 VM 屬性中追蹤進度。
     - **完成移轉**動作會完成移轉程序、移除機器的複寫，並停止該機器的 Site Recovery 計費。

![完成移轉](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

## <a name="migrate-between-azure-regions"></a>在不同的 Azure 地區之間移轉

您可以使用 Site Recovery 在區域之間移轉 Azure VM。 此案例只支援移轉。 換句話說，您可以複寫 Azure VM，並將它們容錯移轉至另一個區域，但您無法容錯回復。 在此案例中，您要設定復原服務保存庫、部署用來管理複寫的內部部署組態伺服器、將它新增至保存庫，以及指定複寫設定。 為您想要移轉的機器啟用複寫，並執行快速的測試容錯移轉。 然後使用**完成移轉**選項執行非計劃性容錯移轉。

## <a name="migrate-aws-to-azure"></a>將 AWS 移轉至 Azure

您可以將 AWS 執行個體移轉至 Azure VM。 此案例只支援移轉。 換句話說，您可以複寫 AWS 執行個體，並將它們容錯移轉至 Azure，但您無法容錯回復。 在進行移轉時，AWS 執行個體的處理方式和實體伺服器相同。 您要設定復原服務保存庫、部署用來管理複寫的內部部署組態伺服器、將它新增至保存庫，以及指定複寫設定。 為您想要移轉的機器啟用複寫，並執行快速的測試容錯移轉。 然後使用**完成移轉**選項執行非計劃性容錯移轉。




## <a name="next-steps"></a>後續步驟

- [將 VMware VM 移轉至 Azure](site-recovery-vmware-to-azure.md)
- [將 VMM 雲端中的 Hyper-V VM 移轉至 Azure](site-recovery-vmm-to-azure.md)
- [將沒有 VMM 的 Hyper-V VM 移轉至 Azure](site-recovery-hyper-v-site-to-azure.md)
- [在 Azure 區域之間移轉 Azure VM](site-recovery-migrate-azure-to-azure.md)
- [將 AWS 執行個體移轉至 Azure](site-recovery-migrate-aws-to-azure.md)
- [準備已移轉的機器，以便複寫](site-recovery-azure-to-azure-after-migration.md)至其他區域以因應災害復原的需要。
- [複寫 Azure 虛擬機器](site-recovery-azure-to-azure.md)來開始保護您的工作負載。
