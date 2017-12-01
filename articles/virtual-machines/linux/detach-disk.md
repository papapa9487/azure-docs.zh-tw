---
title: "從 Linux VM 中斷資料磁碟連結 | Microsoft Docs"
description: "了解如何使用 CLI 2.0 或 Azure 入口網站，從 Azure 中的虛擬機器中斷資料磁碟連結。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: c589dd8c9d597145fd87a00d9a2ba040988cd8ec
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2017
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>如何從 Linux 虛擬機器中斷資料磁碟連結

當不再需要某個連接至虛擬機器的資料磁碟時，卸離此資料磁碟很簡單。 這會將磁碟從虛擬機器中卸離，但這不會將它從儲存體中移除。 

> [!WARNING]
> 將磁碟中斷連結時，並不會自動將它刪除。 如果您已訂閱「進階」儲存體，您將會繼續因該磁碟而導致產生儲存體費用。 如需詳細資訊，請參閱 [使用進階儲存體時的價格和計費](../windows/premium-storage.md#pricing-and-billing)。 
> 
> 

如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連接至相同或其他虛擬機器。  

## <a name="detach-a-data-disk-using-cli-20"></a>使用 CLI 2.0 中斷資料磁碟連結

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

磁碟仍留在儲存體中，但不再連接至虛擬機器。


## <a name="detach-a-data-disk-using-the-portal"></a>使用入口網站來中斷資料磁碟連結
1. 在左窗格中，選取 [虛擬機器]。
2. 選取含有您想要中斷連結之資料磁碟的虛擬機器，然後按一下 [停止] 以解除配置該 VM。
3. 在 [虛擬機器] 窗格中，選取 [磁碟]。
4. 在 [磁碟] 窗格頂端，選取 [編輯]。
5. 在 [磁碟] 窗格中，在您想要中斷連結的資料磁碟最右側，按一下 ![[中斷連結] 按鈕影像](./media/detach-disk/detach.png) [中斷連結] 按鈕。
5. 移除磁碟之後，按一下窗格頂端的 [儲存]。
6. 在 [虛擬機器] 窗格中，按一下 [概觀]，然後按一下窗格頂端的 [啟動] 按鈕以重新啟動 VM。

磁碟仍留在儲存體中，但不再連接至虛擬機器。


## <a name="next-steps"></a>後續步驟
如果您想要重複使用該資料磁碟，只要[將它連結至另一個 VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

