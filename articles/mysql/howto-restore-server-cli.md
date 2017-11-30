---
title: "如何在適用於 MySQL 的 Azure 資料庫中備份及還原伺服器 | Microsoft Docs"
description: "了解如何使用 Azure CLI，在適用於 MySQL 的 Azure 資料庫中備份和還原伺服器。"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: 44b3c68b8df4006d3fe087e5ad4118d7616d3d9a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-cli"></a>如何使用 Azure CLI，在適用於 MySQL 的 Azure 資料庫中備份和還原伺服器

使用適用於 MySQL 的 Azure 資料庫將伺服器資料庫還原到 7 至 35 天前的日期。

## <a name="prerequisites"></a>必要條件
若要完成本操作說明指南，您需要：
- [適用於 MySQL 的 Azure 資料庫伺服器和資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 如果您在本機安裝和使用 Azure CLI，本操作說明指南會要求您使用 Azure CLI 2.0 版或更新版本。 若要確認版本，請在 Azure CLI 命令提示字元中輸入 `az --version`。 若要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="backup-happens-automatically"></a>備份會自動進行
當您使用適用於 MySQL 的 Azure 資料庫時，資料庫服務每隔 5 分鐘會自動備份一次服務。 

基本層的備份保留 7 天。 標準層的備份保留 35 天。 如需詳細資訊，請參閱[適用於 MySQL 的 Azure 資料庫定價層](concepts-service-tiers.md)。

透過這個自動備份功能，您可以將伺服器和其資料庫還原至某個較早的日期或時間點。

## <a name="restore-a-database-to-a-previous-point-in-time-by-using-the-azure-cli"></a>使用 Azure CLI 將資料庫還原到過去的時間點
使用適用於 MySQL 的 Azure 資料庫將伺服器還原至過去的時間點。 還原的資料會複製到新的伺服器，而現有的伺服器則保持原狀。 例如，如果資料表在今天中午意外卸除，您可以還原到中午之前的時間。 然後，您可從伺服器的還原複本擷取遺失的資料表和資料。 

若要還原伺服器，請使用 Azure CLI [az mysql server restore](/cli/azure/mysql/server#az_mysql_server_restore) 命令。

### <a name="run-the-restore-command"></a>執行 restore 命令

若要還原伺服器，請在 Azure CLI 命令提示字元中輸入下列命令：

```azurecli-interactive
az mysql server restore --resource-group myResourceGroup --name myserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server myserver4demo
```

`az mysql server restore` 命令需要下列參數：
| 設定 | 建議的值 | 說明  |
| --- | --- | --- |
| resource-group | myResourceGroup |  來源伺服器所在的資源群組。  |
| 名稱 | myserver-restored | 還原命令所建立之新伺服器的名稱。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 選取要還原的時間點。 這個日期和時間必須在來源伺服器的備份保留期限內。 請使用 ISO8601 日期和時間格式。 例如，您可以使用自己的當地時區，例如 `2017-04-13T05:59:00-08:00`。 您也可以使用 UTC Zulu 格式，例如 `2017-04-13T13:59:00Z`。 |
| source-server | myserver4demo | 要進行還原的來源伺服器之名稱或識別碼。 |

當您將伺服器還原到之前的時間點時，會建立新的伺服器。 指定時間點的原始伺服器及其資料庫會複製到新的伺服器。

已還原伺服器的位置與定價層值與原始伺服器相同。 

`az mysql server restore` 命令是同步的。 還原伺服器之後，您可以再次使用它以不同的時間點重複此程序。 

完成還原程序後，找出新的伺服器，確認資料如預期般還原。

## <a name="next-steps"></a>後續步驟
[適用於 MySQL 的 Azure 資料庫的連線庫](concepts-connection-libraries.md)
