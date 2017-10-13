---
title: "在 Azure 區域之間複寫 Azure VM | Microsoft Docs"
description: "摘要列出在 Azure 入口網站中使用 Azure Site Recovery 服務，在 Azure 區域之間複寫 Azure VM 所需的步驟。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在區域之間椱寫 Azure VM

>本文概要說明將一個 Azure 區域中的 Azure 虛擬機器 (VM) 複寫至不同區域中的 Azure VM 所需的步驟。 

>[!NOTE]
>
> Azure VM 複寫目前為預覽狀態。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) (英文) 上張貼意見或問題。

## <a name="step-1-review-architecture"></a>步驟 1：檢閱架構

在開始部署之前，請檢閱情節架構以及部署所需的元件。

移至[步驟 1：檢閱架構](azure-to-azure-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>步驟 2：檢閱必要條件

請檢查您已具備 Azure 的必要條件，包括訂用帳戶、虛擬網路、儲存體帳戶和 VM 需求。

移至[步驟 2：確認必要條件和限制](azure-to-azure-walkthrough-prerequisites.md)


## <a name="step-3-plan-networking"></a>步驟 3：規劃網路服務

請檢查已在您需要複寫的 Azure VM 上設定輸出連線能力，且已設定內部部署的連線。

移至[步驟 4：規劃網路](azure-to-azure-walkthrough-network.md)



## <a name="step-4-create-a-vault"></a>步驟 4：建立保存庫 

您必須設定「復原服務」保存庫，才能協調和管理複寫，並指定來源區域。

移至[步驟 4：建立保存庫](azure-to-azure-walkthrough-vault.md)


## <a name="step-5-enable-replication"></a>步驟 5：啟用複寫


若要啟用複寫，您可以設定目標位置設定、設定複寫原則，並選取您需要複寫的 Azure VM。 啟用複寫之後，就會進行初始 VM 複寫。

移至[步驟 5：啟用複寫](azure-to-azure-walkthrough-enable-replication.md)


## <a name="step-6-run-a-test-failover"></a>步驟 6：執行測試容錯移轉

在初始複寫完成且差異複寫執行之後，您可以執行測試容錯移轉，以確定一切如預期般運作。

移至[步驟 6：執行測試容錯移轉](azure-to-azure-walkthrough-test-failover.md)


