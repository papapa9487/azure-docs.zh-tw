---
title: "複製 Azure 受控磁碟作為備份 | Microsoft Docs"
description: "了解如何建立 Azure 受控磁碟的複本作為備份，或用於針對磁碟問題進行疑難排解。"
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
ms.openlocfilehash: c91367ef11c9d531bebac7c069d2df586607ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>使用受控快照集建立 VHD 的複本並儲存為 Azure 受控磁碟
建立受控磁碟的快照集作為備份，或從快照集建立受控磁碟並將它附加至測試虛擬機器進行疑難排解。 受控快照集是 VM 受控磁碟的完整時間點複本。 它會建立 VHD 的唯讀複本，而且根據預設儲存為標準受控磁碟。 

如需價格的詳細資訊，請參閱 [Azure 儲存體價格](https://azure.microsoft.com/pricing/details/managed-disks/)。 <!--Add link to topic or blog post that explains managed disks. -->

使用 Azure 入口網站或 Azure CLI 2.0 製作受控磁碟的快照集。

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>使用 Azure CLI 2.0 製作快照集

> [!NOTE] 
> 下列範例需要安裝 Azure CLI 2.0 並登入您的 Azure 帳戶。

下列步驟說明如何使用 `az snapshot create` 命令搭配 `--source-disk` 參數，取得及製作受控 OS 磁碟的快照集。 下列範例假設有一個名為 `myVM` 的 VM，該 VM 是使用 `myResourceGroup` 資源群組中的受控 OS 磁碟加以建立。

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


