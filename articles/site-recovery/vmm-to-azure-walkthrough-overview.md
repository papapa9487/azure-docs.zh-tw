---
title: "使用 Azure Site Recovery 將 VMM 雲端中的 Hyper-V VM 複寫至 Azure | Microsoft Docs"
description: "提供使用 Azure Site Recovery 服務將 VMM 雲端中的 Hyper-V VM 複寫至 Azure 的概觀"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: af68d21184c137b2b0f1bb4c1afb9bf507e8332d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>使用 Azure 入口網站中的 Site Recovery 將 Hyper-V 虛擬機器 (位於 VMM 雲端中) 複寫至 Azure
> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-vmm-to-azure.md)
> * [Azure 傳統型](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell 傳統](site-recovery-deploy-with-powershell.md)


本文提供的步驟概觀，是關於使用 Azure 入口網站中的 [Azure Site Recovery](site-recovery-overview.md) 服務，將 System Center Virtual Machine Manager (VMM) 雲端中管理的內部部署 Hyper-V 虛擬機器 (VM) 複寫至 Azure。

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。


## <a name="step-1-review-the-scenario-architecture"></a>步驟 1：檢閱案例架構

在開始部署之前，請檢閱案例架構，並確定您了解部署所需的一切元件。

移至[步驟 1：檢閱架構](vmm-to-azure-walkthrough-architecture.md)

## <a name="step-2-review-prerequisites-and-limitations"></a>步驟 2：檢閱必要條件和限制

請確定您了解部署的必要條件和限制。

**Azure 必要條件**：您需要 Microsoft Azure 帳戶、Azure 網路及儲存體帳戶。
**內部部署 VMM 伺服器和 Hyper-V 主機**：請確定 VMM 伺服器和 Hyper-V 主機符合 Site Recovery 部署的必要條件並已準備就緒。
**複寫 VM**：請確定您想要複寫的 VM 符合 Azure 需求。

移至[步驟 2：確認必要條件和限制](vmm-to-azure-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>步驟 3：規劃容量

如果您要執行完整部署，就必須了解需要哪些複寫資源。 有幾個工具可幫助您執行這項操作。 如果您要進行快速設定來測試環境，則可以略過此步驟。

移至[步驟 3：規劃容量](vmm-to-azure-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>步驟 4：規劃網路

您必須進行一些網路規劃，以確保部署案例時可以設定網路對應、Azure VM 在容錯移轉發生後將連線到 Azure 虛擬網路，以及為它們指派適當的 IP 位址。

移至[步驟 4：規劃網路](vmm-to-azure-walkthrough-network.md)


## <a name="step-5-prepare-azure-resources"></a>步驟 5：準備 Azure 資源

設定 Azure 帳戶、網路和儲存體。 您可以在部署期間執行這項操作，但建議您在開始之前進行。

移至[步驟 5：準備 Azure](vmm-to-azure-walkthrough-prepare-azure.md)

## <a name="step-6-prepare-vmm-and-hyper-v"></a>步驟 6：準備 VMM 和 Hyper-V

準備 Site Recovery 部署所需的內部部署 VMM 伺服器和 Hyper-V 主機。

移至[步驟 6：準備內部部署伺服器](vmm-to-azure-walkthrough-vmm-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>步驟 7：設定保存庫

設定復原服務保存庫。 保存庫包含組態設定，並協調複寫。

[步驟 7：設定保存庫](vmm-to-azure-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>步驟 8：設定來源和目標設定

設定來源和目標複寫位置。 將 VMM 伺服器加入至保存庫，並下載 Site Recovery 元件的安裝檔案。 在 VMM 伺服器上執行 Azure Site Recovery Provider 安裝程式。 安裝程式會在 VMM 伺服器上安裝 Provider，並在保存庫中註冊伺服器。 在每部 Hyper-V 主機上安裝 Microsoft 復原服務代理程式。

移至[步驟 8：設定來源和目標設定](vmm-to-azure-walkthrough-source-target.md)

## <a name="step-9-configure-network-mapping"></a>步驟 9：設定網路對應

對應內部部署 VMM VM 網路和 Azure 虛擬網路。 在容錯移轉後，會在與來源 Hyper-V 所在的內部部署 VM 網路對應的 Azure 網路中建立 Azure VM。

移至[步驟 9：設定網路對應](vmm-to-azure-walkthrough-network-mapping.md)


## <a name="step-10-set-up-a-replication-policy"></a>步驟 10：設定複寫原則

指定內部部署 VM 複寫至 Azure 的方式。

移至[步驟 10：設定複寫原則](vmm-to-azure-walkthrough-replication.md)


## <a name="step-11-enable-replication-for-vms"></a>步驟 11：啟用 VM 複寫

選取您要複寫的 VM。 啟用 VM 複寫首先會複寫至 Azure，接著持續進行差異複寫。

移至[步驟 11：啟用複寫](vmm-to-azure-walkthrough-enable-replication.md)


## <a name="step-12-run-a-test-failover"></a>步驟 12：執行測試容錯移轉

執行測試容錯移轉，確定一切都沒問題。

移至[步驟 12：執行測試容錯移轉](vmm-to-azure-walkthrough-test-failover.md)


