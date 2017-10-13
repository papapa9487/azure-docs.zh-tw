---
title: "將 VM 從 AWS 移轉到 Azure| Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 將 Amazon Web Services (AWS) 中執行的虛擬機器移轉至 Azure。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: b3c0727a279649f4f7dae30d41027129ce5b04ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>使用 Azure Site Recovery 將 Amazon Web Services (AWS) 中的虛擬機器移轉至 Azure

本文說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 服務將 AWS Windows 執行個體移轉到 Azure 虛擬機器。

移轉實際上是從 AWS 容錯移轉至 Azure。 您無法從機器容錯移轉至 AWS，且不會進行複寫。 本文說明在 Azure 入口網站中進行移轉的步驟，且是以[將實體機器複寫到 Azure](site-recovery-vmware-to-azure.md)的指示為基礎。

請在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>受支援的作業系統

Site Recovery 可用於移轉執行下列任何作業系統的 EC2 執行個體：

- Windows (僅 64 位元)
    - Windows Server 2008 R2 SP1+ (僅限 Citrix PV 驅動程式或 AWS PV 驅動程式。 **不支援執行 RedHat PV 驅動程式的執行個體**) Windows Server 2012 Windows Server 2012 R2
- Linux (僅 64 位元)
    - Red Hat Enterprise Linux 6.7 (只有 HVM 虛擬化執行個體)

## <a name="prerequisites"></a>必要條件

以下是您針對此部署所需要的項目︰

* **組態伺服器**︰執行 Windows Server 2012 R2 的 Amazon EC2 VM 部署為組態伺服器。 根據預設，當您部署設定伺服器時，會安裝其他 Azure Site Recovery 元件 (處理序伺服器和主要目標伺服器)。 本文說明在 Azure 入口網站中進行移轉的步驟，且是以[進一步了解](site-recovery-components.md)的指示為基礎

* **EC2 執行個體**︰您想要移轉的 Amazon EC2 虛擬機器執行個體。

## <a name="deployment-steps"></a>部署步驟

1. 建立復原服務保存庫。
2. EC2 執行個體的安全性群組應該有設定的規則，允許您要移轉的 EC2 執行個體與您打算部署設定伺服器的執行個體之間進行通訊。

3. 在與 EC2 執行個體相同的 Amazon 虛擬私人雲端上，部署 Azure Site Recovery 組態伺服器。 關於組態伺服器部署需求，請參閱 Azure 的 VMware/實體必要條件。

    ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.  一旦將組態伺服器部署在 AWS 中，並向您的復原服務保存庫註冊後，您應該會在 Site Recovery 基礎結構下看到組態伺服器和處理序伺服器，如下所示︰

    ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)

5. 部署組態伺服器之後 (可能需要長達 15 分鐘才會出現)，請驗證該伺服器否能夠與您要移轉的 VM 通訊。

6. [設定複寫設定](site-recovery-setup-replication-settings-vmware.md)。

7. 啟用複寫：針對您想要移轉的 VM 啟用複寫。 您可以使用可從 EC2 主控台取得的私人 IP 位址探索 EC2 執行個體。

    ![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)

8. 一旦 EC2 執行個體已受保護，且複寫至 Azure 完成後，在 Azure 中[執行測試容錯移轉](site-recovery-test-failover-to-azure.md)來驗證您的應用程式效能。

    ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. 為每個 VM 執行從 AWS 容錯移轉至 Azure。 (選擇性) 您可以建立復原計劃並執行容錯移轉，將多部虛擬機器從 AWS 移轉至 Azure。 [深入了解](site-recovery-create-recovery-plans.md) 復原計劃。

10. 若是進行移轉，您不需要認可容錯移轉。 相反地，您要為所要移轉的每一部機器選取 [完成移轉] 選項。 [完成移轉] 動作會完成移轉程序、移除機器的複寫，並停止該機器的 Site Recovery 計費。

    ![移轉](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

## <a name="next-steps"></a>後續步驟

- [準備已移轉的機器，以便複寫](site-recovery-azure-to-azure-after-migration.md)至其他區域以因應災害復原的需要。
- [複寫 Azure 虛擬機器](site-recovery-azure-to-azure.md)來開始保護您的工作負載。
