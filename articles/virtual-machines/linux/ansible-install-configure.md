---
title: "安裝及設定 Ansible 以搭配 Azure 虛擬機器使用 | Microsoft Docs"
description: "了解如何安裝及設定 Ansible，以便管理 Ubuntu、CentOS 和 SLES 上的 Azure 資源"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 52b763274437961dccfc862c8a45fbd57ea9fc4e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017

---

# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>安裝及設定 Ansible 來管理 Azure 中的虛擬機器
本文詳細說明如何針對某些最常見的 Linux 發行版，安裝 Ansible 和必要的 Azure Python SDK 模組。 您可以配合特定的平台調整安裝的套件，來將 Ansible 安裝在其他發行版上。 為了以安全的方式建立 Azure 資源，您也將了解如何建立及定義 Ansible 所要使用的認證。 

如需其他平台的更多安裝選項和步驟，請參閱 [Ansible 安裝指南](https://docs.ansible.com/ansible/intro_installation.html)。


## <a name="install-ansible"></a>安裝 Ansible
首先，使用 [az group create](/cli/azure/group#create) 建立資源群組。 下列範例會在 *eastus* 位置建立名為 *myResourceGroupAnsible* 的資源群組：

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

現在建立 VM，並針對下列其中一個發行版安裝 Ansible：

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [CentOS 7.3](#centos-73)
- [SLES 12.2 SP2](#sles-122-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
使用 [az vm create](/cli/azure/vm#create) 建立 VM。 下列範例會建立名為 *myVMAnsible* 的 VM：

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用 VM 建立作業中的輸出所記錄的 `publicIpAddress` SSH 到您的 VM：

```bash
ssh azureuser@<publicIpAddress>
```

在您的 VM 上，安裝 Azure Python SDK 模組和 Ansible 所需的套件，如下所示：

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

現在繼續前往[建立 Azure 認證](#create-azure-credentials)。


### <a name="centos-73"></a>CentOS 7.3
使用 [az vm create](/cli/azure/vm#create) 建立 VM。 下列範例會建立名為 *myVMAnsible* 的 VM：

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用 VM 建立作業中的輸出所記錄的 `publicIpAddress` SSH 到您的 VM：

```bash
ssh azureuser@<publicIpAddress>
```

在您的 VM 上，安裝 Azure Python SDK 模組和 Ansible 所需的套件，如下所示：

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

現在繼續前往[建立 Azure 認證](#create-azure-credentials)。


### <a name="sles-122-sp2"></a>SLES 12.2 SP2
使用 [az vm create](/cli/azure/vm#create) 建立 VM。 下列範例會建立名為 *myVMAnsible* 的 VM：

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用 VM 建立作業中的輸出所記錄的 `publicIpAddress` SSH 到您的 VM：

```bash
ssh azureuser@<publicIpAddress>
```

在您的 VM 上，安裝 Azure Python SDK 模組和 Ansible 所需的套件，如下所示：

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 python-devel \
    libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

現在繼續前往[建立 Azure 認證](#create-azure-credentials)。


## <a name="create-azure-credentials"></a>建立 Azure 認證
Ansible 會使用使用者名稱與密碼或服務主體與 Azure 進行通訊。 Azure 服務主體是安全性識別，可供您與應用程式、服務及諸如 Ansible 等自動化工具搭配使用。 您可以控制和定義對於服務主體可以在 Azure 中執行哪些作業的權限。 為了提高只提供使用者名稱和密碼的安全性，此範例會建立基本的服務主體。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 建立服務主體，並將 Ansible 所需的認證輸出：

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

上述命令的輸出範例如下所示：

```json
[
  "eec5624a-90f8-4386-8a87-02730b5410d5",
  "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "72f988bf-86f1-41af-91ab-2d7cd011db47"
]
```

若要向 Azure 驗證，您也需要使用 [az account show](/cli/azure/account#show) 取得 Azure 訂用帳戶識別碼：

```azurecli
az account show --query [id] --output tsv
```

您將在下一個步驟中使用這兩個命令的輸出。


## <a name="create-ansible-credentials-file"></a>建立 Ansible 認證檔案
若要提供認證給 Ansible，您可以定義環境變數或建立本機認證檔案。 如需如何定義 Ansible 認證的詳細資訊，請參閱 [Providing Credentials to Azure Modules](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules) (提供認證給 Azure 模組)。 

針對開發環境，在您的主機 VM 上建立 Ansible 的「認證」檔案，如下所示：

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

「認證」檔案本身結合了訂用帳戶識別碼與建立服務主體的輸出。 對於 *client_id*、*secret* 和 *tenant*，先前 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 命令的輸出必須是相同順序。 下列範例*認證*檔案顯示符合上述輸出的值。 輸入您自己的值，如下所示︰

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>使用 Ansible 環境變數
如果您想要使用 Ansible Tower 或 Jenkins 等工具，您可以如下所示定義環境變數。 這些變數結合了訂用帳戶識別碼與建立服務主體的輸出。 對於 *AZURE_CLIENT_ID*、*AZURE_SECRET* 和 *AZURE_TENANT*，先前 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 命令的輸出必須是相同順序。 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>後續步驟
您現在已安裝 Ansible 和必要的 Azure Python SDK 模組，並已定義 Ansible 所要使用的認證。 了解如何[使用 Ansible 建立 VM](ansible-create-vm.md)。 您也可以了解如何[使用 Ansible 建立完整的 Azure VM 和支援資源](ansible-create-complete-vm.md)。
