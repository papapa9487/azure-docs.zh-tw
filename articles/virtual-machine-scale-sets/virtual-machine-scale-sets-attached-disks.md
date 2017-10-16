---
title: "Azure 虛擬機器擴展集連結資料磁碟 | Microsoft Docs"
description: "了解如何搭配使用連線資料磁碟與虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: guybo
ms.openlocfilehash: 22c7e589efa9a9f401549ec9b95c58c4eaf07b94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-vm-scale-sets-and-attached-data-disks"></a>Azure VM 擴展集與連線資料磁碟
Azure [虛擬機器擴展集](/azure/virtual-machine-scale-sets/)現在支援具有連線資料磁碟的虛擬機器。 您可以在使用 Azure 受控磁碟建立的擴展集儲存體設定檔中定義資料磁碟。 先前擴展集中 VM 唯一可用的直接連結儲存體選項是作業系統磁碟機和暫存磁碟機。

> [!NOTE]
>  當您建立已定義連結資料磁碟的擴展集時，仍需掛接和格式化 VM 內的磁碟才能加以使用 (就如同獨立 Azure VM)。 方便的作法是使用自訂指令碼擴充功能，該擴充功能可呼叫標準指令碼來分割及格式化 VM 上的所有資料磁碟。

## <a name="create-a-scale-set-with-attached-data-disks"></a>建立具有連結資料磁碟的擴展集
使用 [Azure CLI](https://github.com/Azure/azure-cli) _vmss create_ 命令是建立具有連結磁碟之擴展集的簡便方法。 下列範例會建立 Azure 資源群組，以及由 10 部 Ubuntu VM 組成的 VM 擴展集，而每部 VM 各有 2 個連結資料磁碟 (分別為 50 GB 和 100 GB)。
```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```
請注意，_vmss create_ 命令會預設某些組態值 (如果您未指定它們)。 若要查看您可覆寫的可用選項，請嘗試︰
```bash
az vmss create --help
```
另一種建立具有連結資料磁碟之擴展集的方法是在 Azure Resource Manager 範本中定義擴展集、在 _storageProfile_ 中包含 _dataDisks_ 區段，以及部署範本。 上述 50 GB 和 100 GB 磁碟範例會在範本中定義如下︰
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```
您可以在此看到完整、可立即部署並已定義連結磁碟的擴展集範例︰[https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data)。

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>將資料磁碟新增至現有的擴展集
> [!NOTE]
>  您只能將資料磁碟連結至使用 [Azure 受控磁碟](./virtual-machine-scale-sets-managed-disks.md)建立的擴展集。

您可以使用 Azure CLI _az vmss disk attach_ 命令，將資料磁碟新增至 VM 擴展集。 務必指定尚未使用的 lun。 下列 CLI 範例將 50 GB 磁碟機新增至 lun 3︰
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

下列 PowerShell 範例將 50 GB 磁碟機新增至 lun 3︰
```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> 不同的 VM 大小對其支援的連結磁碟機數目會有不同的限制。 新增磁碟前，請檢查[虛擬機器大小特性](../virtual-machines/windows/sizes.md)。

將項目新增至擴展集定義的 _storageProfile_ 中的 _dataDisks_ 屬性並套用變更，也可以新增磁碟。 若要進行測試，在 [Azure 資源總管](https://resources.azure.com/)中尋找現有的擴展集定義。 選取 [編輯] 並將新磁碟新增至資料磁碟清單。 例如 使用上述範例︰
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

然後選取 _PUT_ 將變更套用到您的擴展集。 只要您使用的 VM 大小可支援兩個以上的連結資料磁碟，此範例就可行。

> [!NOTE]
> 當您變更擴展集定義時 (例如新增或移除資料磁碟)，它會套用到所有新建立的 VM，但如果 _upgradePolicy_ 屬性設定為 [自動]，則只會套用至現有的 VM。 如果該屬性設定為 [手動]，您必須以手動方式將新的模型套用至現有的 VM。 您可以在入口網站中，使用 _Update-AzureRmVmssInstance_ PowerShell 命令，或使用 _az vmss update-instances_ CLI 命令。

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>將預先填入的資料磁碟新增至現存的擴展集 
> 當您將磁碟新增至現存的擴展集模型時，依照設計，一律會建立空白的磁碟。 此案例也會包含擴展集所建立的新執行個體。 這是因為擴展集定義具有空的資料磁碟。 若要針對現存的擴展集模型建立預先填入的資料磁碟機，您可以選擇下列其中一個選項：

* 藉由執行自訂指令碼，將資料從執行個體 0 VM 複製到其他 VM 中的資料磁碟。
* 建立具有 OS 磁碟再加上資料磁碟 (包含所需資料) 的受控映像，並使用此映像建立新的擴展集。 如此一來，每個新 VM 都會有擴展集定義中提供的資料磁碟。 因為此定義參考的映像具有已自訂資料的資料磁碟，所以擴展集上的每部虛擬機器都會自動出現這些變更。

> 在此可找到建立自訂映像的方式：[在 Azure 中建立一般化 VM 的受控映像](/azure/virtual-machines/windows/capture-image-resource/) 

> 使用者必須擷取具有所需資料的執行個體 0 VM，然後使用映像定義的該 vhd。

## <a name="removing-a-data-disk-from-a-scale-set"></a>從擴展集中移除資料磁碟
您可以使用 Azure CLI _az vmss disk detach_ 命令，從 VM 擴展集中移除資料磁碟。 例如下列命令會移除在 lun 2 定義的磁碟︰
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
同樣第，從 _storageProfile_ 中的 _dataDisks_ 屬性中移除項目並套用變更，也可以從擴展集中移除磁碟。 

## <a name="additional-notes"></a>其他注意事項
API 版本 [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) 或更新版本的 Microsoft.Compute API 中提供 Azure 受控磁碟和擴展集連結資料磁碟的支援。

在擴展集連結磁碟支援的初始實作中，您無法對擴展集中的個別 VM 附加或卸離資料磁碟。

Azure 入口網站對於擴展集中連結資料磁碟的支援一開始就有限制。 視您的需求而定，您可以使用 Azure 範本、CLI、PowerShell、SDK 和 REST API 來管理連結磁碟。


