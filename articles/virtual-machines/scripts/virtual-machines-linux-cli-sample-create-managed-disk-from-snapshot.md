---
title: "Azure CLI 指令碼範例 - 從快照集建立受控磁碟 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 從快照集建立受控磁碟"
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
ms.openlocfilehash: 68e17ae9e5d82da7f9be9d36e3e2324a2aeadbc4
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>使用 CLI 從快照集建立受控磁碟

此指令碼會從快照集建立受控磁碟。 使用它從 OS 和資料磁碟的快照集還原虛擬機器。 從個別的快照集建立 OS 和資料受控磁碟，再連結受控磁碟以建立新的虛擬機器。 您也可以連結從快照集建立的資料磁碟，以還原現有 VM 的資料磁碟。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "從快照集建立受控磁碟")]


## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令從快照集建立受控磁碟。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | 使用快照集的名稱和資源群組屬性，取得快照集的所有屬性。 使用 Id 屬性建立受控磁碟。  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | 使用受管理快照集的快照集識別碼建立受控磁碟 |

## <a name="next-steps"></a>後續步驟

[將受控磁碟連結為作業系統磁碟以建立虛擬機器](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Linux VM 文件](../../app-service-web/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器和受控磁碟 CLI 指令碼範例。

