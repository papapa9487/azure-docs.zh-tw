---
title: "使用 Azure CLI 1.0 擴充 Linux VM 上的 OS 磁碟 | Microsoft Docs"
description: "了解如何使用 Azure CLI 1.0 和 Resource Manager 部署模型，來擴充 Linux VM 上的作業系統 (OS) 虛擬磁碟"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 0aedcd70b54c2ed47ec327ccf0529a48351353c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>透過 Azure CLI 1.0 使用 Azure CLI 擴充 Linux VM 上的 OS 磁碟
在 Azure 中，Linux 虛擬機器 (VM) 上作業系統 (OS) 的預設虛擬硬碟大小通常是 30 GB。 您可以[新增資料磁碟](add-disk.md)，以提供更多儲存空間，但您也可能想要擴充 OS 磁碟。 本文將詳細說明如何搭配使用非受控磁碟與 Azure CLI 1.0，來擴充 Linux VM 的 OS 磁碟。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#prerequisites) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](expand-disks.md) - 適用於資源管理部署模型的新一代 CLI

## <a name="prerequisites"></a>必要條件
您需要安裝[最新的 Azure CLI 1.0](../../cli-install-nodejs.md)，而且已使用 Resource Manager 模式登入 [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)，如下所示：

```azurecli
azure config mode arm
```

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 myResourceGroup 與 myVM。

## <a name="expand-os-disk"></a>擴充 OS 磁碟

1. 當 VM 正在執行時，無法對虛擬硬碟執行作業。 下列範例會停止並解除配置名為 myResourceGroup 的資源群組中名為 myVM 的 VM：

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` 不會釋放計算資源。 若要釋放計算資源，請使用 `azure vm deallocate`。 必須解除配置 VM，才能擴充虛擬硬碟。

2. 使用 `azure vm set` 命令來更新非受控 OS 磁碟的大小。 下列範例會將名為 myResourceGroup 的資源群組中名為 myVM 的 VM 更新為 50 GB：

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. 啟動您的 VM，如下所示︰

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. 使用適當的認證以 SSH 登入 VM。 若要確認 OS 磁碟已調整大小，請使用 `df -h`。 下列範例輸出顯示主要磁碟分割 (/dev/sda1) 現在是 50 GB：

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>後續步驟
如果您需要更多儲存空間，您也可以[將資料磁碟新增至 Linux VM](add-disk.md)。 如需磁碟加密的詳細資訊，請參閱[使用 Azure CLI 將 Linux VM 上的磁碟加密](encrypt-disks.md)。
