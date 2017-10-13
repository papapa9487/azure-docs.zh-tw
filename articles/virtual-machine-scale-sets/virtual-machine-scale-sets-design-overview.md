---
title: "Azure 虛擬機器擴展集的設計考量 | Microsoft Docs"
description: "深入了解 Azure 虛擬機器擴展集的設計考量"
keywords: "linux 虛擬機器, 虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 0b05359938f4da544c4cb2a6fe60cfaf228478e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="design-considerations-for-scale-sets"></a>擴展集的設計考量
本主題會討論虛擬機器擴展集的設計考量。 如需虛擬機器擴展集的相關資訊，請參閱 [虛擬機器擴展集概觀](virtual-machine-scale-sets-overview.md)。

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>何時應使用擴展集而非虛擬機器？
一般而言，擴展集適用於部署高可用性的基礎結構，其中會有一組電腦具備類似的組態。 不過，部分功能僅適用於擴展集，而其他功能僅適用於 VM。 為了在使用每種技術時做出明智的決策，我們應該先看一下部分只能在擴展集而非 VM 中使用的常用功能：

### <a name="scale-set-specific-features"></a>擴展集特定的功能

- 一旦指定擴展集組態之後，您只需更新「容量」屬性，就能以平行方式部署更多 VM。 比起撰寫指令碼，以平行方式協調部署許多個別 VM 來說，這個方法簡單許多。
- 您可以[使用 Azure 自動調整規模自動調整擴展集](./virtual-machine-scale-sets-autoscale-overview.md)，但無法針對個別 VM 執行。
- 您可以[重新安裝擴展集 VM 的映像](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-a-vm)，但[無法針對個別 VM](https://docs.microsoft.com/rest/api/compute/virtualmachines) 執行。
- 您可以[過度佈建](./virtual-machine-scale-sets-design-overview.md)擴展集 VM，以提高可靠性並加快部署速度。 除非您撰寫自訂程式碼來執行這項操作，否則無法使用個別 VM 來執行此操作。
- 您可以指定[升級原則](./virtual-machine-scale-sets-upgrade-scale-set.md)，以便輕鬆地在擴展集的 VM 之間進行升級。 使用個別的 VM，您必須自行協調更新。

### <a name="vm-specific-features"></a>VM 特定的功能

相反地，部分功能只能在 VM 中使用 (至少暫時是這樣)：

- 您可以將資料磁碟連接至特定的個別 VM，但連接的資料磁碟會針對擴展集中的所有 VM 加以設定。
- 您可以將非空白的資料磁碟連接至個別 VM，但無法針對擴展集中的 VM 執行。
- 您可以擷取個別 VM 的快照集，但無法針對擴展集中的 VM 執行。
- 您可以擷取個別 VM 的映像，但無法擴展集中的 VM 執行。
- 您可以將個別 VM 從原生磁碟移轉至受控磁碟，但無法針對擴展集中的 VM 執行此操作。
- 您可以將 IPv6 公用 IP 位址指派給個別 VM NIC，但無法針對擴展集中的 VM 執行此操作。 請注意，您可以將 IPv6 公用 IP 位址指派給個別 VM或擴展集 VM 之前的負載平衡器。

## <a name="storage"></a>儲存體

### <a name="scale-sets-with-azure-managed-disks"></a>包含 Azure 受控磁碟的擴展集
擴展集可以使用 [Azure 受控磁碟](../virtual-machines/windows/managed-disks-overview.md)來建立，而不是傳統的 Azure 儲存體帳戶。 受控磁碟可提供下列優點：
- 您不必為擴展集 VM 預先建立一組 Azure 儲存體帳戶。
- 您可以為擴展集中的 VM 定義[附加的資料磁碟](virtual-machine-scale-sets-attached-disks.md)。
- 可以設定擴展集以[支援集合中多達 1,000 個 VM](virtual-machine-scale-sets-placement-groups.md)。 

如果您目前具有範本，也可以[更新範本來使用受控磁碟](virtual-machine-scale-sets-convert-template-to-md.md)。

### <a name="user-managed-storage"></a>使用者管理的儲存體
未使用 Azure 受控磁碟定義的擴展集依賴使用者建立的儲存體帳戶，來儲存擴展集中 VM 的 OS 磁碟。 建議每個儲存體帳戶 20 部 VM 或更小的比率，以期達到最大 IO，而且也可利用_過度佈建_ (如下所示)。 此外，也建議您分散儲存體帳戶名稱的開頭字元英文字母。 這樣做有助於將負載分散到不同的內部系統。 


## <a name="overprovisioning"></a>過度佈建
擴展集目前預設為「過度佈建」VM。 藉由開啟過度佈建，擴展集實際上所啟動的 VM 數目會比您要求的還多，然後在成功佈建要求的 VM 數目後刪除額外的 VM。 過度佈建可改善佈建成功率並縮短部署時間。 這些額外的 VM 不會計費，也不會計入配額限制。

雖然過度佈建的確改善了佈建的成功率，但也可能導致不是設計來處理額外 VM 出現而後消失的應用程式出現令人困惑的行為。 若要將過度佈建關閉，請確定範本中具有下列字串：`"overprovision": "false"`。 您可以在[擴展集 REST API 文件](/rest/api/virtualmachinescalesets/create-or-update-a-set)中找到更多詳細資料。

如果擴展集使用使用者管理的儲存體，而且您關閉過度佈建，則每個儲存體帳戶可以有 20 部以上的 VM，但基於 IO 效能考量，不建議超過 40 部。 

## <a name="limits"></a>限制
以 Marketplace 映像 (也稱為平台映像) 為建置基礎並設定為使用 Azure 受控磁碟的擴展集可支援多達 1,000 部 VM。 如果您將擴展集設定為支援 100 部以上的 VM，並非所有案例的作用都相同 (適用於負載平衡)。 如需詳細資訊，請參閱[使用大型的虛擬機器擴展集](virtual-machine-scale-sets-placement-groups.md)。 

以使用者管理的儲存體設定的擴展集目前受限於 100 部 VM (且建議此擴展集有 5 個儲存體帳戶)。

若以 Azure 受控磁碟進行設定，以自訂映像 (由您建立的映像) 為建置基礎的擴展集可以有多達 300 部 VM。 如果以使用者管理的儲存體帳戶設定擴展集，它必須在一個儲存體帳戶內建立所有的 OS 磁碟 VHD。 因此，在以自訂映像和使用者管理的儲存體為建置基礎的擴展集中建議 VM 數上限為 20。 如果關閉過度佈建，數目上限為 40。

如果超出這些限制允許的 VM，您必須部署多個擴展集，如 [這個範本](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)中所示。

