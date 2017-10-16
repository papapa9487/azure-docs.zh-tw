---
title: "設定 Azure Site Recovery 的複寫設定 | Microsoft Docs"
description: "說明如何部署 Site Recovery，以協調將 VMM 雲端中的 Hyper-V VM 複寫、容錯移轉和復原至 Azure。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2017
ms.author: sutalasi
ms.openlocfilehash: 73a1f19177f23441f5f7165cf2bc92ba85e62aa5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>管理 VMware 至 Azure 的複寫原則


## <a name="create-a-replication-policy"></a>建立複寫原則

1. 選取 [管理] > [Site Recovery 基礎結構]。
2. 在 [VMware 和實體機器] 下選取 [複寫原則]。
3. 選取 [+複寫原則]。

    ![建立複寫原則](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. 輸入原則名稱。

5. 在 [RPO 臨界值] 中，指定 RPO 限制。 連續複寫超過此限制時，會產生警示。
6. 在 [復原點保留] 中，指定每個復原點的保留週期 (以小時為單位)。 受保護的機器可以復原到保留週期內的任意點。

    > [!NOTE]
    > 針對複寫到進階儲存體的機器支援最多保留 24 小時。 針對複寫到標準儲存體的機器支援最多保留 72 小時。

    > [!NOTE]
    > 系統會自動建立容錯回復的複寫原則。

7. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (以分鐘為單位)。

8. 按一下 [確定] 。 原則應該會在 30 至 60 秒內建立。

![產生複寫原則](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-a-configuration-server-with-a-replication-policy"></a>使組態伺服器與複寫原則產生關聯
1. 選擇要與組態伺服器產生關聯的複寫原則。
2. 按一下 [關聯]。
![關聯組態伺服器](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. 從伺服器清單中選取組態伺服器。
4. 按一下 [確定] 。 組態伺服器應該會在 1 至 2 分鐘內產生關聯。

![關聯組態伺服器](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-a-replication-policy"></a>編輯複寫原則
1. 選擇您要編輯複寫設定的複寫原則。
![編輯複寫原則](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. 按一下 [編輯設定] 。
![編輯複寫原則設定](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. 根據需要來變更設定。
4. 按一下 [儲存] 。 根據多少 VM 使用該複寫原則而定，應該會在 2 至 5 分鐘內儲存原則。

![儲存複寫原則](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-a-configuration-server-from-a-replication-policy"></a>使組態伺服器與複寫原則中斷關聯
1. 選擇要與組態伺服器產生關聯的複寫原則。
2. 按一下 [中斷關聯]。
3. 從伺服器清單中選取組態伺服器。
4. 按一下 [確定] 。 組態伺服器應該會在 1 至 2 分鐘內中斷關聯。

    > [!NOTE]
    > 如果至少有一個複寫項目使用該原則，您就無法與組態伺服器中斷關聯。 與組態伺服器中斷關聯之前，請確定沒有複寫項目使用該原則。

## <a name="delete-a-replication-policy"></a>刪除複寫原則

1. 按一下要刪除的複寫原則。
2. 按一下 [刪除] 。 原則應該會在 30 至 60 秒內刪除。

    > [!NOTE]
    > 如果複寫原則至少有一個相關聯的組態伺服器，您就無法刪除該複寫原則。 請確定沒有複寫項目使用該原則，然後先刪除所有相關聯的組態伺服器，再刪除原則。
