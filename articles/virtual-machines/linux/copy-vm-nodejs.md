---
title: "使用 Azure CLI 1.0 建立 Linux VM 的複本 | Microsoft Docs"
description: "了解如何在 Resource Manager 部署模型中，使用 Azure CLI 1.0 建立 Azure Linux 虛擬機器的複本"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 62ae54f3596c9383cbf3b401fcfdb42ecfdee63c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>使用 Azure CLI 1.0 建立在 Azure 上執行的 Linux 虛擬機器複本
本文示範如何使用 Resource Manager 部署模型來建立執行 Linux 的 Azure 虛擬機器 (VM) 複本。 首先，您需將作業系統和資料磁碟複製到新容器中，然後設定網路資源並建立新的虛擬機器。

您也可以[上傳自訂磁碟映像並從這個映像建立 VM](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- Azure CLI 1.0 - 適用於傳統和資源管理部署模型 (本文) 的 CLI
- [Azure CLI 2.0](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI

## <a name="before-you-begin"></a>開始之前
請先確保符合下列必要條件再開始以下步驟︰

* 您已在電腦上下載及安裝 [Azure CLI](../../cli-install-nodejs.md) 。 
* 您也需要現有 Azure Linux VM 的一些相關資訊：

| 來源 VM 資訊 | 從哪裡取得 |
| --- | --- |
| VM 名稱 |`azure vm list` |
| 資源群組名稱 |`azure vm list` |
| 位置 |`azure vm list` |
| 儲存體帳戶名稱 |`azure storage account list -g <resourceGroup>` |
| 容器名稱 |`azure storage container list -a <sourcestorageaccountname>` |
| 來源 VM VHD 檔案名稱 |`azure storage blob list --container <containerName>` |

* 您將需要進行新 VM 的一些相關選擇：   <br> -容器名稱    <br> -VM 名稱    <br> -VM 大小    <br> -vNet 名稱    <br> -SubNet 名稱    <br> -IP 名稱    <br> -NIC 名稱

## <a name="login-and-set-your-subscription"></a>登入及設定您的訂用帳戶
1. 登入 CLI。

    ```azurecli
    azure login
    ```
2. 確定您處於 Resource Manager 模式。

    ```azurecli
    azure config mode arm
    ```
3. 設定正確的訂用帳戶。 您可以使用 'azure account list' 來查看您的所有訂用帳戶。

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>停止 VM
將來源 VM 停止並解除配置。 您可以使用 'azure vm list' 來取得訂用帳戶中所有 VM 及其資源群組名稱的清單。

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>複製 VHD
您可以使用 `azure storage blob copy start`將 VHD 從來源儲存體複製到目的地。 在此範例中，我們將會把 VHD 複製到相同的儲存體帳戶但不同的容器中。

若要將 VHD 複製到相同儲存體帳戶中的另一個容器，請輸入：

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>為新 VM 設定虛擬網路
為新 VM 設定虛擬網路和 NIC。 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>建立新 VM
您現在可以 [使用 Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) 從已上傳的虛擬磁碟建立 VM，或透過 CLI 藉由輸入下列命令來指定所複製磁碟的 URI 以建立 VM︰

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>後續步驟
若要了解如何使用 Azure CLI 來管理新虛擬機器，請參閱 [Azure Resource Manager 的 Azure CLI 命令](../azure-cli-arm-commands.md)。

