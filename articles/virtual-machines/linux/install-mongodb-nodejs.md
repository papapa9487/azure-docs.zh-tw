---
title: "使用 Azure CLI 1.0 在 Linux VM 上安裝 MongoDB | Microsoft Docs"
description: "了解如何使用 Resource Manager 部署模型在 Azure 中的 Linux 虛擬機器上安裝及設定 MongoDB。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: ff9e23de41245ea21ba6e9c3efe13ca13b0b0ae1
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>如何使用 Azure CLI 1.0 在 Linux VM 上安裝及設定 MongoDB
[MongoDB](http://www.mongodb.org) 是受歡迎的高效能開放原始碼 NoSQL 資料庫。 本文說明如何使用 Resource Manager 部署模型在 Azure 中的 Linux VM 上安裝及設定 MongoDB。 範例會詳細說明如何︰

* [手動安裝及設定基本 MongoDB 執行個體](#manually-install-and-configure-mongodb-on-a-vm)
* [使用 Resource Manager 範本建立基本 MongoDB 執行個體](#create-basic-mongodb-instance-on-centos-using-a-template)
* [使用 Resource Manager 範本建立複雜的 MongoDB 分區化叢集與複本集](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- Azure CLI 1.0 - 適用於傳統和資源管理部署模型 (本文) 的 CLI
- [Azure CLI 2.0](create-cli-complete-nodejs.md) - 適用於資源管理部署模型的新一代 CLI


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>在 VM 上手動安裝及設定 MongoDB
MongoDB [提供 Linux 散發版本的安裝指示](https://docs.mongodb.com/manual/administration/install-on-linux/)，包括 Red Hat / CentOS、SUSE、Ubuntu 和 Debian。 下列範例使用儲存在 ~/.ssh/id_rsa.pub 的 SSH 金鑰建立 CentOS VM。 回答儲存體帳戶名稱、DNS 名稱和系統管理員認證的提示︰

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

使用前一個 VM 建立步驟結尾所顯示的公用 IP 位址登入 VM︰

```bash
ssh azureuser@40.78.23.145
```

若要新增 MongoDB 的安裝來源，請建立 yum 存放庫檔案，如下所示︰

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

開啟 MongoDB 儲存機制檔案以進行編輯。 加入下列幾行：

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

使用 yum 安裝 MongoDB，如下所示：

```bash
sudo yum install -y mongodb-org
```

依預設，會在可防止您存取 MongoDB 的 CentOS 映像上強制採用 SELinux。 安裝原則管理工具及設定 SELinux，以允許 MongoDB 在其預設 TCP 連接埠 27017 上運作，如下所示。 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

啟動 MongoDB 服務，如下所示︰

```bash
sudo service mongod start
```

藉由使用本機 `mongo` 用戶端連線來確認 MongoDB 安裝︰

```bash
mongo
```

現在，新增一些資料然後進行搜尋，以測試 MongoDB 執行個體︰

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

如有需要，設定 MongoDB 以在系統重新開機期間自動啟動：

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>使用範本在 CentOS 上建立基本 MongoDB 執行個體
您可以使用下列來自 GitHub 的 Azure 快速入門範本，在單一 CentOS VM 上建立基本的 MongoDB 執行個體。 這個範本會使用 Linux 適用的自訂指令碼延伸模組將 `yum` 儲存機制新增至您新建立的 CentOS VM，然後安裝 MongoDB。

* [CentOS 上的基本 MongoDB 執行個體](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

下列範例會在 `eastus` 區域建立名為 `myResourceGroup` 的資源群組。 輸入您自己的值，如下所示︰

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI 會在建立部署後幾秒鐘內讓您回到提示，但是安裝和設定需要幾分鐘才能完成。 使用 `azure group deployment show myResourceGroup` 檢查部署狀態，據以輸入資源群組的名稱。 等到 ProvisioningState 顯示 Succeeded 後，再嘗試將 SSH 連線到 VM。

一旦部署完成時，SSH 連線到 VM。 使用 `azure vm show` 命令取得 VM 的 IP 位址，如下列範例所示：

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

在輸出的結尾附近，會顯示公用 IP 位址。 SSH 連線到您的 VM，包含您 VM 的 IP 位址︰

```bash
ssh azureuser@138.91.149.74
```

藉由使用本機 `mongo` 用戶端連線來確認 MongoDB 安裝，如下所示︰

```bash
mongo
```

現在，新增一些資料並進行搜尋來測試執行個體，如下所示︰

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>使用範本在 CentOS 上建立複雜的 MongoDB 分區化叢集
您可以使用下列來自 GitHub 的 Azure 快速入門範本，建立複雜的 MongoDB 分區化叢集。 此範本遵循 [MongoDB 分區化叢集最佳作法](https://docs.mongodb.com/manual/core/sharded-cluster-components/)提供備援和高可用性。 範本會建立兩個分區，其中每個複本集中有三個節點。 還會建立具有三個節點的組態伺服器複本集，加上兩個 mongos 路由器伺服器，以提供跨分區應用程式的一致性。

* [CentOS 上的 MongoDB 分區化叢集](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> 部署這個複雜的 MongoDB 分區化叢集需要超過 20 個 vCPU，這通常是每個訂用帳戶區域的預設 vCPU 計數。 開啟 Azure 支援要求，以增加 vCPU 計數。

以下範例會在 eastus 區域建立名為 myResourceGroup 的資源群組。 輸入您自己的值，如下所示︰

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI 會在建立部署後幾秒鐘內讓您回到提示，但是安裝和設定需要一個小時以上才能完成。 使用 `azure group deployment show myResourceGroup` 檢查部署狀態，據以調整資源群組的名稱。 等到 ProvisioningState 顯示 Succeeded 後，再連線到 VM。


## <a name="next-steps"></a>後續步驟
在這些範例中，您會從 VM 本機連線到 MongoDB 執行個體。 如果您想要從另一個 VM 或網路連線到 MongoDB 執行個體，請確定建立適當的[網路安全性群組規則](nsg-quickstart.md)。

如需關於建立範本的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。

Azure Resource Manager 範本會使用自訂指令碼延伸模組，在您的 VM 上下載並執行指令碼。 如需詳細資訊，請參閱[搭配 Linux 虛擬機器使用 Azure 自訂指令碼擴充功能](extensions-customscript.md)。

