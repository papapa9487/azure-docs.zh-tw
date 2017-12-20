---
title: "使用 cloud-init 在 Azure 上的 Linux VM 中更新和安裝封裝 | Microsoft Docs"
description: "如何透過 Azure CLI 2.0 在建立期間使用 cloud-init 在 Linux 虛擬機器中更新和安裝封裝"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 4209bc270a6d255c8512dd6ccd5551b556da5a6b
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>使用 cloud-init 在 Azure 上的 Linux VM 中更新和安裝封裝
本文會示範如何在 Azure 佈建期間，使用 [cloud-init](https://cloudinit.readthedocs.io) 在 Linux 虛擬機器 (VM) 上或虛擬機器擴展集 (VMSS) 上更新封裝。 一旦 Azure 佈建資源之後，這些 cloud-init 指令碼就會在初次開機時執行。 如需深入了解 cloud-init 如何以原生方式在 Azure 和支援的 Linux 散發版本中運作，請參閱 [cloud-init 概觀](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>使用 cloud-init 更新 VM
基於安全考量，您應該設定 VM 以在第一次開機時套用最新的更新。 由於 cloud-init 可在不同的 Linux 散發版本上運作，所以不需要為套件管理員指定 `apt` 或 `yum`。 相反地，您可定義 `package_upgrade` 並讓 cloud-init 程序判斷使用中散發版本的適當機制。 此工作流程可讓您在不同的散發版本上使用相同的 cloud-init 指令碼。

若要查看作用中的升級程序，請在目前的殼層中建立名為 cloud_init_upgrade.txt 的檔案，然後貼上下列設定。 針對此範例，請在 Cloud Shell 中 (而不是本機電腦上) 建立該檔案。 您可以使用任何您想要的編輯器。 輸入 `sensible-editor cloud_init_upgrade.txt` 可建立檔案，並查看可用的編輯器清單。 建議選擇使用 **nano** 編輯器。 請確定已正確複製整個 cloud-init 檔案，特別是第一行。  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

部署此映像前，您必須使用 [az group create](/cli/azure/group#create) 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

現在，請使用 [az vm create](/cli/azure/vm#create) 建立 VM 並以 `--custom-data cloud_init_upgrade.txt` 指定 cloud-init 檔案，如下所示：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

以 SSH 連線到顯示於由上述命令所產生之輸出中的 VM 公用 IP 位址。 輸入您自己的 **publicIpAddress**，如下所示︰

```bash
ssh <publicIpAddress>
```

執行套件管理工具並檢查是否有更新。 下列範例會在 Ubuntu VM 上使用 `apt-get`：

```bash
sudo apt-get upgrade
```

由於 cloud-init 會在開機時檢查是否有更新並予以安裝，因此應已無要套用的更新，如下列範例輸出所示：

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

您也可以檢視執行 `yum history` 所安裝的 `httpd`，並檢視輸出參考 `httpd`。 

## <a name="next-steps"></a>後續步驟
如需其他設定變更的 cloud-init 範例，請參閱下列文件：
 
- [將其他 Linux 使用者新增至虛擬機器](cloudinit-add-user.md)
- [執行封裝管理員以便在初次開機時更新現有的封裝](cloudinit-update-vm.md)
- [變更虛擬機器本機的主機名稱](cloudinit-update-vm-hostname.md) 
- [安裝應用程式封裝、更新設定檔及插入金鑰](tutorial-automate-vm-deployment.md)