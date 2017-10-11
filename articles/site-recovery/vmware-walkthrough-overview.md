---
title: "使用 Azure Site Recovery 將 VMware VM 複寫至 Azure | Microsoft Docs"
description: "概述將 VMware VM 上執行的工作負載複寫至 Azure 所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: db6f5f95929503e82a529dba26b56af1edb0767f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-vmware-vms-to-azure-with-site-recovery"></a>使用 Site Recovery 將 VMWare VM 複寫至 Azure

本文概述在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署 VMware 虛擬機器複寫至 Azure 所需的步驟。


![部署程序](./media/vmware-walkthrough-overview/vmware-to-azure-process.png)

**圖 1：部署程序摘要**

## <a name="step-1-review-architecture-and-prerequisites"></a>步驟 1：檢閱架構和必要條件

在開始部署之前，請檢閱案例架構，並確定您了解部署所需的一切元件

移至[步驟 1：檢閱架構](vmware-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>步驟 2：檢閱必要條件

請確定您已備妥每個部署元件的必要條件：

- **Azure 必要條件**：您需要 Microsoft Azure 帳戶、Azure 網路及儲存體帳戶。
- **內部部署 Site Recovery 元件**：您需要有一部執行內部部署 Site Recovery 元件的機器。
- **內部部署 VMware 必要條件**：您需要設定帳戶，以便讓 Site Recovery 能夠存取 VMware 伺服器和 VM。
- **複寫的 VM**：您想要複寫的 VM 必須符合 Azure 需求，並且已安裝行動服務元件。

移至[步驟 2：檢閱必要條件和限制](vmware-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>步驟 3：規劃容量

如果您要執行完整部署，就必須了解需要哪些複寫資源。 有幾個工具可幫助您執行這項操作。 移至步驟 2。 如果您要進行快速設定來測試環境，則可以略過此步驟。

移至[步驟 3：規劃容量](vmware-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>步驟 4：規劃網路

您必須進行一些網路規劃，以確保 Azure VM 在發生容錯移轉之後會連線到網路，並且它們具有正確的 IP 位址。

移至[步驟 4：規劃網路](vmware-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>步驟 5：準備 Azure 資源

在開始之前，請先設定 Azure 網路和儲存體。 您可以在部署期間執行這項操作，但建議您在開始之前進行。

移至[步驟 5：準備 Azure](vmware-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-vmware"></a>步驟 6：準備 VMware

您必須設定 Site Recovery 將用來執行下列操作的帳戶：

- 存取 VMware 虛擬化伺服器以自動偵測 VM。
- 存取 VM 以安裝行動服務。 您想要複寫的每個 VM 都必須先安裝行動服務代理程式，您才能為其啟用複寫。

移至[步驟 6：準備 VMware](vmware-walkthrough-prepare-vmware.md)

## <a name="step-7-set-up-a-vault"></a>步驟 7：設定保存庫

您必須設定「復原服務」保存庫，才能協調和管理複寫。 當您設定保存庫時，請指定您想要複寫的項目，以及要將它複寫到何處。

移至[步驟 7：設定保存庫](vmware-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>步驟 8：設定來源和目標設定

設定用於複寫的來源和目標。 設定來源設定包括執行「整合安裝」來安裝內部部署 Site Recovery 元件。

移至[步驟 8：設定來源和目標](vmware-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>步驟 9：設定複寫原則

您需設定原則來為保存庫中的 VMware VM 指定複寫設定。

移至[步驟 9：設定複寫原則](vmware-walkthrough-replication.md)

## <a name="step-10-install-the-mobility-service"></a>步驟 10：安裝行動服務

行動服務必須安裝在您要複寫的每個 VM 上。 有幾個方法可藉由推入或提取安裝來安裝服務。

移至[步驟 10：安裝行動服務](vmware-walkthrough-install-mobility.md)

## <a name="step-11-enable-replication"></a>步驟 11：啟用複寫

在行動服務於 VM 上執行之後，您便可以為其啟用複寫。 啟用複寫之後，就會進行初始 VM 複寫。

移至[步驟 11：啟用複寫](vmware-walkthrough-enable-replication.md)

## <a name="step-12-run-a-test-failover"></a>步驟 12：執行測試容錯移轉

在初始複寫完成且差異複寫執行之後，您可以執行測試容錯移轉，以確定一切如預期般運作。

移至[步驟 12：執行測試容錯移轉](vmware-walkthrough-test-failover.md)
