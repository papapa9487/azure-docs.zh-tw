---
title: " 在 Azure Site Recovery 中管理 VMware vCenter Server | Microsoft Doc"
description: "本文說明如何在 Azure Site Recovery 中新增並管理 VMware vCenter。"
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
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 091f0884417535427c52beee7bcdc5ed1dd83315
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>在 Azure Site Recovery 中管理 VMware vCenter Server
本文討論可以在 VMware vCenter 上執行的各種 Site Recovery 作業。

## <a name="prerequisites"></a>必要條件

**支援 VMware vCenter 和 VMware vSphere ESX 主機** | **詳細資料** |
|--- | --- |
|**內部部署 VMware 伺服器** | 一個或多個 VMware vSphere 伺服器 (執行具有最新更新的 6.0、5.5 或 5.1)。 這些伺服器應該位在與設定伺服器 (或另一台處理序伺服器) 相同的網路。<br/><br/> 建議您使用 vCenter 伺服器 (執行具有最新更新的 6.0 或 5.5) 來管理主機。 當您部署 6.0 版時，僅支援 5.5 中可用的功能。|

## <a name="prepare-an-account-for-automatic-discovery"></a>準備帳戶以進行自動探索
Site Recovery 需要存取 VMware，才能讓處理序伺服器自動探索虛擬機器，以及針對虛擬機器進行容錯移轉和容錯回復。

* **移轉**︰如果您只想將 VMware 虛擬機器移轉至 Azure，並不打算進行容錯回復，您可以使用具有唯讀角色的 VMware 帳戶。 這種角色可以執行容錯移轉，但不能關閉受保護的來源電腦。 這針對移轉並非必要。
* **複寫/復原**︰如果您想要部署完整複寫 (複寫、容錯移轉、容錯回復)，則該帳戶必須能夠執行建立和移除磁碟、啟動虛擬機器等作業。
* **自動探索**︰需要至少一個唯讀帳戶。


|**工作** | **必要的帳戶/角色** | **權限** | **詳細資料**|
|--- | --- | --- | ---|
|**處理序伺服器自動探索 VMware 虛擬機器** | 您需要至少一個唯讀使用者 | 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 物件的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。|
|**容錯移轉** | 您需要至少一個唯讀使用者 | 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 物件的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。<br/><br/> 適用於移轉用途，而不是完整複寫、容錯移轉、容錯回復。|
|**容錯移轉和容錯回復** | 建議您建立具有必要權限的角色 (AzureSiteRecoveryRole)，然後將角色指派給 VMware 使用者或群組 | 資料中心物件 –> 傳播至子物件、role=AzureSiteRecoveryRole<br/><br/> 資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案<br/><br/> 網路 -> 網路指派<br/><br/> 資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM<br/><br/> 工作 -> 建立工作、更新工作<br/><br/> 虛擬機器 -> 組態<br/><br/> 虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝<br/><br/> 虛擬機器 -> 清查 -> 建立、註冊、取消註冊<br/><br/> 虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/> 虛擬機器 -> 快照 -> 移除快照 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 物件的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>建立帳戶以連線至 VMware vCenter Server / VMware vSphere EXSi 主機
1. 登入設定伺服器並使用放置於桌面上的捷徑啟動 cspsconfigtool.exe。
2. 按一下 [管理帳戶] 索引標籤上的 [新增帳戶]。

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. 提供帳戶詳細資料並按一下 [確定] 以新增帳戶。 該帳戶應該要具備[準備帳戶以進行自動探索](#prepare-an-account-for-automatic-discovery)一節中列出的權限。

  >[!NOTE]
  帳戶資訊需要花費約 15 分鐘的時間以和 Site Recovery 服務進行同步處理。


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>關聯 VMware vCenter / VMware vSphere ESX 主機 (新增 vCenter)
* 在 Azure 入口網站上，瀏覽至 [YourRecoveryServicesVault]  >  [Site Recovery 基礎結構]  >  [設定伺服器]  >  [ConfigurationServer]
* 在設定伺服器的詳細資料頁面中，按一下 [+vCenter] 按鈕。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>修改用來連線至 vCenter 伺服器 / vSphere ESXi 主機的認證

1. 登入設定伺服器，並啟動 cspsconfigtool.exe
2. 按一下 [管理帳戶] 索引標籤上的 [新增帳戶]。

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. 提供新帳戶詳細資料並按一下 [確定] 以新增帳戶。 該帳戶應該要具備[準備帳戶以進行自動探索](#prepare-an-account-for-automatic-discovery)一節中列出的權限。
4. 在 Azure 入口網站上，瀏覽至 [YourRecoveryServicesVault]  >  [Site Recovery 基礎結構]  >  [設定伺服器]  >  [ConfigurationServer]
5. 在設定伺服器的詳細資料頁面中，按一下 [重新整理伺服器] 按鈕。
6. 當重新整理伺服器作業完成時，選取 [vCenter Server] 以開啟 [vCenter 摘要] 頁面。
7. 在 [vCenter 伺服器/vSphere 主機帳戶] 欄位選取新增的帳戶，然後按一下 [儲存] 按鈕。

  ![modify-account](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>在 Azure Site Recovery 中刪除 vCenter
1. 在 Azure 入口網站上，瀏覽至 [YourRecoveryServicesVault]  >  [Site Recovery 基礎結構]  >  [設定伺服器]  >  [ConfigurationServer]
2. 在設定伺服器的詳細資料頁面中，選取 [vCenter Server] 以開啟 [vCenter 摘要] 頁面。
3. 按一下 [刪除] 按鈕以刪除 vCenter

  ![delete-account](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
如果您需要修改 vCenter IP 位址/FQDN、連接埠詳細資料，您必須刪除 vCenter Server 並再次新增它。
