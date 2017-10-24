---
title: "使用 Azure 入口網站建立虛擬機器擴展集 | Microsoft Docs"
description: "使用 Azure 入口網站部署擴展集。"
keywords: "虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc52368a1a14ad094e7ab9180b90a9aa4ccdb6d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>如何使用 Azure 入口網站建立虛擬機器擴展集
本教學課程示範使用 Azure 入口網站建虛擬機器擴展集有多麼容易，只需數分鐘。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="choose-the-vm-image-from-the-marketplace"></a>從 Marketplace 選擇 VM 映像
您可以使用 CentOS、CoreOS、Debian、Ubuntu Server、其他 Linux 映像及 Windows Server 映像，輕鬆地從入口網站部署擴展集。

首先，在網頁瀏覽器中導覽至 [Azure 入口網站](https://portal.azure.com) 。 按一下 [新增]，搜尋**擴展集**，然後選取 [虛擬機器擴展集] 項目：

![Azure 虛擬機器擴展集入口網站搜尋](./media/virtual-machine-scale-sets-portal-create/portal-search.png)

## <a name="create-the-scale-set"></a>建立擴展集
現在您可以使用預設設定並快速建立擴展集。

* 輸入擴展集的名稱。  
此名稱會成為擴展集前方負載平衡器 FQDN 的基底，因此請確保該名稱在整個 Azure 中是唯一的。

* 選取想要的 OS 類型。

* 輸入所需的使用者名稱，然後選取偏好的驗證類型。  
如果您選擇密碼，它的長度必須至少為 12 個字元，且符合下列四個複雜性需求的其中三項：1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元。 進一步了解 [使用者名稱和密碼需求](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)。 如果您選擇 **SSH 公開金鑰**，請務必只貼上公開金鑰，而不要貼上您的私密金鑰：

* 針對 [允許調整為超過 100 個執行個體] 選取 [是] 或 [否]。  
如果 [是]，擴展集可以跨越橫跨多個放置群組。 如需詳細資訊，請參閱[這份文件](./virtual-machine-scale-sets-placement-groups.md)。

* 請務必選取適當的**執行個體大小**。  
如需虛擬機器大小的詳細資訊，請參閱 [Windows VM 大小](..\virtual-machines\windows\sizes.md)或 [Linux VM 大小](..\virtual-machines\linux\sizes.md)。

* 輸入所需的資源群組名稱及位置。  
如果您的地區與**執行個體大小**支援可用性區域，就會啟用 [可用性區域] 欄位。 如需可用性區域的詳細資訊，請參閱此[概觀](../availability-zones/az-overview.md)一文。

* 輸入所需的網域名稱標籤 (擴展集前方負載平衡器 FQDN 的基底)。  
此標籤在整個 Azure 中必須是唯一的。

* 選擇所需的作業系統磁碟映像、執行個體計數，以及機器大小。

* 選擇您想要的磁碟類型：受管理或未受管理。  
如需詳細資訊，請參閱[這份文件](./virtual-machine-scale-sets-managed-disks.md)。 如果您選擇讓擴展集跨多個放置群組，將無法使用此選項，因為必須使用受管理磁碟，擴展集才能跨放置群組。

* 啟用或停用自動調整，並於啟用的情況下進行設定。

![Azure 虛擬機器擴展集入口網站建立提示](./media/virtual-machine-scale-sets-portal-create/portal-create.png)

## <a name="connect-to-a-vm-in-the-scale-set"></a>連線到擴展集中的 VM
如果您選擇將擴展集限制在單一放置群組，則在部署擴展集時會設定好可讓您輕鬆連接到擴展集的 NAT 規則 (否則，若要連接到擴展集中的虛擬機器，您可能需要在與該擴展集相同的虛擬網路中建立跳轉盒 (Jumpbox))。 若要查看它們，請瀏覽至該擴展集之負載平衡器的 [`Inbound NAT Rules`] 索引標籤：

![Azure 虛擬機器擴展集入口網站 NAT 規則](./media/virtual-machine-scale-sets-portal-create/portal-nat-rules.png)

您可以使用下列 NAT 規則來連線到擴展集中的每個 VM。 例如，針對 Windows 擴展集而言，假設連入連接埠 50000 上具有一個 NAT 規則，您便可以透過 `<load-balancer-ip-address>:50000`上的 RDP 連線到該機器。 針對 Linux 擴展集，您必須使用命令 `ssh -p 50000 <username>@<load-balancer-ip-address>`來進行連線。

## <a name="next-steps"></a>後續步驟
如需如何從 CLI 部署擴展集的相關文件，請參閱 [此文件](virtual-machine-scale-sets-cli-quick-create.md)。

如需如何從 PowerShell 部署擴展集的相關文件，請參閱[此文件](virtual-machine-scale-sets-windows-create.md)。

如需如何從 Visual Studio 部署擴展集的相關文件，請參閱 [此文件](virtual-machine-scale-sets-vs-create.md)。

如需一般文件，請參閱 [擴展集的文件概觀頁面](virtual-machine-scale-sets-overview.md)。

如需一般資訊，請參閱 [擴展集的主要登陸頁面](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

