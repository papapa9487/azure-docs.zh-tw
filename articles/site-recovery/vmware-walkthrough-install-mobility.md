---
title: "針對 VMware 到 Azure 的複寫安裝行動服務 | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 服務，針對 VMware 到 Azure 的複寫安裝行動服務代理程式。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-install-the-mobility-service"></a>步驟 10：安裝行動服務


本文說明在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署 VMware 虛擬機器複寫至 Azure 時，如何設定來源和目標設定。

行動服務會擷取機器上的資料寫入，然後將它們轉送到處理伺服器。 此服務應該安裝在您想要複寫至 Azure 的每部機器上。

您可以透過手動方式、使用來自 Site Recovery 處理伺服器的推入安裝 (當啟用複寫時)，或使用 System Center Configuration Manager 工具，來安裝行動服務。 如果您使用推入安裝，則在啟用複寫時，服務就會安裝在 VM 上。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。

## <a name="install-manually"></a>手動安裝

1. 查看[必要條件](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites)來進行手動安裝。
2. 依照[這些指示](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)來使用入口網站進行手動安裝。
3. 如果您偏好從命令列進行安裝，請依照[這些指示](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)操作。

## <a name="install-from-the-process-server"></a>從處理伺服器安裝

如果您想要在為 VM 啟用複寫時，從處理伺服器推送行動服務安裝，則需要有一個可供處理伺服器用來存取該 VM 的帳戶。 此帳戶僅用於推入安裝。

1. 您應該[已建立一個帳戶](vmware-walkthrough-prepare-vmware.md)來供推入安裝使用。 接著，您可以指定進行 Site Recovery 部署期間，在設定來源設定時要使用的帳戶。
2. 接著，如果您想要在執行 Windows 或 Linux 的 VM 上推送行動服務，請依照[這些指示](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)操作。

## <a name="other-methods"></a>其他方法

了解如何[使用 Configuration Manager](site-recovery-install-mobility-service-using-sccm.md) 或 [Azure Automation DSC](site-recovery-automate-mobility-service-install.md) 來安裝行動服務。

## <a name="next-steps"></a>後續步驟

移至[步驟 11：啟用複寫](vmware-walkthrough-enable-replication.md)
