---
title: "Azure CLI 指令碼 - 變更伺服器組態 | Microsoft Docs"
description: "這個範例 CLI 指令碼會列出所有可用的伺服器組態，並更新 innodb_lock_wait_timeout 的值。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 08/29/2017
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 558e7d4ee96a3424a777f9f57679da25f2306efd
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 列出及更新 Azure Database for MySQL 伺服器的組態
這個範例 CLI 指令碼會列出所有可用的組態參數，以及其對於 Azure Database for MySQL 伺服器的允許值，並將 innodb_lock_wait_timeout 設定為預設值以外的值。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼
在此範例指令碼中，請變更醒目提示的命令列以自訂系統管理員使用者名稱和密碼。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "列出及更新 Azure Database for MySQL 的組態。")]

## <a name="clean-up-deployment"></a>清除部署
在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。
[!code-azurecli-interactive[主要](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "刪除資源群組。")]

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| **命令** | **注意事項** |
|---|---|
| [az group create](/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az mysql server create](/cli/azure/mysql/server#create) | 建立主控資料庫的 MySQL 伺服器。 |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#list) | 列出 Azure Database for MySQL 伺服器的組態。 |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#set) | 更新 Azure Database for MySQL 伺服器的組態。 |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#show) | 顯示 Azure Database for MySQL 伺服器的組態。 |
| [az group delete](/cli/azure/group#delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
- 了解 Azure CLI 的詳細資訊：[Azure CLI 文件](/cli/azure/overview)。
- 嘗試其他指令碼：[「適用於 MySQL 的 Azure 資料庫」的 Azure CLI 範例](../sample-scripts-azure-cli.md)
- 如需有關伺服器參數的詳細資訊，請參閱[如何在 Azure Database for MySQL 中設定伺服器參數](../howto-server-parameters.md)。

