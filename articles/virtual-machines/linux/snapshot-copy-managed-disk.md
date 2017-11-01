---
title: "在 Azure 中建立 VHD 的快照集 | Microsoft Docs"
description: "了解如何在 Azure 中建立 VHD 的複本，以作為備份，或用來針對問題進行疑難排解。"
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: da00c48f7da5a9be146f4fdb626c93db746c0f9b
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>建立快照集 

製作作業系統或資料磁碟 VHD 的快照集作為備份，或是用來針對 VM 問題進行疑難排解。 快照集是完整的 VHD 唯讀複本。 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>使用 Azure CLI 2.0 製作快照集

下列範例需要安裝 Azure CLI 2.0 並登入您的 Azure 帳戶。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

下列步驟說明如何使用 `az snapshot create` 命令搭配 `--source-disk` 參數製作快照集。 下列範例假設有一個名為 `myVM` 的 VM，該 VM 是使用 `myResourceGroup` 資源群組中的受控 OS 磁碟加以建立。

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

輸出應如下所示︰

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>使用 Azure 入口網站建立快照集 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左上方開始，按一下 [新增]並搜尋**快照集**。
3. 在 [快照集] 刀鋒視窗中，按一下 [建立]。
4. 輸入快照集的 [名稱]。
5. 選取現有的[資源群組](../../azure-resource-manager/resource-group-overview.md#resource-groups)，或輸入新群組的名稱。 
6. 選取 Azure 資料中心的 [位置]。  
7. 在 [來源磁碟] 中，選取要建立快照集的受控磁碟。
8. 選取用來儲存快照集的 [帳戶類型]。 除非需要儲存在高效能磁碟上，否則建議選取 **Standard_LRS**。
9. 按一下 [建立] 。

如果您打算使用快照集來建立受控磁碟，並將它附加至必須是高效能的 VM，請使用 `--sku Premium_LRS` 參數搭配 `az snapshot create` 命令。 這麼做建立的快照集會儲存為進階受控磁碟。 進階受控磁碟的效能比較好，因為它們是固態硬碟 (SSD)，但成本高於標準磁碟 (HDD)。


## <a name="next-steps"></a>後續步驟

 從快照集建立受控磁碟，然後連結新的受控磁碟作為作業系統磁碟，以從快照集建立虛擬機器。 如需詳細資訊，請參閱[從快照集建立 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) 指令碼。

