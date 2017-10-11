---
title: " 管理在 Azure 中執行的處理伺服器 (Resource Manager) | Microsoft Doc"
description: "使文章說明如何在 Azure 中設定容錯回復處理伺服器 (Resource Manager)。"
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
ms.openlocfilehash: 2b9b31abd5d11d02935a74e47d26be9803cdc920
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>管理在 Azure 中執行的處理伺服器 (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [傳統](./site-recovery-vmware-setup-azure-ps-classic.md)

在容錯回復期間，如果 Azure 虛擬網路和您的內部部署網路之間發生高延遲，則建議在 Azure 中部署處理伺服器。 此文章說明如何設定、配置及管理在 Azure 中執行的處理伺服器。

> [!NOTE]
> 若要在容錯回復期間使用 **Resource Manager** 做為虛擬機器部署模型，可以參考此文章。 如果您使用「傳統」部署模型，請依照[如何設定及配置容錯回復處理序伺服器 (傳統)](./site-recovery-vmware-setup-azure-ps-classic.md) 中的步驟執行

## <a name="prerequisites"></a>必要條件

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>在 Azure 上部署處理伺服器
1. 在 保存庫 > 位於 管理 標題下方的 Site Recovery 基礎結構 > 位於 適用於 VMware 與實體機器 標題下方 組態伺服器 中，選取組態伺服器。
2. 在開啟的組態伺服器詳細資料頁面中按一下 [+ 處理伺服器]

  ![新增處理伺服器資源庫](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  在 [新增處理伺服器] 頁面上，選取下列值

  ![新增處理伺服器資源庫項目](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**欄位名稱**|**值**|
|-|-|
|選擇您要部署處理伺服器的位置|選取 [在 Azure 中部署容錯回復處理伺服器] 值 |
|訂用帳戶|選取容錯回復虛擬機器的 Azure 訂用帳戶|
|資源群組|您可以建立要部署此處理伺服器的資源群組，或選擇要在其中部署處理伺服器的現有資源群組|
|位置|選取做為虛擬機器容錯回復目標的 Azure 資料中心|
|Azure 網路|選取做為虛擬機器容錯回復目標的 Azure 虛擬網路 (VNet) 如果將虛擬機器容錯回復到多個 Azure VNet，則您需要在每個 VNet 上部署一部處理伺服器|

4. 填入其餘的處理伺服器內容

  ![新增處理伺服器摘要](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**欄位名稱**|**值**|
|-|-|
|伺服器名稱|處理伺服器虛擬機器的顯示名稱與主機名稱|
| 使用者名稱|會成為該虛擬機器上系統管理員的使用者名稱|
|儲存體帳戶|放置虛擬機器之虛擬磁碟的儲存體帳戶名稱|
|子網路|虛擬機器應該連接之 Azure VNet 的子網路|
| IP 位址|您想要處理伺服器一旦開機後要採用的 IP 位址|
5. 按一下 [確定] 按鈕來開始部署處理伺服器虛擬機器。

> [!NOTE]
> 為了能夠使用此處理伺服器進行容錯回復，您必須向內部部署組態伺服器註冊它。

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>將處理伺服器 (在 Azure 中執行) 註冊到組態伺服器 (在內部部署上執行)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>將處理伺服器升級到最新版本。

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>從組態伺服器 (在內部部署上執行) 取消註冊處理伺服器 (在 Azure 中執行)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
