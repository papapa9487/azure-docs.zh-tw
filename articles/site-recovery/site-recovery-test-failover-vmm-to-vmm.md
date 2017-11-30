---
title: "在 Azure Site Recovery 中測試容錯移轉 (VMM 到 VMM) | Microsoft Docs"
description: "Azure Site Recovery 可協調虛擬機器和實體伺服器的複寫、容錯移轉及復原作業。 了解如何容錯移轉到 Azure 或次要資料中心。"
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: ponatara
ms.openlocfilehash: 2730cebc1cdc47db283ae851560d93fdbf50ee48
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>在 Site Recovery 中測試容錯移轉 (VMM 到 VMM)


針對以 Azure Site Recovery 保護的虛擬機器 (VM) 與實體伺服器所執行的測試容錯移轉或災害復原 (DR) 訓練，本文提供相關資訊和指示。 您將使用由 System Center Virtual Machine Manager (VMM) 管理的內部部署網站作為復原網站。

您執行測試容錯移轉來驗證您的複寫策略，或在沒有資料遺失或停機時間的情況下執行災害復原訓練。 測試容錯移轉不會影響任何正在進行的複寫或生產環境。 您可以在虛擬機器上執行測試容錯移轉，或依[復原方案](site-recovery-create-recovery-plans.md)執行。 當您觸發測試容錯移轉時，必須指定測試虛擬機器將要連線的網路。 您可以在 [作業] 頁面中追蹤測試容錯移轉的進度。  

如果您有任何意見或問題，請張貼在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="prepare-the-infrastructure-for-test-failover"></a>準備測試容錯移轉的基礎結構
如果您想要使用現有網路來執行測試容錯移轉，請在該網路中準備好 Active Directory、DHCP 和 DNS。

如果您想要使用自動建立 VM 網路的選項來執行測試容錯移轉，請在您將用於測試容錯移轉之復原方案中的群組 1 前新增手動步驟。 然後，將基礎結構資源新增到自動建立的網路，接著再執行測試容錯移轉。

### <a name="things-to-note"></a>注意事項
複寫到次要網站時，複本機器所使用的網路類型不需要與用來測試容錯移轉的邏輯網路類型相符，但某些組合可能無法運作。 如果複本會使用 DHCP 和 VLAN 架構的隔離，則複本的 VM 網路不需要靜態 IP 位址集區。 因此，使用 Windows 網路虛擬化來測試容錯移轉將不會運作，因為沒有任何位址集區可供使用。 

此外，如果使用「未隔離」複本網路和「Windows 網路虛擬化」測試網路，則測試容錯移轉將無法運作。 這是因為「未隔離」的網路沒有建立 Windows 網路虛擬化網路所需的子網路。

容錯移轉之後，用來將複本虛擬機器連線至對應 VM 網路的方式，取決於 VM 網路在 VMM 主控台中的設定方式。

#### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>設定為未隔離或 VLAN 隔離的 VM 網路
如果 VM 網路已定義 DHCP，則複本虛擬機器將透過針對相關聯邏輯網路中網路站台所指定的設定連線到 VLAN 識別碼。 虛擬機器從可用的 DHCP 伺服器接收其 IP 位址。 

您不需要為目標 VM 網路定義靜態 IP 位址集區。 如果 VM 網路使用靜態 IP位址集區，則複本虛擬機器將透過針對相關聯邏輯網路中網路站台所指定的設定連線到 VLAN 識別碼。

虛擬機器從 VM 網路所定義的集區接收其 IP 位址。 如果靜態 IP 位址集區未定義於目標 VM 網路上，則 IP 位址配置將會失敗。 在您即將用來保護與復原的來源和目標 VMM 伺服器上，建立 IP 位址集區。

#### <a name="vm-network-with-windows-network-virtualization"></a>採用 Windows 網路虛擬化的 VM 網路
如果 VM 網路設定為 Windows 網路虛擬化，您應該為目標 VM 網路定義靜態集區，而不論來源 VM 網路是設定為使用 DHCP 或靜態 IP 位址集區。 

如果您定義 DHCP，目標 VMM 伺服器將作為 DHCP 伺服器，並提供為目標 VM 網路定義之集區中的 IP 位址。 如果已定義來源伺服器使用靜態 IP 位址集區，則目標 VMM 伺服器會配置集區中的 IP 位址。 在這兩種情況下，如果未定義靜態 IP 位址集區，IP 位址配置將會失敗。


### <a name="prepare-dhcp"></a>準備 DHCP
如果參與測試容錯移轉的虛擬機器使用 DHCP，請在基於測試容錯移轉目的而建立的隔離網路中建立測試 DHCP 伺服器。

### <a name="prepare-active-directory"></a>準備 Active Directory
若要執行測試容錯移轉以進行應用程式測試，您需要在測試環境中具有 Active Directory 生產環境的複本。 如需詳細資訊，請參閱 [Active Directory 的測試容錯移轉考量](site-recovery-active-directory.md#test-failover-considerations)。

### <a name="prepare-dns"></a>準備 DNS
準備 DNS 伺服器來進行測試容錯移轉，如下所示：

* **DHCP**：如果虛擬機器使用 DHCP，就應該在測試 DHCP 伺服器上更新測試 DNS 的 IP 位址。 如果使用的是 Windows 網路虛擬化的網路類型，VMM 伺服器就會當做 DHCP 伺服器使用。 因此，應該在測試容錯移轉中更新 DNS 的 IP 位址。 在此情況下，虛擬機器會向相關的 DNS 伺服器註冊其本身。
* **靜態位址**：如果虛擬機器使用靜態 IP 位址，就應該在測試容錯移轉中更新測試 DNS 伺服器的 IP 位址。 您可能需要以測試虛擬機器的 IP 位址來更新 DNS。 您可以基於此目的來使用下列指令碼範例：

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>執行測試容錯移轉
此程序說明如何針對復原方案執行測試容錯移轉。 或者，您可以在 [虛擬機器] 索引標籤上針對單一虛擬機器執行容錯移轉。

![測試容錯移轉刀鋒視窗](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. 選取 [復原方案]  >  *recoveryplan_name*。 按一下 [容錯移轉] **容錯移轉** > **Test 容錯移轉**中張貼意見或問題。
1. 在 [測試容錯移轉] 刀鋒視窗上，指定虛擬機器在測試容錯移轉後應如何連接到網路。 如需詳細資訊，請參閱[網路選項](#network-options-in-site-recovery)。
1. 在 [工作]  索引標籤上追蹤容錯移轉進度。
1. 完成容錯移轉之後，請確認虛擬機器已成功啟動。
1. 完成後，按一下復原方案上的 [清除測試容錯移轉]。 在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。 這個步驟會刪除在測試容錯移轉期間所建立的虛擬機器和網路。


## <a name="network-options-in-site-recovery"></a>Site Recovery 中的網路選項

當您執行測試容錯移轉時，系統會要求您選取用以測試複本機器的網路設定。 您可以很多選擇。  

| **測試容錯移轉選項** | **說明** | **容錯移轉檢查** | **詳細資料** |
| --- | --- | --- | --- |
| **容錯移轉到次要 VMM 網站 -- 無網路** |請勿選取 VM 網路。 |容錯移轉會檢查測試機器是否已建立。<br/><br/>測試虛擬機器會建立在與複本虛擬機器相同的主機上。 它不會新增至複本虛擬機器所在的雲端。 |<p>容錯移轉的機器不會連線至任何網路。<br/><br/>此機器建立後可以連線至 VM 網路。 |
| **容錯移轉到次要 VMM 網站 -- 有網路** |選取現有的 VM 網路。 |容錯移轉會檢查虛擬機器是否已建立。 |測試虛擬機器會建立在與複本虛擬機器相同的主機上。 它不會新增至複本虛擬機器所在的雲端。<br/><br/>建立與您的生產網路隔離的 VM 網路。<br/><br/>如果您是使用 VLAN 網路，建議您在 VMM 中另外建立未用於生產網路的測試專用邏輯網路。 此邏輯網路是用來建立測試容錯移轉的 VM 網路。<br/><br/>邏輯網路應該至少要與所有裝載虛擬機器之 Hyper-V 伺服器的其中一個網路介面卡相關聯。<br/><br/>若使用 VLAN 邏輯網路，應該要隔離您新增至邏輯網路的網路站台。<br/><br/>如果您是使用 Windows 網路虛擬化型邏輯網路，Azure Site Recovery 會自動建立隔離的 VM 網路。 |
| **容錯移轉到次要 VMM 站台 -- 建立網路** |暫時性測試網路將根據您在 [邏輯網路] 及其相關網路站台中指定的設定自動建立。 |容錯移轉會檢查虛擬機器是否已建立。 |如果復原計畫使用多個 VM 網路，請使用這個選項。 如果您使用的是 Windows 網路虛擬化網路，這個選項會使用複本虛擬機器網路中的相同設定 (子網路和 IP 位址集區) 自動建立 VM 網路。 在測試容錯移轉完成後，這些 VM 網路將自動清除。</p><p>測試虛擬機器會建立在與複本虛擬機器相同的主機上。 它不會新增至複本虛擬機器所在的雲端。 |

> [!TIP]
> 測試容錯移轉期間提供給虛擬機器的 IP 位址，與計劃性或非計劃性容錯移轉時虛擬機器收到的 IP 位址相同 (假設該 IP 位址可用於測試容錯移轉網路)。 如果在測試容錯移轉網路中無法使用相同的 IP 位址，虛擬機器會收到另一個可用於測試容錯移轉網路的 IP 位址。
>
>


## <a name="test-failover-to-a-production-network-on-a-recovery-site"></a>在復原網路上測試容錯移轉到生產網路
當您進行測試容錯移轉時，建議您選擇與在 [網路對應](https://docs.microsoft.com/azure/site-recovery/site-recovery-network-mapping) 中提供的生產復原網站網路不同的網路。 但是，如果您真的想驗證容錯移轉的虛擬機器中的端對端網路連線，請注意下列幾點︰

* 進行測試容錯移轉時，請確定主要虛擬機器已關機。 如果不這麼做，相同的網路中將同時有兩部虛擬機器以相同的身分識別執行。 這可能造成不良的後果。
* 當您清除測試容錯移轉虛擬機器時，您對測試容錯移轉虛擬機器所做的任何變更將會遺失。 這些變更不會複寫回主要虛擬機器。
* 這種測試方式會導致生產應用程式的停機時間。 進行 DR 訓練時，要求應用程式的使用者不要使用應用程式。  


## <a name="next-steps"></a>後續步驟
成功執行測試容錯移轉之後，您就可以嘗試執行[容錯移轉](site-recovery-failover.md)。
