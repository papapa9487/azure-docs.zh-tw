---
title: "搭配使用受控磁碟與 Azure 虛擬機器擴展集 | Microsoft Docs"
description: "了解為什麼以及如何搭配使用受控磁碟與虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.openlocfilehash: 82fa518e6c0498a13f950ce33c51be8581918f9b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Azure VM 擴展集與受控磁碟

Azure [虛擬機器擴展集](/azure/virtual-machine-scale-sets/)支援具有受控磁碟的虛擬機器。 搭配使用受控磁碟與擴展集有數個優點，包括：

* 您不再需要預先建立和管理儲存體帳戶，來儲存作業系統磁碟以供擴展集 VM 使用。

* 您可以將受控的資料磁碟連接到擴展集。

* 利用受控磁碟，擴展集所擁有的容量可以高達 1,000 個 VM (如果以平台映像為基礎) 或 300 個 VM (如果以自訂映像為基礎)。

## <a name="get-started"></a>開始使用

開始使用受控磁碟擴展集的簡單方式是從 Azure 入口網站部署一個。 如需詳細資訊，請參閱 [本篇文章](./virtual-machine-scale-sets-portal-create.md)。 另一個開始使用的簡單方法是使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) 來部署擴展集。 下列範例示範如何建立含有 10 個 VM 的 Ubuntu 等級擴展集，每個 VM 各有 50 GB 和 100 GB 的資料磁碟：

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

或者，您可以針對包含 `vmss` 的資料夾查閱 [Azure 快速入門範本 GitHub 存放庫 (英文)](https://github.com/Azure/azure-quickstart-templates)，以查看預先建置來部署擴展集之範本的範例。 若要分辨哪些範本已經在使用受控磁碟，您可以參考[這份清單](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)。

## <a name="next-steps"></a>後續步驟

如需更多有關一般受控磁碟的資訊，請參閱[這篇文章](../virtual-machines/windows/managed-disks-overview.md)。

若要了解如何轉換 Resource Manager 範本來佈建含有受控磁碟的擴展集，請參閱[這篇文章](./virtual-machine-scale-sets-convert-template-to-md.md)。 對於 Resource Manager 範本的修改同樣適用於 Azure REST API。

若要深入了解如何搭配使用受控資料磁碟與擴展集，請參閱[這篇文章](./virtual-machine-scale-sets-attached-disks.md)。

若要開始使用大型擴展集，請參閱[這篇文章](./virtual-machine-scale-sets-placement-groups.md)。


