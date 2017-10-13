---
title: "針對使用 Azure Site Recovery 將 Hyper-V 複寫至次要網站的複寫作業設定來源與目標 | Microsoft Docs"
description: "說明在使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要 VMM 站台時，如何設定來源與目標。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: fa7809f1-7633-425f-b25d-d10d004e8d0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 07135e9b5e619971a59cc22ec68a0a4e8bcaabe1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-set-up-the-replication-source-and-target"></a>步驟 6：設定複寫來源與目標


針對使用 [Azure Site Recovery](site-recovery-overview.md) 將 Hyper-V 複寫至次要 VMM 站台的複寫作業，建立好復原服務保存庫後，請使用本文設定來源與目標的複寫位置。 

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。




## <a name="set-up-the-source-environment"></a>設定來源環境

在 VMM 伺服器上安裝 Azure Site Recovery Provider，並在保存庫中探索和註冊伺服器。

1. 按一下 [步驟 1：準備基礎結構]  >  [來源]。

    ![設定來源](./media/vmm-to-vmm-walkthrough-source-target/goals-source.png)
2. 在 [準備來源] 中，按一下 [+ VMM] 以新增 VMM 伺服器。

    ![設定來源](./media/vmm-to-vmm-walkthrough-source-target/set-source1.png)
3. 在 [新增伺服器] 中，檢查 [System Center VMM 伺服器] 是否出現在 [伺服器類型] 中，以及 VMM 伺服器是否符合[必要條件](#prerequisites)。
4. 下載 Azure Site Recovery Provider 安裝檔案。
5. 下載註冊金鑰。 您會在執行安裝程式時用到此金鑰。 該金鑰在產生後會維持 5 天有效。

    ![設定來源](./media/vmm-to-vmm-walkthrough-source-target/set-source3.png)
6. 您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。 您不需要明確地在 Hyper-V 主機伺服器上安裝任何項目。


## <a name="install-the-azure-site-recovery-provider"></a>安裝 Azure Site Recovery 提供者

1. 在每部 VMM 伺服器上執行 Provider 安裝檔案。 如果 VMM 部署在叢集中，請在第一次安裝時執行下列作業︰
    -  將提供者安裝在作用中的節點上，並完成安裝以在保存庫中註冊該 VMM 伺服器。
    - 然後在其他節點上安裝 Provider。 叢集節點全都應該執行相同版本的 Provider。
2. 安裝程式會執行幾項必要條件檢查，並要求停止 VMM 服務的權限。 當安裝完成之後，VMM 服務將會自動重新啟動。 如果您安裝在 VMM 叢集上，您會收到停止叢集角色的提示。
3. 在 [Microsoft Update] 中，可以選擇根據您的 Microsoft Update 原則來安裝提供者更新。
4. 在 [安裝] 中，接受或修改預設安裝位置，然後按一下 [安裝]。

    ![安裝位置](./media/vmm-to-vmm-walkthrough-source-target/provider-location.png)
5. 安裝完成之後，按一下 [註冊] 以在保存庫中註冊伺服器。

    ![安裝位置](./media/vmm-to-vmm-walkthrough-source-target/provider-register.png)
6. 在 [保存庫名稱] 中，確認要註冊伺服器的保存庫名稱。 按 [下一步] 。

    ![伺服器註冊](./media/vmm-to-vmm-walkthrough-source-target/vaultcred.png)
7. 在 [網際網路連線] 中，指定 VMM 伺服器上執行的提供者連接至 Azure 的方式。

    ![網際網路設定](./media/vmm-to-vmm-walkthrough-source-target/proxydetails.png)

   - 您可以指定提供者應該直接或透過 proxy 連接至網際網路。
   - 指定 proxy 設定 (如有需要)。
   - 如果您使用 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 proxy 伺服器，讓此帳戶可以成功進行驗證。 您可以在 VMM 控主台 > [設定] > [安全性] > [執行身分帳戶]中修改 RunAs 帳戶設定。 重新啟動 VMM 服務以更新變更。
8. 在 [註冊金鑰] 中，選取您從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
9. 只有在您正在將 VMM 雲端中的 Hyper-V VM 複寫至 Azure 時，才會使用這項加密設定。 如果您是在複寫至次要站台，則不會使用它。
10. 在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。 在叢集設定中，指定 VMM 叢集角色名稱。
11. 在 [同步處理雲端中繼資料] 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。 這個動作只需要在每個伺服器上進行一次。 如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。
12. 按 [下一步]  ，完成此程序。 註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。 此伺服器會顯示於保存庫中 [伺服器] 頁面的 [VMM 伺服器] 索引標籤上。

    ![伺服器](./media/vmm-to-vmm-walkthrough-source-target/provider13.png)
13. 當伺服器可以在 Site Recovery 主控台中使用之後，於 [來源]  >  中選取 VMM 伺服器，然後選取 Hyper-V 主機所在的雲端。 然後按一下 [確定] 。

您也可以從命令列安裝提供者：

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>設定目標環境

選取目標 VMM 伺服器和雲端：

1. 按一下 [準備基礎結構] > [目標]，然後選取您想要使用的目標 VMM 伺服器。
2. 隨即顯示伺服器上與 Site Recovery 同步處理的雲端。 選取目標雲端。

   ![目標](./media/vmm-to-vmm-walkthrough-source-target/target-vmm.png)



## <a name="next-steps"></a>後續步驟

移至[步驟 7：設定網路對應](vmm-to-vmm-walkthrough-network-mapping.md)。
