---
title: "使用 Azure Site Recovery 針對實體伺服器到 Azure 的複寫設定來源和目標 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 服務針對將實體伺服器複寫至 Azure 儲存體設定來源和目標設定時所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>步驟 7：針對實體伺服器到 Azure 的複寫設定來源和目標

本文說明在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署實體伺服器複寫至 Azure 時，如何設定來源和目標設定。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。


## <a name="set-up-the-source-environment"></a>設定來源環境

設定組態伺服器、在保存庫中註冊它，然後探索機器。

1. 按一下 [Site Recovery] > [步驟 1: 準備基礎結構] > [來源]。
2. 如果您沒有設定伺服器，請按一下 [+設定伺服器]。
3. 在 [新增伺服器] 中，檢查 [設定伺服器] 是否出現在 [伺服器類型] 中。
4. 下載 Site Recovery 統一安裝的安裝檔案。
5. 下載保存庫註冊金鑰。 您會在執行統一安裝時用到此金鑰。 該金鑰在產生後會維持 5 天有效。

   ![設定來源](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>在保存庫中註冊設定伺服器

開始之前請先執行下列作業，然後執行統一安裝以安裝組態伺服器、處理序伺服器與主要目標伺服器。 影片說明如何針對 VMware VM 到 Azure 的複寫設定元件。 不過，相同程序也適用於實體伺服器到 Azure 的複寫。

- 在組態伺服器 VM 上，確定系統時鐘與[時間伺服器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步。 應該相符。 如果快慢誤差 15 分鐘，安裝可能會失敗。
- 在組態伺服器機器上，以本機系統管理員身分執行安裝程式。
- 確定 VM 上已啟用 TLS 1.0


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 您也可以[從命令列](http://aka.ms/installconfigsrv)安裝組態伺服器。




## <a name="set-up-the-target-environment"></a>設定目標環境

設定目標環境之前，請確定您已備妥 Azure 儲存體帳戶和虛擬網路。

1. 按一下 [準備基礎結構] > [目標]，然後選取您要使用的 Azure 訂用帳戶。
2. 指定目標部署模型是以 Resource Manager 為基礎或傳統。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

   ![目標](./media/physical-walkthrough-source-target/gs-target.png)

4. 如果您尚未建立儲存體帳戶或網路，請按一下 [+儲存體帳戶] 或[+網路]，以建立 Resource Manager 帳戶或網路內嵌。

## <a name="next-steps"></a>後續步驟

移至[步驟 8：設定複寫原則](physical-walkthrough-replication.md)
