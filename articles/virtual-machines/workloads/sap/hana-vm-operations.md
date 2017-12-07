---
title: "Azure 上的 SAP Hana 作業 | Microsoft Docs"
description: "Azure 原生 VM 上的 SAP Hana 作業"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>Azure 上的 SAP Hana 作業指南
本指南提供已部署到 Azure 虛擬機器上 SAP Hana 系統的作業指引。 這份文件並非用以取代任何的標準 SAP 文件。 可以在下列位置找到 SAP 指南和附註：

- [SAP 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 安裝指南](https://service.sap.com/instguides)
- [SAP 附註](https://sservice.sap.com/notes)

必要條件是您具備不同 Azure 元件的基本知識：

- [Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 網路功能和 VNet](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure 儲存體](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

關於 SAP NetWeaver 及 Azure 上其他 SAP 元件的其他文件，可在 [Azure 文件](https://docs.microsoft.com/azure/)的 [Azure 上的 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)一節中找到。

## <a name="basic-setup-considerations"></a>基本設定考量
### <a name="connecting-into-azure"></a>連線到 Azure
如 [SAP NetWeaver 的 Azure 虛擬機器規劃和實作] [計劃指南] 中所述，有兩種基本的方法可連線到 Azure 虛擬機器。 

- 透過網際網路連線，以及透過跳接 VM 或執行 SAP Hana 的 VM 上的公用端點來進行連線
- 透過 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 或 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 進行連線

針對實際執行案例，或搭配 SAP 軟體一起饋送到實際執行案例中的非實際執行案例，您會需要透過 VPN 或 ExpressRoute 的站台對站台連線能力，如下圖所示：

![跨站台連線能力](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Azure VM 類型的選擇
能用於實際執行案例的 Azure VM 類型可在[這裡](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)查詢。 針對非實際執行案例，您可以選擇更多種類的原生 Azure VM。 不過，建議您將 VM 類型限制為 [SAP 附註 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中所列的項目。 可透過下列方式在 Azure 中部署這些 VM：

- Azure 入口網站
- Azure Powershell Cmdlet
- Azure CLI

您也可以透過[這裡](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)所述的 [SAP Cloud Platform](https://cal.sap.com/)，將完整安裝的 SAP Hana 平台部署到 Azure 虛擬機器服務上。

### <a name="choice-of-azure-storage"></a>Azure 儲存體的選擇
Azure 針對執行 SAP Hana 的 Azure VM，提供兩種主要的儲存體類型：

- [Azure 標準儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure 進階儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure 針對 Azure 標準儲存體和 Azure 進階儲存體上的 VHD，提供兩種部署方法。 在整體情況允許的情況下，建議利用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)部署。

如需確切的儲存體類型和這些儲存體類型相關的 SLA，請參閱[本文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

/hana/data 和 /hana/log 磁碟區建議使用 Azure 進階磁碟。 可支援透過多個進階儲存體磁碟組建 LVM RAID，並使用這些 RAID 磁碟區作為 /hana/data 的和 /hana/log 磁碟區。

針對不同的常見 VM 類型，目前客戶用於在 Azure VM 上裝載 SAP Hana 的設定之一，看起來可能像這樣：

| VM SKU | RAM | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8 TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Azure 網路
假設您對 Azure 有 VPN 或 ExpressRoute 的站台對站台連線能力，則您至少會有一個 [Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)，可透過虛擬閘道連線到 VPN 或 ExpressRoute 線路。 虛擬閘道存在於 Azure Vnet 的子網路中。 若要安裝 HANA，您需要在 VNet 中建立另外兩個子網路。 其中一個子網路用以裝載執行 SAP Hana 執行個體的 VM，另一個子網路則用以執行最終的 Jumpbox 或管理 VM，其中會裝載 SAP Hana Studio 或其他管理軟體。
安裝應執行 HANA 的 VM 時，該 VM 應具備以下條件：

- 已安裝兩個虛擬 NIC，其中一個連線到管理子網路，另一個 NIC 則用以從內部部署或其他網路，連線到 Azure VM 中的 SAP Hana 執行個體。
- 已針對這兩個 vNIC 部署了靜態私人 IP 位址

您可以在[這裡](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)找到不同的 IP 位址指派方式概觀。 

無論是直接路由傳送到 SAP Hana 執行個體的流量，還是路由傳送到 jumpbox 的流量，都是由與 HANA 子網路和管理子網路相關聯的 [Azure 網路安全性群組](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)所導向。

整體粗略部署結構描述看起來可能像這樣：

![SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking.PNG)


若您只是在 Azure 部署 SAP Hana 而沒有站台對站台連線能力 (透過 VPN 或 ExpressRoute 方式連線到 Azure)，則要存取 SAP Hana 執行個體會透過 Azure VM 的公用 IP 位址，而該 Azure VM 會執行 Jumpbox VM。 在簡單的情況下，您也可以倚賴 Azure 內建的 DNS 服務來解析主機名稱。 尤其是使用公開 IP 位址時，建議您使用 Azure 網路安全性群組，來限制可連線到 Azure 子網路的開放連接埠或 IP 位址範圍 (該子網路會以公開 IP 位址執行資產)。 這類部署中的結構描述看起來可能像這樣：
  
![不具站台對站台連線能力的 SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>作業
### <a name="backup-and-restore-operations-on-azure-vms"></a>Azure VM 上的備份和還原作業
SAP Hana 的備份和還原可能方式記載於下列文件中：

- [SAP Hana 備份概觀](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 檔案層級備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP Hana 儲存體快照集基準](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>啟動和重新啟動包含 SAP Hana 的 VM
Azure 公用雲端的優點之一是，您只需支付花費在計算上的分鐘數。 這表示若您關閉了執行 SAP Hana 的 VM，在這段期間內只會計算儲存成本。 而當您再次啟動了內含 SAP Hana 的 VM，再次啟動的 VM 會且具有相同的 IP 位址 (若您是以靜態 IP 位址部署)。 


### <a name="saprouter-enabling-sap-remote-support"></a>SAPRouter 可實現 SAP 遠端支援
若您在內部部署位置和 Azure 之間有站台對站台連線能力，且您已執行了 SAP 元件，您很有可能已執行了 SAProuter。 在此情況下，您無須針對部署在 Azure 中的 SAP Hana 執行個體進行任何動作。 除了維護在 SAPRouter 設定中，裝載 HANA 的 VM 私人和靜態 IP 位址，還有配適裝載 HANA VM 的子網路 NSG (允許透過連接埠 TCP/IP 連接埠 3299) 以外。

若您正在部署 SAP Hana 且透過網際網路連線到 Azure，而在執行包含 SAP Hana 的 VM 上的 Vnet 中未安裝 SAP 路由器，請在管理子網路中不同的 VM 上安裝 SAPRouter，如下所示：


![不具站台對站台連線能力和 SAPRouter 的 SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking3.PNG)

請在不同的 VM 上安裝 SAPRouter，而非安裝在 Jumpbox VM 上。 該 VM 需要靜態 IP 位址。 為了將 SAPRouter 連線到 SAP 代管的 SAPRouter (與您在 VM 上所安裝的 SAPRouter 執行個體相對)，您需要連絡 SAP 以便從 SAP 取得設定 SAPRouter 執行個體所需的 IP 位址。 唯一需要的連接埠是 TCP 連接埠 3299。
如需有關如何設定和維護透過 SAPRouter 的遠端連線詳細資訊，請參考此 [SAP 來源](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>在 Azure 原生 VM 上具有 SAP Hana 的高可用性
若執行 SUSE Linux 12 SP1 或更新版本，您就可以使用 STONITH 裝置建立 Pacemaker 叢集，以便設定使用同步複製的 SAP Hana 設定，搭配 HANA 系統複製和自動容錯移轉。 設定程序如 [Azure 虛擬機器上 SAP Hana 的高可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability)一文中所述。

 










