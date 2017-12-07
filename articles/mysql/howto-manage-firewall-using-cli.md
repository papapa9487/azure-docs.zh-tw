---
title: "使用 Azure CLI 建立和管理適用於 MySQL 的 Azure 資料庫防火牆規則 | Microsoft Docs"
description: "本文描述如何使用 Azure CLI 命令列，建立和管理適用於 MySQL 的 Azure 資料庫防火牆規則。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: 0adcf8fd21049ee75972352b2e7d3c56300e0f87
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>使用 Azure CLI 建立和管理適用於 MySQL 的 Azure 資料庫防火牆規則
伺服器層級的防火牆規則可讓系統管理員從指定的 IP 位址或 IP 位址範圍，管理適用於 MySQL 的 Azure 資料庫伺服器的存取。 透過方便的 Azure CLI 命令，您可以建立、更新、刪除、列出及顯示防火牆規則，以管理您的伺服器。 如需「適用於 MySQL 的 Azure 資料庫」防火牆的概觀，請參閱[適用於 MySQL 的 Azure 資料庫伺服器防火牆規則](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>必要條件
* [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。
* 安裝適用於 PostgreSQL 和 MySQL 服務的 Azure Python SDK。
* 安裝適用於 PostgreSQL 和 MySQL 服務的 Azure CLI 元件。
* 建立適用於 MySQL 伺服器的 Azure 資料庫。

## <a name="firewall-rule-commands"></a>防火牆規則命令：
**az mysql server firewall-rule** 命令是從 Azure CLI 中用來建立、刪除、列出、顯示及更新防火牆規則。

命令：
- **create**︰建立 Azure MySQL 伺服器防火牆規則。
- **delete**︰刪除 Azure MySQL 伺服器防火牆規則。
- **list**：列出 Azure MySQL 伺服器防火牆規則。
- **show**︰顯示 Azure MySQL 伺服器防火牆規則的詳細資料。
- **update**：更新 Azure MySQL 伺服器防火牆規則。

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>登入 Azure 並列出適用於 MySQL 的 Azure 資料庫伺服器
使用 **az login** 命令，安全地連接 Azure CLI 與 Azure 帳戶。

1. 從命令列中執行下列命令：
```azurecli
az login
```
此命令會輸出下一個步驟中要使用的代碼。

2. 使用網頁瀏覽器開啟 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 頁面並輸入代碼。

3. 出現提示時，使用您的 Azure 認證登入。

4. 當您的登入獲得授權之後，主控台就會列印訂用帳戶清單。 複製所需訂用帳戶的識別碼，以設定目前要使用的訂用帳戶。 使用 [az account set](/cli/azure/account#az_account_set) 命令。
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. 如果您不確定名稱，請列出您的訂用帳戶和資源群組的「適用於 MySQL 的 Azure 資料庫」伺服器。 使用 [az mysql server list](/cli/azure/mysql/server#az_mysql_server_list) 命令。

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   請注意清單中的名稱屬性，您需要指定要使用的 MySQL 伺服器。 如有需要，請確認該伺服器的詳細資料，並使用名稱屬性來確認名稱正確。 使用 [az mysql server show](/cli/azure/mysql/server#az_mysql_server_show) 命令。

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>在適用於 MySQL 的 Azure 資料庫伺服器上列出防火牆規則 
使用伺服器名稱和資源群組名稱，列出伺服器上現有的伺服器防火牆規則。 使用 [az mysql server firewall list](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_list) 命令。  請注意，伺服器名稱屬性是在 **--server** 參數中指定，而不是 **--name** 參數。 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
輸出會以 JSON 格式 (依預設) 列出規則 (若有的話)。 您可以使用 **--output table** 參數，以更容易閱讀的表格格式來輸出結果。
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>在適用於 MySQL 的 Azure 資料庫伺服器上建立防火牆規則
使用 Azure MySQL 伺服器名稱和資源群組名稱，在伺服器上建立新的防火牆規則。 使用 [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) 命令。 提供規則的名稱，以及規則的起始 IP 和結尾 IP (以提供 IP 位址範圍的存取)。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
若要允許存取單一 IP 位址，請提供相同的 IP 位址作為起始 IP 和結尾 IP，如這個範例所示。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
成功時，命令輸出會以 JSON 格式 (依預設) 列出您已建立之防火牆規則的詳細資料。 如果失敗，輸出就會改為顯示錯誤訊息文字。

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>在適用於 MySQL 的 Azure 資料庫伺服器上更新防火牆規則 
使用 Azure MySQL 伺服器名稱和資源群組名稱，在伺服器上更新現有的防火牆規則。 使用 [az mysql server firewall update](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_update) 命令。 提供現有防火牆規則的名稱作為輸入，以及要更新的起始 IP 和結尾 IP 屬性。
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
成功時，命令輸出會以 JSON 格式 (依預設) 列出您已更新之防火牆規則的詳細資料。 如果失敗，輸出就會改為顯示錯誤訊息文字。

> [!NOTE]
> 如果防火牆規則不存在，更新命令就會建立規則。

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>在適用於 MySQL 的 Azure 資料庫伺服器上顯示防火牆規則詳細資料
使用 Azure MySQL 伺服器名稱和資源群組名稱，顯示伺服器上現有的防火牆規則詳細資料。 使用 [az mysql server firewall show](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_show) 命令。 提供現有防火牆規則的名稱作為輸入。
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
成功時，命令輸出會以 JSON 格式 (依預設) 列出您已指定之防火牆規則的詳細資料。 如果失敗，輸出就會改為顯示錯誤訊息文字。

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>在適用於 MySQL 的 Azure 資料庫伺服器上刪除防火牆規則
使用 Azure MySQL 伺服器名稱和資源群組名稱，從伺服器上移除現有的防火牆規則。 使用 [az mysql server firewall delete](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_delete) 命令。 提供現有防火牆規則的名稱。
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
成功時，沒有任何輸出。 發生錯誤時，就會顯示錯誤訊息文字。

## <a name="next-steps"></a>後續步驟
- 進一步了解[適用於 MySQL 的 Azure 資料庫伺服器防火牆規則](./concepts-firewall-rules.md)。
- [使用 Azure 入口網站建立和管理適用於 MySQL 的 Azure 資料庫防火牆規則](./howto-manage-firewall-using-portal.md)。
