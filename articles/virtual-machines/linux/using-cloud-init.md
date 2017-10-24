---
title: "使用 cloud-init 自訂 Linux VM | Microsoft Docs"
description: "如何透過 Azure CLI 2.0 在建立期間使用 cloud-init 自訂 Linux VM"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>在 Azure 中使用 cloud-init 自訂 Linux VM
本文會示範如何在 Azure 中的虛擬機器 (VM) 上使用 [cloud-init](https://cloudinit.readthedocs.io) \(英文\) 來設定主機名稱、更新套件及管理使用者帳戶。 使用 Azure CLI 2.0 建立 VM 時，這些 cloud-init 指令碼會在開機時執行。 如需如何安裝應用程式、撰寫設定檔，以及從 Key Vault 插入金鑰的更深入概觀，請參閱[本教學課程](tutorial-automate-vm-deployment.md)。 您也可以使用 [Azure CLI 1.0](using-cloud-init-nodejs.md) 來執行這些步驟。


## <a name="cloud-init-overview"></a>Cloud-Init 概觀
[Cloud-init (英文)](https://cloudinit.readthedocs.io) 是在 Linux VM 初次開機時，廣泛用來自訂它們的方法。 您可以使用 cloud-init 來安裝封裝和寫入檔案，或者設定使用者和安全性。 當 cloud-init 在初次開機程序期間執行時，不需要使用任何額外的步驟或必要的代理程式來套用您的組態。

Cloud-init 也適用於散發套件。 例如，您不使用 **apt-get install** 或 **yum install** 來安裝套件。 您可以改為定義要安裝的套件清單。 Cloud-init 會針對您選取的散發套件自動使用原生的套件管理工具。

我們正在與合作夥伴合作，以期在他們提供給 Azure 的映像中包含和使用 cloud-init。 下表概述 cloud-init 目前在 Azure 平台映像上的可用性：

| Alias | 發佈者 | 提供項目 | SKU | 版本 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-LTS |最新 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |最新 |
| CoreOS |CoreOS |CoreOS |Stable |最新 |


## <a name="set-the-hostname-with-cloud-init"></a>使用 cloud-init 設定主機名稱
Cloud-init 檔案是以 [YAML](http://www.yaml.org) \(英文\) 撰寫。 若要在您使用 [az vm create](/cli/azure/vm#create) 於 Azure 中建立 VM 時執行 cloud-init 指令碼，請使用 `--custom-data` 參數來指定 cloud-init 檔案。 讓我們來看看您可以使用 cloud-init 檔案設定的一些範例。 常見的案例是設定 VM 的主機名稱。 根據預設，主機名稱會與 VM 名稱相同。 

首先，使用 [az group create](/cli/azure/group#create) 建立資源群組。 下列範例會在 *eastus* 位置建立名為 *myResourceGroup* 的資源群組：

```azurecli
az group create --name myResourceGroup --location eastus
```

在您目前的殼層中，建立名為 *cloud_init_hostname.txt* 的檔案，並貼上下列設定。 例如，在 Cloud Shell 中建立不在本機電腦上的檔案。 您可以使用任何您想要的編輯器。 在 Cloud Shell 中，輸入 `sensible-editor cloud_init_hostname.txt` 以建立檔案並查看可用的編輯器清單。 請確定已正確複製整個 cloud-init 檔案，特別是第一行：

```yaml
#cloud-config
hostname: myhostname
```

現在，請使用 [az vm create](/cli/azure/vm#create) 建立 VM 並以 `--custom-data cloud_init_hostname.txt` 指定 cloud-init 檔案，如下所示：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

建立之後，Azure CLI 隨即顯示 VM 的相關資訊。 使用 `publicIpAddress` 來以 SSH 連線到您的 VM。 輸入您自己的位址，如下所示︰

```bash
ssh azureuser@publicIpAddress
```

若要查看 VM 名稱，請使用 `hostname` 命令，如下所示：

```bash
hostname
```

VM 所報告的主機名稱應該會和 cloud-init 檔案中所設定的值相同，如下列範例輸出所示：

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>使用 cloud-init 更新 VM
基於安全考量，您應該設定 VM 以在第一次開機時套用最新的更新。 由於 cloud-init 可在不同的 Linux 散發版本上運作，所以不需要為套件管理員指定 `apt` 或 `yum`。 相反地，您可定義 `package_upgrade` 並讓 cloud-init 程序判斷使用中散發版本的適當機制。 此工作流程可讓您在不同的散發版本上使用相同的 cloud-init 指令碼。

若要查看作用中的升級程序，請建立名為 *cloud_init_upgrade.txt* 的 cloud-init 檔案，然後貼上下列設定：

```yaml
#cloud-config
package_upgrade: true
```

現在，請使用 [az vm create](/cli/azure/vm#create) 建立 VM 並以 `--custom-data cloud_init_upgrade.txt` 指定 cloud-init 檔案，如下所示：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

以 SSH 連線到顯示於由上述命令所產生之輸出中的 VM 公用 IP 位址。 輸入您自己的公用 IP 位址，如下所示︰

```bash
ssh azureuser@publicIpAddress
```

執行套件管理工具並檢查是否有更新。 下列範例會在 Ubuntu VM 上使用 `apt-get`：

```bash
sudo apt-get upgrade
```

由於 cloud-init 會在開機時檢查是否有更新並予以安裝，因此已無要套用的更新，如下列範例輸出所示：

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>使用 cloud-init 將使用者新增至 VM
針對任何新的 Linux VM，首要工作之一就是為您自己新增一個使用者，以避免使用*根*。 SSH 金鑰是提供安全性和可用性的最佳做法。 此 cloud-init 指令碼會將金鑰新增至 *~/.ssh/authorized_keys* 檔案。

若要將使用者新增至 Linux VM，請建立名為 *cloud_init_add_user.txt* 的 cloud-init 檔案，然後貼上下列設定。 針對 *ssh-authorized-keys* 提供您自己公開金鑰，(例如 *~/.ssh/id_rsa.pub* 的內容)：

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

現在，請使用 [az vm create](/cli/azure/vm#create) 建立 VM 並以 `--custom-data cloud_init_add_user.txt` 指定 cloud-init 檔案，如下所示：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

以 SSH 連線到顯示於由上述命令所產生之輸出中的 VM 公用 IP 位址。 輸入您自己的公用 IP 位址，如下所示︰

```bash
ssh myadminuser@publicIpAddress
```

若要確認已將您的使用者新增至 VM 與指定的群組，請檢視 */etc/group* 檔案的內容，如下所示：

```bash
cat /etc/group
```

下列範例輸出顯示來自 *cloud_init_add_user.txt* 檔案的使用者已新增至 VM 與適當的群組：

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>後續步驟
Cloud-init 是在開機時修改 Linux VM 的其中一種標準方式。 在 Azure 中，您也可以使用 VM 擴充功能來在開機時或在 VM 執行之後修改 Linux VM。 例如，您可以使用 Azure VM 擴充功能在執行中的 VM 上執行指令碼，而非僅在首次開機時執行。 如需 VM 擴充功能的詳細資訊，請參閱 [VM 擴充功能和功能](extensions-features.md)；或者，若要取得如何使用擴充功能的範例，請參閱[使用 VMAccess 擴充功能在 Azure Linux VM 上管理使用者、SSH 及檢查或修復磁碟](using-vmaccess-extension.md)。