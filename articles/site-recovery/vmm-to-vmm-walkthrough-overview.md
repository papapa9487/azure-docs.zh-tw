---
title: "使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要 VMM 站台 | Microsoft Docs"
description: "提供使用 Azure 入口網站將 Hyper-V VM 複寫至次要 VMM 站台的概觀。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>將位於 VMM 雲端中的 Hyper-V 虛擬機器複寫至次要 VMM 站台

> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-vmm-to-vmm.md)
> * [傳統入口網站](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - 資源管理員](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

本文提供的步驟概觀，是關於如何使用 Azure 入口網站中的 [Azure Site Recovery](site-recovery-overview.md)，將 System Center Virtual Machine Manager (VMM) 雲端中管理的內部部署 Hyper-V 虛擬機器 (VM) 複寫至次要 VMM 位置。

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。


## <a name="step-1-review-the-scenario-architecture"></a>步驟 1：檢閱案例架構

在開始部署之前，請檢閱案例架構，並確定您了解部署所需的一切元件。

移至[步驟 1：檢閱架構](vmm-to-vmm-walkthrough-architecture.md)。

## <a name="step-2-review-prerequisites-and-limitations"></a>步驟 2：檢閱必要條件和限制

請確定您了解部署的必要條件和限制。

**Azure 必要條件**：您需要 Microsoft Azure 訂用帳戶和 Azure 復原服務保存庫，以協調和管理複寫。
**內部部署 VMM 伺服器和 Hyper-V 主機**：請確定 VMM 伺服器和 Hyper-V 主機符合 Site Recovery 部署的必要條件並已準備就緒。

移至[步驟 2：確認必要條件和限制](vmm-to-vmm-walkthrough-prerequisites.md)。

## <a name="step-3-plan-networking"></a>步驟 3：規劃網路服務

您必須進行一些網路規劃，以確保部署案例時可以在 VMM VM 網路之間設定網路對應。

移至[步驟 3：規劃網路服務](vmm-to-vmm-walkthrough-network.md)。


## <a name="step-4-prepare-vmm-and-hyper-v"></a>步驟 4：準備 VMM 和 Hyper-V

準備 Site Recovery 部署所需的 VMM 伺服器和 Hyper-V 主機。

移至[步驟 4：準備內部部署伺服器](vmm-to-vmm-walkthrough-vmm-hyper-v.md)。

## <a name="step-5-set-up-a-vault"></a>步驟 5：設定保存庫

設定復原服務保存庫。 保存庫包含組態設定，並協調複寫。

[步驟 5：設定保存庫](vmm-to-vmm-walkthrough-create-vault.md)。

## <a name="step-6-set-up-source-and-target-settings"></a>步驟 6：設定來源和目標設定

設定來源和目標複寫 VMM 位置。 將 VMM 伺服器加入至保存庫，並下載 Site Recovery 元件的安裝檔案。 在 VMM 伺服器上執行 Azure Site Recovery Provider 安裝程式。 安裝程式會在 VMM 伺服器上安裝 Provider，並在保存庫中註冊伺服器。 在每部 Hyper-V 主機上安裝 Microsoft 復原服務代理程式。

移至[步驟 6：設定來源和目標設定](vmm-to-vmm-walkthrough-source-target.md)。

## <a name="step-7-configure-network-mapping"></a>步驟 7：設定網路對應

對應來源和目標位置中的 VMM VM 網路。 在容錯移轉後，會在與來源 Hyper-V VM 所在的來源 VM 網路對應的目標網路中建立 VM。

移至[步驟 7：設定網路對應](vmm-to-vmm-walkthrough-network-mapping.md)。


## <a name="step-8-set-up-a-replication-policy"></a>步驟 8：設定複寫原則

指定 VM 在 VMM 位置之間的複寫方式。

移至[步驟 8：設定複寫原則](vmm-to-vmm-walkthrough-replication.md)。


## <a name="step-9-enable-replication-for-vms"></a>步驟 9：啟用 VM 複寫

選取您要複寫的 VM。 啟用 VM 複寫首先會複寫至次要站台，接著持續進行差異複寫。

移至[步驟 9：啟用複寫](vmm-to-vmm-walkthrough-enable-replication.md)。


## <a name="step-10-run-a-test-failover"></a>步驟 10：執行測試容錯移轉

執行測試容錯移轉，確定一切都沒問題。

移至[步驟 10：執行測試容錯移轉](vmm-to-vmm-walkthrough-test-failover.md)。
