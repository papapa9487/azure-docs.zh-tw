---
title: "設定來源環境 (從 VMware 到 Azure) | Microsoft Docs"
description: "此文章說明如何設定您的內部部署環境，以開始將 VMware 虛擬機器複寫到 Azure。"
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
ms.openlocfilehash: 49cc01e54158b68e440f1efbfe2f8b0d076a5692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>設定來源環境 (從 VMware 到 Azure)
> [!div class="op_single_selector"]
> * [VMware 至 Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [實體至 Azure](./site-recovery-set-up-physical-to-azure.md)

此文章說明如何設定您的內部部署環境，以開始將在 VMware 上執行的虛擬機器複寫到 Azure。

## <a name="prerequisites"></a>必要條件

此文章假設您已經建立：
- [Azure 入口網站](http://portal.azure.com "Azure 入口網站")中的復原服務保存庫。
- 您 VMware vCenter 中可用來進行[自動探索](./site-recovery-vmware-to-azure.md)的專用帳戶。
- 可供安裝組態伺服器的虛擬機器。

## <a name="configuration-server-minimum-requirements"></a>組態伺服器最低需求
下表列出組態伺服器的最低硬體、軟體與網路需求。

> [!IMPORTANT]
> 部署用於保護 VMWare 虛擬機器的組態伺服器時，我們建議您將其部署為**高可用 (HA)** 虛擬機器。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> 組態伺服器不支援 HTTPS 型 Proxy 伺服器。

## <a name="choose-your-protection-goals"></a>選擇您的保護目標

1. 在 Azure 入口網站中，移至 [復原服務] 保存庫刀鋒視窗，然後選取您的保存庫。
2. 在保存庫的 [資源] 功能表上，移至 [快速入門] > [Site Recovery] > [步驟 1: 準備基礎結構] > [保護目標]。

    ![選擇目標](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. 在 [保護目標] 中選取 [至 Azure (To Azure)]，然後選擇 [是，使用 VMware vSphere Hypervisor (Yes, with VMware vSphere Hypervisor)]。 然後按一下 [確定] 。

    ![選擇目標](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>設定來源環境
設定來源環境涉及兩個主要活動：

- 安裝組態伺服器並向 Site Recovery 服務註冊。
- 將 Site Recovery 連接到內部部署 VMware vCenter 或 vSphere EXSi 主機以探索內部部署虛擬機器。

### <a name="step-1-install-and-register-a-configuration-server"></a>步驟 1：安裝及註冊組態伺服器

1. 按一下 [步驟 1：準備基礎結構]  >  [來源]。 在 [準備來源] 中，如果您沒有組態伺服器，請按一下 [+組態伺服器] 來新增一部組態伺服器。

    ![設定來源](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. 在 [新增伺服器] 刀鋒視窗中，檢查 [組態伺服器] 是否出現在 [伺服器類型] 中。
4. 下載 Site Recovery 統一安裝的安裝檔案。
5. 下載保存庫註冊金鑰。 執行「整合安裝」時，您需要該註冊金鑰。 該金鑰在產生後會維持 5 天有效。

    ![設定來源](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. 在作為組態伺服器的電腦上，執行「Azure Site Recovery 整合安裝」，以安裝組態伺服器、處理伺服器和主要目標伺服器。

#### <a name="run-azure-site-recovery-unified-setup"></a>執行 Azure Site Recovery 統一安裝

> [!TIP]
> 如果您電腦系統時鐘的時間與當地時間差五分鐘以上，組態伺服器註冊將會失敗。 開始安裝之前，請先將系統時鐘與[時間伺服器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 您可以透過命令列安裝組態伺服器。 如需詳細資訊，請參閱[使用命令列工具安裝組態伺服器](http://aka.ms/installconfigsrv)。

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>新增用於自動探索的 VMware 帳戶

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>步驟 2：新增 vCenter
若要允許 Azure Site Recovery 探索在您內部部署環境中執行的虛擬機器，您必須將 VMware vCenter Server 或 vSphere ESXi 主機與 Site Recovery 連接。

選取 [+vCenter] 來開始連接 VMware vCenter Server 或 VMware vSphere ESXi 主機。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>常見問題
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>後續步驟
在 Azure 中[設定您的目標環境](./site-recovery-prepare-target-vmware-to-azure.md)。
