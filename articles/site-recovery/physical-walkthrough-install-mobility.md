---
title: "針對實體伺服器到 Azure 的複寫安裝行動服務 | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 服務，在複寫至 Azure 的實體伺服器上安裝行動服務代理程式。"
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
ms.openlocfilehash: d73267d7a64221a3138af19e9a2d5dd15809b927
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="step-9-install-the-mobility-service"></a>步驟 9：安裝行動服務


本文說明在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署 Windows/Linux 實體伺服器複寫至 Azure 時，如何安裝行動服務元件。

行動服務會擷取機器上的資料寫入，然後將它們轉送到處理伺服器。 此服務應該安裝在您想要複寫至 Azure 的每部伺服器上。

您可以透過手動方式、使用來自 Site Recovery 處理伺服器的推入安裝 (當啟用複寫時)，或使用 System Center Configuration Manager 之類的工具，來安裝行動服務。 如果您使用推入安裝，則當您啟用複寫時，服務就會安裝在伺服器上。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。

## <a name="install-manually"></a>手動安裝

1. 查看[必要條件](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites)來進行手動安裝。
2. 依照[這些指示](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)來使用入口網站進行手動安裝。
3. 如果您偏好從命令列進行安裝，請依照[這些指示](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)操作。

## <a name="install-from-the-process-server"></a>從處理伺服器安裝

如果您想要在為機器啟用複寫時，從處理伺服器推送行動服務安裝，則需要有一個可供處理伺服器用來存取該機器的帳戶。 此帳戶僅用於推入安裝。

1. 如果您尚未建立帳戶，請參考下列指導方針來進行此操作：

    - 您可以使用網域或本機帳戶
    - 在 Windows 上，如果您不使用網域帳戶，則必須停用本機電腦上的遠端使用者存取控制。 若要這樣做，請在登錄的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，新增 DWORD 項目 **LocalAccountTokenFilterPolicy**，值為 1。
    - 如果您想要從 CLI 新增適用於 Windows 的登錄項目，請輸入︰

        ```
        REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
        ```

    - 在 Linux 上，帳戶應該是來源 Linux 伺服器上的根使用者。

2. 接著，如果您想要在執行 Windows 或 Linux 的 VM 上推送行動服務，請依照[這些指示](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)操作。

## <a name="other-installation-methods"></a>其他安裝方法

- [了解](site-recovery-install-mobility-service-using-sccm.md)如何使用 Configuration Manager 來安裝行動服務
- [了解](site-recovery-automate-mobility-service-install.md)如何使用 Azure Automation DSC 來進行安裝。


## <a name="next-steps"></a>後續步驟

移至[步驟 10：啟用複寫](physical-walkthrough-enable-replication.md)
