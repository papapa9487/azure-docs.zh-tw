---
title: "使用 Azure Site Recovery 將 Hyper-V VM 複寫至 Azure | Microsoft Docs"
description: "說明如何將內部部署 Hyper-V VM 針對 Azure 進行協調複寫、容錯移轉及復原"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: efddd986-bc13-4a1d-932d-5484cdc7ad8d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: da10b213bc2543942b5ac77cf5c5d8547c00220c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure"></a>將 Hyper-V 虛擬機器 (不含 VMM) 複寫至 Azure 

> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-hyper-v-site-to-azure.md)
> * [Azure 傳統型](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - 資源管理員](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

本文概述在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md)，將內部部署 Hyper-V 虛擬機器複寫至 Azure 所需的步驟。 在此部署中，Hyper-V VM 並非由 System Center Virtual Machine Manager (VMM) 所管理。


閱讀本文之後，在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。


## <a name="step-1-review-architecture-and-prerequisites"></a>步驟 1：檢閱架構和必要條件

在開始部署之前，請檢閱案例架構，並確定您了解部署所需的一切元件

移至[步驟 1：檢閱架構](hyper-v-site-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>步驟 2：檢閱必要條件

請確定您已備妥每個部署元件的必要條件：

- **Azure 必要條件**您需要 Microsoft Azure 帳戶、Azure 網路和儲存體帳戶。
- **內部部署 Hyper-V 必要條件**：請確定備妥 Hyper-V 主機可進行 Site Recovery 部署。
- **複寫 VM**：您想要複寫的 VM 必須符合 Azure 需求。

移至[步驟 2：確認必要條件和限制](hyper-v-site-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>步驟 3：規劃產能

如果您要執行完整部署，就必須了解需要哪些複寫資源。 有幾個工具可幫助您執行這項操作。 移至步驟 2。 如果您要進行快速設定來測試環境，則可以略過此步驟。

移至[步驟 3：規劃容量](hyper-v-site-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>步驟 4：規劃網路

您必須進行一些網路規劃，以確保 Azure VM 在發生容錯移轉之後會連線到網路，並且它們具有正確的 IP 位址。

移至[步驟 4：規劃網路](hyper-v-site-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>步驟 5：準備 Azure 資源

在開始之前，請先設定 Azure 網路和儲存體。 您可以在部署期間執行這項操作，但建議您在開始之前進行。

移至[步驟 5：準備 Azure](hyper-v-site-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-hyper-v"></a>步驟 6：準備 Hyper-V

請確定 Hyper-V 伺服器符合 Site Recovery 部署需求。

移至[步驟 6：準備 Hyper-V](hyper-v-site-walkthrough-prepare-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>步驟 7：設定保存庫

您必須設定「復原服務」保存庫，才能協調和管理複寫。 當您設定保存庫時，請指定您想要複寫的項目，以及您要複寫它的位置。

移至[步驟 7：建立保存庫](hyper-v-site-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>步驟 8：設定來源與目標設定

設定可用於複寫的來源和目標。 設定來源設定包括將 Hyper-V 主機新增至 Hyper-V 站台、在每個 Hyper-V 主機上安裝 Site Recovery 提供者和復原服務代理程式，以及在復原服務保存庫中註冊站台。

移至[步驟 8：設定來源和目標](hyper-v-site-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>步驟 9：設定複寫原則

若要在保存庫中指定 Hyper-V VM 的複寫設定，您就要設定原則。

移至[步驟 9：設定複寫原則](hyper-v-site-walkthrough-replication.md)


## <a name="step-10-enable-replication"></a>步驟 10：啟用複寫

您備妥複寫原則之後，在啟用後就會發生初始複寫 VM。

移至[步驟 10：啟用複寫](hyper-v-site-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>步驟 11：執行測試容錯移轉

初始複寫完成，且差異複寫執行之後，您可以執行測試容錯移轉，以確定一切如預期運作。

移至[步驟 11：執行測試容錯移轉](hyper-v-site-walkthrough-test-failover.md)
