---
title: "Azure CLI 指令碼範例 - 從相同訂用帳戶的儲存體帳戶的 VHD 檔案建立受管理的磁碟 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 從相同訂用帳戶的儲存體帳戶的 VHD 檔案建立受管理的磁碟"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 448636e87db126defc804a613bb61ff19a086ad9
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>使用 CLI 從相同訂用帳戶的儲存體帳戶的 VHD 檔案建立受管理的磁碟

此指令碼會從相同訂用帳戶的儲存體帳戶的 VHD 檔案建立受管理的磁碟。 請使用此指令碼將特殊化 (未一般化/執行 sysprep) 的 VHD 匯入受控的作業系統磁碟，以建立虛擬機器。 或者，用它將資料 VHD 匯入到受控資料磁碟。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "從 VHD 建立受控磁碟")]


## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令從 VHD 建立受控磁碟。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | 在相同的訂用帳戶中使用儲存體帳戶的 VHD URI 受控磁碟 |

## <a name="next-steps"></a>後續步驟

[將受控磁碟連結為作業系統磁碟以建立虛擬機器](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Linux VM 文件](../../app-service-web/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器和受控磁碟 CLI 指令碼範例。

