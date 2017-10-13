---
title: "連線雲端服務中的 Windows VM | Microsoft Docs"
description: "將利用傳統部署模型所建立的 Windows 虛擬機器連線至 Azure 雲端服務或虛擬網路。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: 0c7a21461e5bb111c4359df8e949d48382b591c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>透過虛擬網路或雲端服務來連線利用傳統部署模型所建立的 Windows 虛擬機器
> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../../../resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。

以傳統部署模型所建立的 Windows 虛擬機器一律會放置於雲端服務中。 雲端服務能做為容器，並提供唯一的公用 DNS 名稱、公用 IP 位址，以及一組透過網際網路存取虛擬機器的端點。 雲端服務可以位於虛擬網路，但這不是必要條件。 您也可以 [透過虛擬網路或雲端服務來連線 Linux 虛擬機器](../../linux/classic/connect-vms.md)。

如果雲端服務不在虛擬網路中，就稱為 *獨立* 雲端服務。 獨立雲端服務中的虛擬機器可以使用其他虛擬機器的公用 DNS 名稱與其他虛擬機器通訊，且流量會透過網際網路傳送。 如果雲端服務是在虛擬網路中，該雲端服務中的虛擬機器可與虛擬網路中的所有其他虛擬機器通訊，而不需要透過網際網路傳送任何流量。

如果您將虛擬機器放在同一個獨立雲端服務中，您仍然可以使用負載平衡和可用性設定組。 如需詳細資訊，請參閱[虛擬機器負載平衡](../../virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)和[管理虛擬機器的可用性](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 不過，您無法組織子網路上的虛擬機器，或將獨立雲端服務連線到內部部署網路。 以下是範例：

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>後續步驟
建立虛擬機器之後，最好 [新增資料磁碟](attach-disk.md) ，讓服務與工作負載有存放資料的地方。
