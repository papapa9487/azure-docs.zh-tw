---
title: "使用 Ansible 在 Azure 中建立基本 Linux VM | Microsoft Docs"
description: "了解如何使用 Ansible 在 Azure 中建立及管理基本 Linux 虛擬機器"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: ee499b57733cd19dfe411e0e463dd3c65e82a52d
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>使用 Ansible 在 Azure 中建立基本虛擬機器
Ansible 可讓您將環境中的資源部署和設定自動化。 您可以使用 Ansible 在 Azure 中管理虛擬機器 (VM)，就像是任何其他資源一樣。 本文示範如何使用 Ansible 建立基本 VM。 您也可以了解如何[使用 Ansible 建立完整的 VM 環境](ansible-create-complete-vm.md)。


## <a name="prerequisites"></a>必要條件
若要使用 Ansible 管理 Azure 資源，您需要下列各項：

- 在您的主機系統上安裝 Ansible 和 Azure Python SDK 模組。
    - 在 [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts)、[CentOS 7.3](ansible-install-configure.md#centos-73) 和 [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2) 上安裝 Ansible
- Azure 認證，並設定 Ansible 使用這些認證。
    - [建立 Azure 認證和設定 Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 
    - 如果您需要升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 您也可以在瀏覽器中使用 [Cloud Shell](/azure/cloud-shell/quickstart)。


## <a name="create-supporting-azure-resources"></a>建立支援的 Azure 資源
在此範例中，我們會建立一個 Runbook 來將 VM 部署到現有的基礎結構中。 首先，使用 [az group create](/cli/azure/vm#create) 建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#create) 為您的 VM 建立虛擬網路。 下列範例會建立名為 *myVnet* 的虛擬網路和名為 *mySubnet* 的子網路：

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>建立及執行 Ansible 腳本
建立名為 **azure_create_vm.yml** 的 Ansible 腳本，然後貼上下列內容。 此範例會建立單一 VM 並設定 SSH 認證。 在 *key_data* 配對中輸入您自己的公開金鑰資料，如下所示：

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

若要使用 Ansible 建立 VM，請如下所示執行腳本：

```bash
ansible-playbook azure_create_vm.yml
```

輸出看起來會類似下列範例，顯示已成功建立 VM：

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>後續步驟
此範例會在現有的資源群組中建立 VM，並已部署虛擬網路。 如需如何使用 Ansible 建立支援資源 (例如虛擬網路和網路安全性群組規則) 的更詳細範例，請參閱[使用 Ansible 建立完整的 VM 環境](ansible-create-complete-vm.md)。
