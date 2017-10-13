---
title: "建立要搭配 Azure Site Recovery 使用的資源 | Microsoft Docs"
description: "了解如何準備 Azure，以使用 Azure Site Recovery 服務來進行內部部署機器的複寫。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 321e304f-b29e-49e4-aa64-453878490ea7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 506b625905abf52963230a787af66f956bc292b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>準備 Azure 資源以進行內部部署機器的複寫

[Azure Site Recovery](site-recovery-overview.md) 服務藉由確保您的商務應用程式可在計劃性與非計劃性中斷期間持續啟動並執行，來提供商務持續性和災害復原 (BCDR) 策略。 Site Recovery 會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的災害復原，包括複寫、容錯移轉和復原。

本教學課程說明如何在您想要將內部部署 VM 和實體伺服器複寫至 Azure 時，準備好 Azure 元件。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 確認您的帳戶具有複寫權限
> * 建立 Azure 儲存體帳戶。
> * 設定 Azure 網路。 當 Azure VM 在容錯移轉後建立時，會加入此 Azure 網路。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。

## <a name="log-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="verify-account-permissions"></a>確認帳戶權限

如果您剛建立免費的 Azure 帳戶，則您會是訂用帳戶的系統管理員。 如果您不是訂用帳戶系統管理員，請與系統管理員合作以指派您所需的權限。 若要啟用新虛擬機器的複寫，您必須具有：

- 在所選資源群組中建立虛擬機器的權限
- 在所選虛擬網路中建立虛擬機器的權限
- 寫入所選儲存體帳戶的權限

具有這些權限的「虛擬機器參與者」內建角色。 您也需要管理 Azure Site Recovery 作業的權限。 「Site Recovery 參與者」角色具有在復原服務保存庫中管理 Site Recovery 作業所需的所有權限。

## <a name="create-a-storage-account"></a>建立儲存體帳戶

複寫機器的映像會保留在 Azure 儲存體中。 當您從內部部署容錯移轉至 Azure 時，會從儲存體建立 Azure VM。

1. 在 [Azure 入口網站](https://portal.azure.com)功能表中，按一下 **[新增]** -> **[儲存體]** -> **[儲存體帳戶]**。
2. 輸入儲存體帳戶的名稱。 在這些教學課程中，我們將使用名稱 **contosovmsacct1910171607**。 此名稱必須是 Azure 中的唯一名稱，並介於 3 到 24 個字元，而且只能包含數字和小寫字母。
3. 使用 **Resource Manager** 部署模型。
4. 選取 [一般目的] > [標準]。
5. 針對儲存體備援選取預設的 **RA-GRS**。
6. 選取您要在其中建立新儲存體帳戶的訂用帳戶。
7. 指定新的資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在這些教學課程中，我們使用名稱 **ContosoRG**。
8. 選取儲存體帳戶的地理位置。 儲存體帳戶與復原服務保存庫必須位於相同的區域。 在這些教學課程中，我們使用位置 [西歐]。

   ![create-storageacct](media/tutorial-prepare-azure/create-storageacct.png)

9. 按一下 [建立]  建立儲存體帳戶。

## <a name="create-a-vault"></a>建立保存庫

1. 在 Azure 入口網站功能表中，按一下 **[新增]** > **[監視和管理]** >
   **[備份與復原]**。
2. 在 [名稱] 中，指定保存庫的易記識別名稱。 在此教學課程中，我們使用 **ContosoVMVault**。
3. 選取名為 **contosoRG** 的現有資源群組。
4. 指定 Azure 區域 [西歐]。
5. 若要從儀表板快速存取保存庫，請按一下 [釘選到儀表板] > [建立]。

   ![新增保存庫](./media/tutorial-prepare-azure/new-vault-settings.png)

   新的保存庫會出現在 [儀表板] > [所有資源] 上，以及主要 [復原服務保存庫] 頁面上。

## <a name="set-up-an-azure-network"></a>設定 Azure 網路

當 Azure VM 在容錯移轉後從儲存體建立時，會加入此網路。

1. 在 [Azure 入口網站](https://portal.azure.com)功能表中，按一下 **[新增]** > **[網路]** >
   **[虛擬網路]**
2. 保持選取 [Resource Manager] 作為部署模型。 Resource Manager 是慣用的部署模型。
   - 指定網路名稱。 此名稱必須是 Azure 資源群組中的唯一名稱。 我們將使用名稱 **ContosoASRnet**
   - 使用現有的資源群組 **contosoRG**。
   - 指定網路位址範圍 10.0.0.0/24。
   - 在此教學課程中，我們不需要子網路。
   - 選取要在其中建立網路的訂用帳戶。
   - 選取位置 [西歐]。 此網路必須位於與復原服務保存庫相同的區域中。
3. 按一下 [建立] 。

   ![create-network](media/tutorial-prepare-azure/create-network.png)

   虛擬網路的建立會耗用幾秒鐘時間。 建立後，您會在 Azure 入口網站儀表板中看到它。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [準備內部部署 VMware 基礎結構以進行 Azure 的災害復原](tutorial-prepare-on-premises-vmware.md)
