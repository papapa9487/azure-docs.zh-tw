---
title: "將 Azure 受控磁碟儲存體從標準轉換至進階，反之亦然 | Microsoft Docs"
description: "如何使用 Azure CLI 將 Azure 受控磁碟儲存體從標準轉換至進階，反之亦然。"
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: c22c2c194cb839c3ec9e3e851768ca19bc6fc443
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>將 Azure 受控磁碟儲存體從標準轉換至進階，反之亦然

受控磁碟提供兩個儲存體選項：[進階](../windows/premium-storage.md) (以 SSD 為基礎) 和[標準](../windows/standard-storage.md) (以 HDD 為基礎)。 它可讓您根據您的效能需求，在兩個選項之間，以最少的停機時間輕鬆切換。 這項功能不適用於非受控磁碟。 但您可以輕鬆地[轉換為受控磁碟](convert-unmanaged-to-managed-disks.md)，以在兩個選項之間輕鬆切換。

本文說明如何使用 Azure CLI 將受控磁碟從標準轉換至進階，反之亦然。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli.md)。 

## <a name="before-you-begin"></a>開始之前

* 轉換需要重新啟動 VM，因此請在預先存在的維護期間排定磁碟儲存體移轉。 
* 如果您使用非受控磁碟，請先[轉換為受控磁碟](convert-unmanaged-to-managed-disks.md)，以使用本文在兩個儲存體選項之間切換。 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>將 VM 的所有受控磁碟從標準轉換至進階，反之亦然

在下列範例中，我們會示範如何將 VM 的所有磁碟從標準儲存體切換成進階儲存體。 若要使用進階受控磁碟，您的 VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會切換成可支援進階儲存體的大小。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>將受控磁碟從標準轉換至進階，反之亦然

對於您的開發/測試工作負載，您可能希望混合標準和進階磁碟，以降低成本。 您可以藉由升級至進階儲存體來完成此作業，僅限需要更佳效能的磁碟。 在下列範例中，我們會示範如何將 VM 的單一磁碟從標準儲存體切換成進階儲存體，反之亦然。 若要使用進階受控磁碟，您的 VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會切換成可支援進階儲存體的大小。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>後續步驟

使用[快照](snapshot-copy-managed-disk.md)來取得 VM 的唯讀複本。

