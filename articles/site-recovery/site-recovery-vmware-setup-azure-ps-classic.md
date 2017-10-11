---
title: " 管理在 Azure 中執行的處理伺服器 (傳統) | Microsoft Docs"
description: "此文章說明如何在 Azure 中設定容錯回復處理伺服器 (傳統)。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 479bbd207bcf715138c340f9e4d2634120bab85c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="manage-a-process-server-running-in-azure-classic"></a>管理在 Azure 中執行的處理伺服器 (傳統)
> [!div class="op_single_selector"]
> * [Azure 傳統](./site-recovery-vmware-setup-azure-ps-classic.md)
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

在容錯回復期間，如果 Azure 虛擬網路和您的內部部署網路之間發生高延遲，則建議在 Azure 中部署處理伺服器。 此文章說明如何設定、配置及管理在 Azure 中執行的處理伺服器。

> [!NOTE]
> 如果在容錯移轉期間使用「傳統」作為虛擬機器部署模型，可以參考此文章。 如果您使用 Resource Manager 作為部署模型，請依照[如何設定及配置容錯回復處理伺服器 (Resource Manager)](./site-recovery-vmware-setup-azure-ps-resource-manager.md) 中的步驟執行

## <a name="prerequisites"></a>必要條件

[!INCLUDE [site-recovery-vmware-process-server-prereq](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>在 Azure 上部署處理伺服器

1. 在 Azure Marketplace 中，使用 **Microsoft Azure Site Recovery 處理伺服器 V2** 建立虛擬機器 </br>
    ![Marketplace_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image.png)
2. 請確定您選取**傳統**做為部署模型 </br>
  ![Marketplace_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image-classic.png)
3. 在 [建立虛擬機器精靈] > [基本設定] 中，請確定您選取要容錯回復虛擬機器的訂用帳戶和位置。</br>
  ![create_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-basic-info.png)
4. 請確認虛擬機器已連線到容錯回復虛擬機器連線的 Azure 虛擬網路。</br>
  ![create_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-settings.png)
5. 一旦佈建處理伺服器虛擬機器，您需要登入並向組態伺服器註冊它。

> [!NOTE]
> 為了能夠使用此處理伺服器進行容錯回復，您必須向內部部署組態伺服器註冊它。

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>將處理伺服器 (在 Azure 中執行) 註冊到組態伺服器 (在內部部署上執行)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>將處理伺服器升級到最新版本。

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>從組態伺服器 (在內部部署上執行) 取消註冊處理伺服器 (在 Azure 中執行)

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
