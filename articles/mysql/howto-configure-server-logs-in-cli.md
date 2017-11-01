---
title: "使用 Azure CLI 在適用於 MySQL 的 Azure 資料庫中存取伺服器記錄檔 | Microsoft Docs"
description: "本文說明如何使用 Azure CLI 命令列公用程式，在適用於 MySQL 的 Azure 資料庫中存取伺服器記錄。"
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 6ee2c2c6e6cff824d3167ea600a1ddc778ad011b
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>使用 Azure CLI 設定和存取伺服器記錄
您可以使用 Azure CLI (Azure 的命令列公用程式) 來下載適用於 MySQL 的 Azure 資料庫的伺服器記錄檔。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) 或在瀏覽器中使用 Azure Cloud Shell。

## <a name="configure-logging-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫的記錄
您可以設定伺服器以存取 MySQL 慢速查詢記錄檔。
1. 藉由將 **slow\_query\_log** 參數設為 ON 來開啟記錄功能。
2. 調整其他參數，例如 **long\_query\_time** 和 **log\_slow\_admin\_statements**。

請參閱[如何設定伺服器參數](howto-configure-server-parameters-using-cli.md)，以了解如何透過 Azure CLI 設定這些參數的值。

例如，下列 CLI 命令會開啟慢速查詢記錄檔、將長時間查詢的時間設定為 10 秒，並且會關閉慢速管理陳述式的記錄。 最後，它會列出設定選項供您檢閱。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>列出適用於 MySQL 的 Azure 資料庫伺服器之記錄
若要列出伺服器的可用記錄檔，請執行 [az mysql server-logs list](/cli/azure/mysql/server-logs#list) 命令。

您可以針對資源群組 **myresourcegroup** 下的伺服器 **myserver4demo.mysql.database.azure.com**，列出伺服器的記錄檔，並輸出至名為 **log\_files\_list.txt** 的文字檔。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>從伺服器下載記錄檔
[az mysql server-logs download](/cli/azure/mysql/server-logs#download) 命令讓您可下載您伺服器適用的個別記錄檔。 

此範例會針對資源群組 **myresourcegroup** 下的伺服器 **myserver4demo.mysql.database.azure.com**，將特定的記錄檔下載至您的本機環境。
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>後續步驟
- 深入了解[適用於 MySQL 的 Azure 資料庫中的伺服器記錄檔](concepts-server-logs.md)
