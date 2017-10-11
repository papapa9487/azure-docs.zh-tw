---
title: "使用 Azure Site Recovery 將實體內部部署伺服器複寫至 Azure | Microsoft Docs"
description: "簡述使用 Azure Site Recovery 服務將在內部部署 Windows/Linux 實體伺服器上執行的工作負載複寫至 Azure 所需的步驟。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>使用 Site Recovery 將實體伺服器複寫至 Azure

本文概述在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署 Windows/Linux 實體伺服器複寫至 Azure 所需的步驟。


## <a name="step-1-review-architecture-and-prerequisites"></a>步驟 1：檢閱架構和必要條件

在開始部署之前，請檢閱案例架構，並確定您了解設定部署所需的一切元件。

移至[步驟 1：檢閱架構](physical-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>步驟 2：檢閱必要條件

請確定您已備妥每個部署元件的必要條件：

- **Azure 必要條件**：您需要 Microsoft Azure 帳戶、Azure 網路及儲存體帳戶。
- **內部部署 Site Recovery 元件**：您需要有一部執行內部部署 Site Recovery 元件的機器。
- **複寫的機器**：您想要複寫的伺服器必須符合內部部署環境與 Azure 的需求。

移至[步驟 2：檢閱必要條件和限制](physical-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>步驟 3：規劃容量

如果您要執行完整部署，就必須了解需要哪些複寫資源。 如果您要進行快速設定來測試環境，則可以略過此步驟。

移至[步驟 3：規劃容量](physical-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>步驟 4：規劃網路

您必須進行一些網路規劃，以確保 Azure VM 在發生容錯移轉之後會連線到網路，並且它們具有正確的 IP 位址。

移至[步驟 4：規劃網路](physical-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>步驟 5：準備 Azure 資源

在開始之前，請先設定 Azure 網路和儲存體。 

移至[步驟 5：準備 Azure](physical-walkthrough-prepare-azure.md)


## <a name="step-6-set-up-a-vault"></a>步驟 6：設定保存庫

您需設定「復原服務」保存庫來協調和管理複寫。 當您設定保存庫時，請指定您想要複寫的項目，以及要將它複寫到何處。

移至[步驟 6：設定保存庫](physical-walkthrough-create-vault.md)

## <a name="step-7-configure-source-and-target-settings"></a>步驟 7：設定來源和目標設定

設定來源和目標 (Azure) 站台的設定。 來源設定包括執行「整合安裝」來安裝內部部署 Site Recovery 元件。

移至[步驟 7：設定來源和目標](physical-walkthrough-source-target.md)

## <a name="step-8-set-up-a-replication-policy"></a>步驟 8：設定複寫原則

您需設定原則來指定實體伺服器應該如何進行複寫。

移至[步驟 8：設定複寫原則](physical-walkthrough-replication.md)

## <a name="step-9-install-the-mobility-service"></a>步驟 9：安裝行動服務

行動服務必須安裝在您要複寫的每部伺服器上。 有幾個方法可藉由推入或提取安裝來安裝服務。

移至[步驟 9：安裝行動服務](physical-walkthrough-install-mobility.md)

## <a name="step-10-enable-replication"></a>步驟 10：啟用複寫

在行動服務於伺服器上執行之後，您便可以為其啟用複寫。 啟用複寫之後，就會進行初始 VM 複寫。

移至[步驟 10：啟用複寫](physical-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>步驟 11：執行測試容錯移轉

在初始複寫完成且差異複寫執行之後，您可以執行測試容錯移轉，以確定一切如預期般運作。

移至[步驟 11：執行測試容錯移轉](physical-walkthrough-test-failover.md)

