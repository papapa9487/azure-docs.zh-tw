---
title: "將 Azure VM 複寫到另一個 Azure 區域 (預覽)"
description: "本快速入門提供將一個 Azure 區域中的 Azure VM 複寫到不同區域所需的步驟。"
services: site-recovery
author: rajani-janaki-ram
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 369ffed823bc76ee4273d7866935c0ddc7ffa515
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="replicate-an-azure-vm-to-another-azure-region-preview"></a>將 Azure VM 複寫到另一個 Azure 區域 (預覽)

[Azure Site Recovery](site-recovery-overview.md) 服務藉由確保您的商務應用程式可在計劃性與非計劃性中斷期間持續啟動並執行，來提供商務持續性和災害復原 (BCDR) 策略。 Site Recovery 會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的災害復原，包括複寫、容錯移轉和復原。

本快速入門說明如何將 Azure VM 複寫到不同的 Azure 區域。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="enable-replication-for-the-azure-vm"></a>啟用 Azure VM 的複寫

1. 在 Azure 入口網站中，按一下 [虛擬機器]，然後選取您想要複寫的 VM。

2. 在 [設定] 中，按一下 [災害復原 (預覽)]。
3. 在 [設定災害復原] >  [目標區域] 中，選取您要複寫至的目標區域。
4. 在本快速入門中，接受其他預設設定。
5. 按一下 [啟用複寫]。 這會開始一項作業來啟用 VM 的複寫。

    ![啟用複寫](media/azure-to-azure-quickstart/enable-replication1.png)



## <a name="verify-settings"></a>確認設定

在複寫作業完成之後，您可以檢查複寫狀態、修改複寫設定，以及測試部署。

1. 在 VM 功能表中，按一下 [災害復原 (預覽)]。
2. 您可以確認複寫健康情況、已建立的復原點，以及地圖上的來源和目標區域。

   ![複寫狀態](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>清除資源

主要區域中的 VM 會在您停用其複寫功能時停止複寫：

- 來源複寫設定會自動清除。
- VM 的 Site Recovery 計費也會停止。

停止複寫，如下所示︰

1. 選取 VM。
2. 在 [災害復原 (預覽)] 中，按一下 [更多]。
3. 按一下 [停用複寫]。

   ![停用複寫](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>後續步驟

本快速入門中，您以將單一 VM 複寫到次要區域。

> [!div class="nextstepaction"]
> [設定 Azure VM 的災害復原](azure-to-azure-tutorial-enable-replication.md)
