---
title: "設定使用 Azure Site Recovery 將 Hyper-V 複寫至次要站台的複寫原則 | Microsoft Docs"
description: "說明如何設定使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要 VMM 站台的原則。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5d9b79cf-89f2-4af9-ac8e-3a32ad8c6c4d
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: a3ecc555e049914d7bffdddce7b0522f09362d24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-a-replication-policy"></a>步驟 8：設定複寫原則

設定[網路對應](vmm-to-vmm-walkthrough-network-mapping.md)後，請使用本文設定複寫原則，以使用 [Azure Site Recovery](site-recovery-overview.md) 將 Hyper-V 虛擬機器 (VM) 複寫至次要站台。

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。


## <a name="before-you-start"></a>開始之前

- 當您建立複寫原則時，所有使用該原則的主機都必須有相同的作業系統。 VMM 雲端中可以包含執行不同 Windows Server 版本的 Hyper-V 主機，但在此情況下，您需要多個複寫原則。
- 您可以離線執行初始複寫。

## <a name="configure-replication-settings"></a>設定複寫設定

1. 若要建立新的複寫原則，請按一下 [準備基礎結構] > [複寫設定] > [+建立及關聯]。

    ![網路](./media/vmm-to-vmm-walkthrough-replication/gs-replication.png)
2. 在 [建立及關聯原則] 中指定原則名稱。 來源和目標類型應該是 **Hyper-V**。
3. 在 [Hyper-V 主機版本] 中，選取在主機上執行的作業系統。
4. 在 [驗證類型] 和 [驗證連接埠] 中，指定主要與復原 Hyper-V 主機伺服器之間流量的驗證方式。 除非您有有效的 Kerberos 環境，否則請選取 [憑證]。 Azure Site Recovery 會自動設定用於 HTTPS 驗證的憑證。 您不需要手動執行任何動作。 根據預設，連接埠 8083 和 8084 (用於憑證) 將在 Hyper-V 主機伺服器上的 Windows 防火牆中開啟。 如果您確實選取 [Kerberos]，Kerberos 票證將會用於主機伺服器的相互驗證。 請注意，這項設定只有在 Hyper-V 主機伺服器是執行於 Windows Server 2012 R2 時才有重要性。
5. 在 [複製頻率] 中，指定您要在初始複寫後複寫差異資料的頻率 (每隔 30 秒、5 或 15 分鐘)。
6. 在 [復原點保留] 中，針對每個復原點指定保留週期的長度 (以小時為單位)。 受保護的機器可以復原到週期內的任意點。
7. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (1-12 小時)。 Hyper-V 使用兩種類型的快照，一個是標準快照，提供整個虛擬機器的增量快照，另一個是應用程式一致快照，會建立虛擬機器內應用程式資料的時間點快照。 應用程式一致快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。 如果您啟用應用程式一致快照，它會影響在來源虛擬機器上執行的應用程式效能。 確認您設定的值低於您設定的其他復原點數目。
8. 在 [資料傳輸壓縮] 中，指定是否應該將傳輸的已複寫資料壓縮。
9. 選取 [刪除複本 VM]，以指定如果您停用對來源 VM 的保護，則應刪除複本虛擬機器。 如果啟用此設定，當您停用對來源 VM 的保護時，便會從 Site Recovery 主控台中加以移除、在 VMM 主控台中移除 VMM 的 Site Recovery 設定，並刪除複本。
10. 在 [初始複寫方法] 中，如果您要透過網路進行複寫，請指定是否要啟動初始複寫，或將它排程。 若要節省網路頻寬，您可能要將它排程在離峰時間執行。 然後按一下 [確定] 。

     ![複寫原則](./media/vmm-to-vmm-walkthrough-replication/gs-replication2.png)
11. 當您建立新的原則時，該原則會自動與 VMM 雲端產生關聯。 在 [複寫原則] 中，按一下 [確定]。 您可以在 [複寫] > 原則名稱 > [關聯 VMM 雲端] 中，將其他 VMM 雲端 (及其中的 VM) 與此複寫原則產生關聯。

     ![複寫原則](./media/vmm-to-vmm-walkthrough-replication/policy-associate.png)



## <a name="prepare-for-offline-initial-replication"></a>準備進行離線初始複寫

您可以針對初始資料複本進行離線複寫。 您可以如下所示方式進行準備︰

* 在來源伺服器上，您會指定要從中匯出資料的路徑位置。 在匯出路徑上指派 NTFS 和共用權限的完整控制權給 VMM 服務。 在目標伺服器上，您會指定要從中匯入資料的路徑位置。 在這個匯入路徑上指派相同的權限。
* 如果共用匯入或匯出路徑，請在共用所在的遠端桌面上，指派系統管理員、進階使用者、列印操作員或伺服器操作員群組成員資格給 VMM 伺服器帳戶。
* 如果您使用任何執行身分帳戶加入主機，請在匯入和匯出路徑上指派讀取和寫入權限給 VMM 中的執行身分帳戶。
* 匯入和匯出共用不得位於任何做為 Hyper-V 主機伺服器的電腦，因為 Hyper-V 不支援回送設定。
* 在 Active Directory 的每部包含您想要保護之虛擬機器的 Hyper-V 主機伺服器上，啟用與設定限制委派，以信任匯入與匯出路徑所在的遠端電腦，如下所示：
  1. 在網域控制站上，開啟 [Active Directory 使用者和電腦] 。
  2. 在主控台樹狀目錄中，按一下 [DomainName] > [電腦]。
  3. 在 Hyper-V 主機伺服器名稱上按一下滑鼠右鍵 > [屬性]。
  4. 在 [委派] 索引標籤上，按一下 [信任這台電腦，但只委派指定的服務]。
  5. 按一下 [使用任何驗證通訊協定] 。
  6. 按一下 [新增]  > 提出技術問題。
  7. 輸入裝載匯出路徑之電腦的名稱 > [確定]。 從可用服務清單，按住 CTRL 鍵，然後按一下 [cifs] > [確定]。 為裝載匯入路徑的電腦名稱重複動作。 視需要為其他 Hyper-V 主機伺服器重複動作。



## <a name="next-steps"></a>後續步驟

移至[步驟 9：啟用複寫](vmm-to-vmm-walkthrough-enable-replication.md)。
