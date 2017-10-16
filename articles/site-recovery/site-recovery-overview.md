---
title: "什麼是 Azure Site Recovery？ | Microsoft Docs"
description: "提供 Azure Site Recovery 服務的概觀，並摘要說明部署案例。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: aa657c92f347f7529affee78ad1842e5e066b74d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-site-recovery"></a>什麼是 Site Recovery？

歡迎使用 Azure Site Recovery 服務！ 本文提供此服務的快速概觀。

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Azure 復原服務提供的業務持續性和災害復原 (BCDR)

作為一個組織，您必須了解如何維持資料安全，以及發生計劃性和非計劃性中斷時，如何讓應用程式/工作負載持續執行。

Azure 復原服務會以下列方式參與您的 BCDR 策略：

- **Site Recovery 服務**：Site Recovery 可在網站關閉時讓您的應用程式持續在 VM 上執行，並保持實體伺服器可用，協助確保業務持續性。 Site Recovery 會複寫在 VM 和實體伺服器上執行的工作負載，因此如果主要網站無法使用，仍可在次要位置中使用工作負載。 當主要網站再次開始運作及執行時，它會將工作負載復原至其中。
- **備份服務**：此外，[Azure 備份](https://docs.microsoft.com/azure/backup/)服務會將您的資料備份至 Azure，維持資料安全且更容易復原。

Site Recovery 可以管理複寫：

- 在 Azure 區域間複寫 Azure VM。
- 將內部部署虛擬機器和實體伺服器複寫至 Azure 或次要網站。


## <a name="what-does-site-recovery-provide"></a>Site Recovery 可以提供什麼功能？

**功能** | **詳細資料**
--- | ---
**部署簡單的 BCDR 解決方案** | 您可以使用 Site Recovery 從 Azure 入口網站中的單一位置設定及管理複寫、容錯移轉和容錯回復。
**複寫 Azure VM** | 您可以設定 BCDR 策略，即可在 Azure 區域間複寫 Azure VM。
**異地複寫內部部署 VM** | 您可以將內部部署 VM 和實體伺服器複寫至 Azure 或次要內部部署位置。 複寫至 Azure 可排除維護次要資料中心的成本和複雜度。
**複寫任何工作負載** | 複寫在支援的 Azure VM、內部部署 Hyper-V VM、VMware VM 和 Windows/Linux 實體伺服器上執行的任何工作負載。
**保持資料彈性及安全** | Site Recovery 會協調複寫，且無須攔截應用程式資料。 複寫的資料會儲存在 Azure 儲存體中，並具備所提供的恢復功能。 進行容錯移轉時，會根據複寫的資料來建立 Azuer VM。
**符合 RTO 和 RPO** | 將復原時間目標 (RTO) 和復原點目標 (RPO) 保持在組織的限制範圍內。 Site Recovery 為 Azure VM 和 VMware VM 提供了連續複寫功能，並為 Hyper-V 提供最低 30 秒的複寫頻率。 您可以藉由與 [Azure 流量管理員](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)整合，進一步縮減復原時間目標 (RTO)。
**透過容錯移轉讓應用程式保持一致** | 您可以使用與應用程式一致的快照集設定復原點。 與應用程式一致的快照集會擷取磁碟資料、記憶體中的所有資料和處理序中的所有交易。
**測試不中斷** | 您可以輕鬆執行測試用容錯移轉，既能支援災害復原演練，又不會影響進行中的複寫。
**執行彈性容錯移轉** | 您可以執行計劃性容錯移轉，因為是預期中的中斷，所以不會遺失任何資料；也可以執行非計劃性容錯移轉，以在發生未預期的災害時將資料損失減到最少 (取決於複寫頻率)。 您可以在主要站台再次可用時，輕鬆地容錯回復到該站台。
**建立復原計劃** | 您可以透過復原計劃，自訂及排序多部 VM 上多層應用程式的容錯移轉和復原。 您要將計劃內的機器分組，並新增指令碼和手動動作。 復原計劃可以與 Azure 自動化 Runbook 整合。
**與現有的 BCDR 技術整合** | Site Recovery 與其他 BCDR 技術整合。 例如，您可以使用 Site Recovery 保護公司工作負載的 SQL Server 後端，包括原生支援 SQL Server AlwaysOn 以便管理可用性群組的容錯移轉。
**與自動化文件庫整合** | 豐富的 Azure Automation 文件庫，提供已可用於生產環境，且可下載並已經與 Site Recovery 整合的應用程式特定指令碼。
**管理網路設定** | Site Recovery 與 Azure 整合，提供簡單的應用程式網路管理，包括保留 IP 位址、設定負載平衡器和整合 Azure 流量管理員以進行有效率的網路轉換。


## <a name="what-can-i-replicate"></a>我可以複寫哪些項目？

**支援** | **詳細資料**
--- | ---
**我可以複寫哪些項目？** | Azure 區域間的 Azure VM (預覽)<br/><br/>  內部部署 VMware VM、Hyper-V VM、實體伺服器 (Windows 和 Linux) 至 Azure<br/<br/> 內部部署 VMware VM、Hyper-V VM、實體伺服器至次要網站。 針對 Hyper-V VM，僅在 Hyper-V 主機是由 System Center VMM 所管理時才支援複寫至次要網站。
**Site Recovery 支援哪些區域？** | [支援區域](https://azure.microsoft.com/regions/services/) |
**複寫的機器需要哪些作業系統？** | [Azure 虛擬機器需求](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)s<br></br>[VMware 虛擬機器需求](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> 如果是 Hyper-V VM，支援 Azure 和 Hyper-V 支援的所有[客體 OS](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)。<br/><br/> [實體伺服器需求](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**我需要哪些 VMware 伺服器/主機？** | VMware VM 可以位於[支援的 vSphere 主機/vCenter 伺服器](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)上
**可以複寫哪些工作負載？** | 您可以複寫在支援的複寫機器上執行的所有工作負載。 此外，Site Recovery 小組已經針對[應用程式數目](site-recovery-workload.md#workload-summary)執行應用程式專屬測試。


## <a name="azure-portal-considerations"></a>Azure 入口網站的考量

* Site Recovery 可以部署在 [Azure 入口網站](https://portal.azure.com)中。
* 在 Azure 傳統入口網站中，您可以使用傳統服務管理模型來管理 Site Recovery。
- 傳統的入口網站只能用來維護現有的 Site Recovery 部署。 您無法在傳統入口網站中建立新的保存庫。

## <a name="next-steps"></a>後續步驟
* 深入了解[工作負載支援](site-recovery-workload.md)
* 開始使用[區域間的 Azure VM 複寫](site-recovery-azure-to-azure.md)、[VMware 複寫至 Azure](vmware-walkthrough-overview.md)，或 [Hyper-V 複寫至 Azure](hyper-v-site-walkthrough-overview.md)。
