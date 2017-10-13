---
title: "如何使用 Azure CLI 1.0 重新調整 Linux VM 的大小 | Microsoft Docs"
description: "如何藉由變更 VM 的大小來相應增加或相應減少 Linux 虛擬機器。"
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72f5a3cd6463befd5108040ed166984281bfc5f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="resize-a-linux-vm-with-azure-cli-10"></a>使用 Azure CLI 1.0 來調整 Linux VM 大小

## <a name="overview"></a>概觀

部屬虛擬機器 (VM) 之後，您可以藉由變更 [VM 大小][vm-sizes]來相應增加或減少 VM。 在某些情況下，您必須先解除配置 VM。 如果新的大小無法在裝載 VM 的硬體叢集上取得，可能有這樣的情況。

本文說明如何使用 [Azure CLI][azure-cli] 重新調整 Linux VM 的大小。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#resize-a-linux-vm) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI


## <a name="resize-a-linux-vm"></a>重新調整 Linux VM 的大小
若要重新調整 VM 的大小，請執行下列步驟。

1. 執行下列 CLI 命令。 這個命令會列出裝載 VM 的硬體叢集上的可用 VM 大小。
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. 如果有列出所需的大小，請執行以下命令來調整 VM 的大小。
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    在此程序中 VM 會重新啟動。 重新啟動之後，您現有的作業系統和資料磁碟將會重新對應。 暫存磁碟的所有項目將會遺失。
   
    使用 `--enable-boot-diagnostics` 選項可讓[開機診斷][boot-diagnostics]記錄任何與啟動相關的錯誤。
3. 另一方面，如果沒有列出所需的大小，請執行以下命令以將 VM 解除配置、重新調整大小，然後再將它重新啟動。
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > 將 VM 解除配置也會釋出指派給該 VM 的任何動態 IP 位址。 不會影響作業系統和資料磁碟。
   > 
   > 

## <a name="next-steps"></a>後續步驟
如需提高延展性，可執行多個 VM 執行個體並相應放大。如需詳細資訊，請參閱[在虛擬機器擴展集中自動調整 Linux 機器][scale-set]。 

<!-- links -->

[azure-cli]:../../cli-install-nodejs.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
