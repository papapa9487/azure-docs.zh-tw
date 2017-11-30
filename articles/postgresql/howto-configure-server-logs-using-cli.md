---
title: "使用 Azure CLI 設定和存取 PostgreSQL 的伺服器記錄 | Microsoft Docs"
description: "本文描述如何使用 Azure CLI 命令列，在適用於 PostgreSQL 的 Azure 資料庫中設定和存取伺服器記錄。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: d18ec44ecede44829b488ac9864bbfae2c62883a
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>使用 Azure CLI 設定和存取伺服器記錄
您可以使用命令列介面 (Azure CLI) 來下載 PostgreSQL 伺服器錯誤記錄。 不過，不支援存取交易記錄。 

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-azure-cli.md)
- 安裝 [Azure CLI 2.0](/cli/azure/install-azure-cli) 命令列公用程式，或在瀏覽器中使用 Azure Cloud Shell。

## <a name="configure-logging-for-azure-database-for-postgresql"></a>設定「適用於 PostgreSQL 的 Azure 資料庫」的記錄
您可以設定伺服器來存取查詢記錄和錯誤記錄。 錯誤記錄可包含自動清空、連線和檢查點資訊。
1. 開啟記錄功能
2. 更新 log\_statement 和 log\_min\_duration\_statement 來啟用查詢記錄
3. 更新保留期限

如需詳細資訊，請參閱[自訂伺服器設定參數](howto-configure-server-parameters-using-cli.md)。

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>列出適用於 PostgreSQL 伺服器的 Azure 資料庫之記錄
若要列出伺服器的可用記錄檔，請執行 [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) 命令。

您可以針對資源群組 **myresourcegroup** 下的伺服器 **mypgserver-20170401.postgres.database.azure.com**，列出伺服器的記錄檔，並輸出至名為 **log\_files\_list.txt** 的文字檔。
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mypgserver-20170401 > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>從伺服器將記錄下載至本機
[az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) 命令讓您可下載您伺服器適用的個別記錄檔。 

此範例會針對資源群組 **myresourcegroup** 下的伺服器 **mypgserver-20170401.postgres.database.azure.com**，將特定的記錄檔下載至您的本機環境。
```azurecli-interactive
az postgres server-logs download --name 20170414-mypgserver-20170401-postgresql.log --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="next-steps"></a>後續步驟
- 若要深入了解伺服器記錄，請參閱[適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄](concepts-server-logs.md)
- 如需伺服器參數的詳細資訊，請參閱[使用 Azure CLI 自訂伺服器設定參數](howto-configure-server-parameters-using-cli.md)
