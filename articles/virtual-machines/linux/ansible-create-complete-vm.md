---
title: "使用 Ansible 在 Azure 中建立完整的 Linux VM | Microsoft Docs"
description: "了解如何使用 Ansible 在 Azure 中建立及管理完整的 Linux 虛擬機器環境"
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
ms.openlocfilehash: f5dfecd1f5aafc1b7117433ef1bdb1805be2c57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>使用 Ansible 在 Azure 中建立完整的 Linux 虛擬機器環境
Ansible 可讓您將環境中的資源部署和設定自動化。 您可以使用 Ansible 在 Azure 中管理虛擬機器 (VM)，就像是任何其他資源一樣。 本文說明如何使用 Ansible 建立完整的 Linux 環境與支援的資源。 您也可以了解如何[使用 Ansible 建立基本 VM](ansible-create-vm.md)。


## <a name="prerequisites"></a>必要條件
若要使用 Ansible 管理 Azure 資源，您需要下列各項：

- 在您的主機系統上安裝 Ansible 和 Azure Python SDK 模組。
    - 在 [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts)、[CentOS 7.3](ansible-install-configure.md#centos-73) 和 [SLES 12.2 SP2](ansible-install-configure.md#sles-122-sp2) 上安裝 Ansible
- Azure 認證，並設定 Ansible 使用這些認證。
    - [建立 Azure 認證和設定 Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 
    - 如果您需要升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 您也可以在瀏覽器中使用 [Cloud Shell](/azure/cloud-shell/quickstart)。


## <a name="create-virtual-network"></a>建立虛擬網路
Ansible 腳本中的以下區段可在 *10.0.0.0/16* 位址空間中建立名為 *myVnet* 的虛擬網路：

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

為了新增子網路，下列區段會在 *myVnet* 虛擬網路中建立名為 *mySubnet* 的子網路：

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>建立公用 IP 位址
若要存取網際網路上的資源，請建立公用 IP 位址並指派給您的 VM。 Ansible 腳本中的以下區段會建立名為 *myPublicIP* 的公用 IP 位址：

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>建立網路安全性群組
網路安全性群組會控制進出 VM 的網路流量。 Ansible 腳本中的以下區段會建立名為 *myNetworkSecurityGroup* 的網路安全性群組，並定義規則以允許 TCP 通訊埠 22 上的 SSH 流量：

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>建立虛擬網路介面卡
虛擬網路介面卡 (NIC) 會將您的 VM 連線至指定的虛擬網路、公用 IP 位址，及網路安全性群組。 Ansible 腳本中的下列區段會建立名為 *myNIC* 的虛擬 NIC，以連線至您所建立的虛擬網路資源：

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Create virtual machine
最後一個步驟是建立 VM，然後使用建立的所有資源。 Ansible 腳本中的以下區段會建立名為 *myVM* 的 VM，並附加名為 *myNIC* 的虛擬 NIC。 在 *key_data* 配對中輸入您自己的公開金鑰資料，如下所示：

```yaml
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
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>完整 Ansible 腳本
若要這些區段全部整合在一起，請建立名為 *azure_create_vm.yml* 的 Ansible 腳本，然後貼上下列內容：

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
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
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible 需要一個資源群組，以將所有的資源部署至該群組。 使用 [az group create](/cli/azure/vm#create) 來建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli
az group create --name myResourceGroup --location eastus
```

若要使用 Ansible 建立完整的 VM 環境，請如下所示執行腳本：

```bash
ansible-playbook azure_create_complete_vm.yml
```

輸出看起來會類似下列範例，顯示已成功建立 VM：

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>後續步驟
此範例會建立包含必要虛擬網路資源的完整 VM 環境。 如需使用預設選項在現有網路資源中建立 VM 的更直接範例，請參閱[建立 VM](ansible-create-vm.md)。