---
title: "備份 Azure VM | Microsoft Docs"
description: "探索、註冊及備份 Azure 虛擬機器到復原服務保存庫。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "虛擬機器備份; 備份虛擬機器; 備份和災害復原; arm vm 備份"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40983a3de104238d09b976b5fcf2419da42c1bba
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="back-up-azure-virtual-machines-to-a-recovery-services-vault"></a>將 Azure 虛擬機器備份到復原服務保存庫
> [!div class="op_single_selector"]
> * [將 VM 備份到復原服務保存庫](backup-azure-arm-vms.md)
> * [將 VM 備份到備份保存庫](backup-azure-vms.md)
>
>

本文將詳細說明如何將 Azure VM (包括以 Resource Manager 部署和傳統部署的 VM) 備份至復原服務保存庫。 備份 VM 的工作多數是準備。 在備份或保護 VM 之前，您必須完成 [必要條件](backup-azure-arm-vms-prepare.md) 來備妥 VM 的保護環境。 完成必要條件後，您就能初始備份作業來製作 VM 的快照集。


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

如需詳細資訊，請參閱[在 Azure 中規劃 VM 備份基礎結構](backup-azure-vms-introduction.md)和 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)的文章。

## <a name="triggering-the-backup-job"></a>觸發備份作業
與復原服務保存庫相關聯的備份原則會定義備份作業的執行頻率和時間。 根據預設，第一個排定的備份是初始備份。 在執行初始備份之前，[備份作業] 刀鋒視窗上的 [上次備份狀態] 會顯示為 [警告 (待執行初始備份)]。

![待備份](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

除非您的初始備份預計會馬上開始，否則建議您執行 [立即備份] 。 以下程序從保存庫儀表板開始。 當您完成所有必要條件後，此程序可用來執行初始備份作業。 如果您已執行過初始備份作業，此程序便不適用。 相關聯的備份原則決定後續的備份作業。  

若要執行初始備份作業：

1. 在保存庫儀表板中，按一下 [備份項目] 下的數字，或按一下 [備份項目] 圖格。 <br/>
  ![設定圖示](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  [備份項目]  刀鋒視窗隨即開啟。

  ![備份項目](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. 在 [備份項目] 刀鋒視窗中，選取該項目。

  ![設定圖示](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  [備份項目] 清單隨即開啟。 <br/>

  ![備份作業已觸發](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. 在 [備份項目] 清單上，按一下省略符號 **...** 以開啟操作功能表。

  ![內容功能表](./media/backup-azure-vms-first-look-arm/context-menu.png)

  操作功能表隨即出現。

  ![內容功能表](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. 在操作功能表上，按一下 [立即備份]。

  ![內容功能表](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  [立即備份] 刀鋒視窗隨即開啟。

  ![顯示 [立即備份] 刀鋒視窗](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. 在 [立即備份] 刀鋒視窗上，按一下日曆圖示，使用日曆控制項選取此復原點保留的最後一天，然後按一下 [備份]。

  ![設定 [立即備份] 復原點保留的最後一天](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  部署通知可讓您知道已觸發備份工作，而且您可以在 [備份工作] 頁面上監視作業的進度。 根據您的 VM 大小，建立初始備份可能需要花一點時間。

6. 若要在保存庫儀表板上檢視或追蹤初始備份的狀態，請在 [備份工作] 圖格上，按一下 [進行中]。

  ![備份工作圖格](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  [備份工作] 刀鋒視窗隨即開啟。

  ![備份工作圖格](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  在 [備份作業]  刀鋒視窗中，您可以看到所有作業的狀態。 檢查您 VM 的備份作業是否仍在進行中或已經完成。 當備份工作完成時，狀態會是「完成」 。

  > [!NOTE]
  > 在備份工作進行時，Azure 備份服務會對每個 VM 中的備份擴充功能發出命令，以排清所有寫入並取得一致的快照。
  >
  >

## <a name="troubleshooting-errors"></a>錯誤疑難排解
如果您在備份虛擬機器時遇到問題，請參閱 [VM 疑難排解文章](backup-azure-vms-troubleshoot.md)以取得說明。

## <a name="next-steps"></a>後續步驟
現在您已能保護您的 VM，請參閱下列文章，以了解 VM 管理工作及如何還原 VM。

* [管理和監視虛擬機器](backup-azure-manage-vms.md)
* [還原虛擬機器](backup-azure-arm-restore-vms.md)
