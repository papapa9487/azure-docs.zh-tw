---
title: "使用 Azure Site Recovery 針對 VMware 到 Azure 的複寫設定來源和目標 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 針對將 VMware VM 複寫至 Azure 儲存體設定來源和目標設定時所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99e422e-daf7-4fa8-af3c-af2340340136
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94b629a62c3a54eee69ee397b2f27e3f20b753d5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-vmware-replication-to-azure"></a>步驟 8：針對 VMware 到 Azure 的複寫設定來源和目標

本文說明在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署 VMware 虛擬機器複寫至 Azure 時，如何設定來源和目標設定。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。


## <a name="set-up-the-source-environment"></a>設定來源環境

安裝設定伺服器、註冊在保存庫及探索 VM。

1. 按一下 [Site Recovery] > [步驟 1: 準備基礎結構] > [來源]。
2. 如果您沒有設定伺服器，請按一下 [+設定伺服器]。
3. 在 [新增伺服器] 中，檢查 [設定伺服器] 是否出現在 [伺服器類型] 中。
4. 下載 Site Recovery 統一安裝的安裝檔案。
5. 下載保存庫註冊金鑰。 您會在執行統一安裝時用到此金鑰。 該金鑰在產生後會維持 5 天有效。

   ![設定來源](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>在保存庫中註冊設定伺服器

開始之前請先執行下列作業，然後執行統一安裝以安裝組態伺服器、處理序伺服器與主要目標伺服器。
    - 透過影片快速建立概念

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - 在組態伺服器 VM 上，確定系統時鐘與[時間伺服器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步。 應該相符。 如果快慢誤差 15 分鐘，安裝可能會失敗。
    - 在設定伺服器 VM 上以本機系統管理員身分執行安裝程式。
    - 確定 VM 上已啟用 TLS 1.0


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 您也可以[從命令列](http://aka.ms/installconfigsrv)安裝組態伺服器。



## <a name="connect-to-vmware-servers"></a>連接至 VMware 伺服器

若要允許 Azure Site Recovery 探索在您內部部署環境中執行的虛擬機器，您必須將 VMware vCenter Server 或 vSphere ESXi 主機與 Site Recovery 連接。 開始之前，請注意下列事項：

- 如果您使用在伺服器上沒有系統管理員權限的帳戶將 vCenter 伺服器或 vSphere 主機新增到 Site Recovery，則該帳戶必須啟用下列權限︰
    - 資料中心、資料存放區、資料夾、主機、網路、資源、虛擬機器、vSphere 分散式交換器。
    - vCenter 伺服器需要儲存體檢視權限。
- 將 VMware 伺服器新增到 Site Recovery 時，可能需要 15 分鐘或更久，它們才會出現在入口網站中。

### <a name="add-the-account-for-automatic-discovery"></a>新增用於自動探索的帳戶

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="set-up-a-connection"></a>設定連線

請依照下列方式連線到伺服器：

1. 選取 [+vCenter] 來開始連接 VMware vCenter Server 或 VMware vSphere ESXi 主機。
2. 在 [新增 vCenter] 中，指定 vSphere 主機或 vCenter 伺服器的易記名稱，然後指定伺服器的 IP 位址或 FQDN。
3. 保留連接埠為 443，除非您的 VMware 伺服器設定為在不同連接埠上接聽要求。 選取將用於連接至 VMware vCenter 或 vSphere ESXi 伺服器的帳戶。 按一下 [確定] 。
4. Site Recovery 會使用指定的設定連接至 VMware 伺服器並探索 VM。

> [!NOTE]
> 如果您要使用在 vCenter 或主機伺服器上沒有系統管理員權限的帳戶來新增伺服器或主機，請確定該帳戶已啟用下列權限：資料中心、資料存放區、資料夾、主機、網路、資源、虛擬機器，以及 vSphere 分散式交換器。 此外，VMware vCenter 伺服器還需要啟用「儲存體檢視」權限。


## <a name="set-up-the-target-environment"></a>設定目標環境

設定目標環境之前，請確定您已備妥 Azure 儲存體帳戶和虛擬網路。

1. 按一下 [準備基礎結構] > [目標]，然後選取您要使用的 Azure 訂用帳戶。
2. 指定目標部署模型是以 Resource Manager 為基礎或傳統。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

   ![目標](./media/vmware-walkthrough-source-target/gs-target.png)
4. 如果您尚未建立儲存體帳戶或網路，請按一下 [+儲存體帳戶] 或[+網路]，以建立 Resource Manager 帳戶或網路內嵌。

## <a name="next-steps"></a>後續步驟

移至[步驟 9：設定複寫原則](vmware-walkthrough-replication.md)
