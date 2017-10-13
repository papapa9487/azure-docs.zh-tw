---
title: "在 Azure 中的建立期間使用 cloud-init 自訂 Linux VM | Microsoft Docs"
description: "如何透過 Azure CLI 1.0 在建立期間使用 cloud-init 自訂 Linux VM"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
ms.openlocfilehash: 0b6150bca333188666935b3c9aa02c4b33690db9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>透過 Azure CLI 1.0 在建立期間使用 cloud-init 自訂 Linux VM
本文中示範如何製作 cloud-init 指令碼來設定主機名稱、更新已安裝的封裝及管理使用者帳戶。  在 VM 建立期間，會從 Azure CLI 呼叫 cloud-init 指令碼。  本文需要：

* 一個 Azure 帳戶 ([取得免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/))。
* 使用 `azure login` 登入的 [Azure CLI](../../cli-install-nodejs.md)。
* Azure CLI *必須處於* Azure Resource Manager 模式 `azure config mode arm`。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#quick-commands) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI

## <a name="quick-commands"></a>快速命令
建立一個設定主機名稱、更新所有封裝、並將 sudo 使用者新增至 Linux 的 cloud-init.txt 指令碼。

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
建立資源群組，以將 VM 啟動至其中。

```azurecli
azure group create myResourceGroup westus
```

使用 cloud-init 建立一個 Linux VM 以在開機時進行設定。

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說
### <a name="introduction"></a>簡介
啟動新的 Linux VM 時，Linux VM 會是標準模式，沒有自訂或符合您需求的選項。 [Cloud-init](https://cloudinit.readthedocs.org) 第一次啟動時，會是在 Linux VM 中插入指令碼或組態設定的標準方法。

Azure 有三種不同的方法可在 Linux VM 部署或啟動時進行變更。

* 使用 cloud-init 插入指令碼。
* 使用 Azure [VMAccess 延伸模組](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)插入指令碼。
* 使用 cloud-init 的 Azure 範本。
* 使用 [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的 Azure 範本。

若開機後隨時要插入指令碼︰

* 直接執行命令的 SSH
* 使用 Azure [VMAccess 延伸模組](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，以命令方式或在 Azure 範本中插入指令碼
* 組態管理工具，例如 Ansible、Salt、Chef 及 Puppet。

> [!NOTE]
> ：VMAccess 延伸模組會以 root 身分，以使用 SSH 時的相同方式執行指令碼。  不過，使用 VM 延伸模組可視您的案例啟用數個 Azure 提供的實用功能。
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Cloud-init 在 Azure VM quick-create 映像別名上的可用性︰
| Alias | 發佈者 | 提供項目 | SKU | 版本 | Cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |最新 |no |
| CoreOS |CoreOS |CoreOS |Stable |最新 |yes |
| Debian |credativ |Debian |8 |最新 |no |
| openSUSE |SUSE |openSUSE |13.2 |最新 |no |
| RHEL |Redhat |RHEL |7.2 |最新 |no |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |最新 |yes |

Microsoft 正與我們的合作夥伴合作，以期在他們提供給 Azure 的映像中包含和使用 cloud-init。

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>將 cloud-init 指令碼加入使用 Azure CLI 建立 VM 的作業中
在 Azure 中建立 VM 時，若要啟動 cloud-init 指令碼，請使用 Azure CLI `--custom-data` 參數來指定 cloud-init 檔案。

建立資源群組，以將 VM 啟動至其中。

```azurecli
azure group create myResourceGroup westus
```

使用 cloud-init 建立一個 Linux VM 以在開機時進行設定。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>建立 cloud-init 指令碼設定 Linux VM 的主機名稱
對任何 Linux VM 而言，其中一個最簡單且最重要的設定就是主機名稱。 使用 cloud-init 和這個指令碼就可以輕鬆地設定這個項目。  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>名為 `cloud_config_hostname.txt`的範例 cloud-init 指令碼。
```sh
#cloud-config
hostname: myservername
```

在 VM 首次啟動期間，這個 cloud-init 指令碼會將主機名稱設定為 `myservername`。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

登入並驗證新 VM 的主機名稱。

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>建立 cloud-init 指令碼以更新 Linux 
基於安全性，您希望您的 Ubuntu VM 能在第一次開機時進行更新。  我們可以使用 cloud-init 和下列指令碼執行這個作業，視您使用的 Linux 散發套件而定。

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>適用於 Debian 系列的範例 cloud-init 指令碼 `cloud_config_apt_upgrade.txt`
```sh
#cloud-config
apt_upgrade: true
```

在 Linux 開機後，所有已安裝的封裝都會透過 `apt-get`更新。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

登入並驗證所有封裝是否皆已更新。

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>建立 cloud-init 指令碼將使用者加入 Linux 中
針對任何新的 Linux VM，首要工作之一就是為您自己新增一位使用者，或是避免使用 `root`。 SSH 金鑰是基於安全性和可用性的最佳作法，它們會隨此 cloud-init 指令碼新增至 `~/.ssh/authorized_keys` 檔案。

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>適用於 Debian 系列的範例 cloud-init 指令碼 `cloud_config_add_users.txt`
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Linux 開機之後，所有列出的使用者就會建立並加入 sudo 群組。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

登入並驗證新建立的使用者。

```bash
ssh myVM
cat /etc/group
```

輸出

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>後續步驟
Cloud-init 已成為在開機時修改 Linux VM 的一種標準方式。 Azure 也有 VM 延伸模組，可讓您在開機或執行時修改您的 LinuxVM。 例如，當 VM 執行時，您可以使用 Azure VMAccessExtension 來重設 SSH 或使用者資訊。 使用 cloud-init，您必須重新開機才能重設密碼。

[有關虛擬機器擴充功能和功能](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[管理使用者、SSH，並使用 VMAccess 擴充功能檢查或修復 Azure Linux VM 上的磁碟](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

