---
title: "快速入門：建立 Azure Database for MySQL 伺服器 - Azuer CLI | Microsoft Docs"
description: "本快速入門說明如何使用 Azure CLI 在 Azure 資源群組中建立 Azure Database for MySQL 伺服器。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: hero-article
ms.date: 06/13/2017
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 3d66efa6935150c665a3f568e60c35ddbd70e8be
ms.contentlocale: zh-tw
ms.lasthandoff: 09/09/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 建立 Azure Database for MySQL 伺服器
本快速入門說明如何使用 Azure CLI，在大約 5 分鐘內於 Azure 資源群組中建立 Azure Database for MySQL 伺服器。 Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

如果您有多個訂用帳戶，請選擇資源所在或作為計費對象的適當訂用帳戶。 使用 [az account set](/cli/azure/account#set) 命令來選取您帳戶底下的特定訂用帳戶 ID。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>建立資源群組
使用 [az group create](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) 命令建立 [Azure 資源群組](https://docs.microsoft.com/cli/azure/group#az_group_create)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。

下列範例會在 `westus` 位置建立名為 `myresourcegroup` 的資源群組。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>建立適用於 MySQL 的 Azure 資料庫伺服器
使用 **az mysql server create** 命令建立 Azure Database for MySQL 伺服器。 一部伺服器可以管理多個資料庫。 一般而言，每個專案或每個使用者會使用個別的資料庫。

下列範例會在資源群組 `myresourcegroup` 的 `westus` 中建立名稱為 `myserver4demo` 的「適用於 MySQL 的 Azure 資料庫」伺服器。 此伺服器具有一個名為 `myadmin` 且密碼為 `Password01!` 的系統管理員登入。 建立的伺服器為 [基本] 效能層級，並有 **50** 個計算單位在伺服器中的所有資料庫之間共用。 您可以視應用程式需求而定，相應增加或減少計算和儲存體規模。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name myserver4demo --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>設定防火牆規則
使用 **az mysql server firewall-rule create** 命令建立 Azure Database for MySQL 伺服器層級的防火牆規則。 伺服器層級的防火牆規則允許外部應用程式 (例如 **mysql.exe** 命令列工具或 MySQL Workbench) 穿過 Azure MySQL 服務防火牆連線到您的伺服器。 

下列範例會建立適用於預先定義之位址範圍的防火牆規則，在此範例中，這是整個可能的 IP 位址範圍。

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server myserver4demo --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>進行 SSL 設定
預設會強制執行您的伺服器與用戶端應用程式之間的 SSL 連線。  這可藉由加密透過網際網路的資料流，確保「移動中」資料的安全性。  為了讓本快速入門更容易進行，我們停用了您伺服器的 SSL 連線。  不建議對生產伺服器這麼做。  如需詳細資訊，請參閱[在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫](./howto-configure-ssl.md)。

下列範例會停用在 MySQL 伺服器上強制執行 SSL。
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name myserver4demo -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>取得連線資訊

若要連線到您的伺服器，您必須提供主機資訊和存取認證。

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name myserver4demo
```

結果會採用 JSON 格式。 請記下 **fullyQualifiedDomainName** 和 **administratorLogin**。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "myserver4demo.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/myserver4demo",
  "location": "westus",
  "name": "myserver4demo",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>使用 mysql.exe 命令列工具連線到伺服器
使用 **mysql.exe** 命令列工具連線到伺服器。 您可以從[這裡](https://dev.mysql.com/downloads/)下載 MySQL，再將它安裝於電腦上。 相反地，您也可以按一下程式碼範例上的 [試用] 按鈕，或是 Azure 入口網站右上角工具列的 `>_` 按鈕，並啟動 **Azure Cloud Shell**。

輸入下一個命令： 

1. 使用 **mysql** 命令列工具連線到伺服器︰
```azurecli-interactive
 mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. 檢視伺服器狀態：
```sql
 mysql> status
```
如果一切順利，命令列工具應輸出下列文字︰

```dos
C:\Users\>mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             myserver4demo.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> 如需其他命令，請參閱 [MySQL 5.7 參考手冊 - 第 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)。

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>使用 MySQL Workbench GUI 工具連線到伺服器
1.  在您的用戶端電腦上啟動 MySQL Workbench 應用程式。 您可以從[這裡](https://dev.mysql.com/downloads/workbench/)下載並安裝 MySQL Workbench。

2.  在 [設定新連線] 對話方塊的 [參數] 索引標籤上輸入下列資訊︰

   ![設定新連線](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **設定** | **建議的值** | **說明** |
|---|---|---|
|   連線名稱 | 我的連線 | 指定此連線的標籤 (這可以是任何項目) |
| 連線方式 | 選擇標準 (TCP/IP) | 使用 TCP/IP 通訊協定來連線到 MySQL> 的 Azure 資料庫 |
| 主機名稱 | myserver4demo.mysql.database.azure.com | 您先前記下的伺服器名稱。 |
| 連接埠 | 3306 | 會使用 MySQL 的預設連接埠。 |
| 使用者名稱 | myadmin@myserver4demo | 先前記下的伺服器管理員登入。 |
| 密碼 | **** | 使用您稍早設定的管理帳戶密碼。 |

按一下 [測試連線] 以測試所有參數是否都已設定正確。
您現在可以按一下連線，以成功連線到伺服器。

## <a name="clean-up-resources"></a>清除資源
如果您不需要這些資源來進行其他快速入門/教學課程，可以執行下列命令加以刪除︰ 

```azurecli-interactive
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 設計 MySQL 資料庫](./tutorial-design-database-using-cli.md)

