---
title: "使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則 | Microsoft Docs"
description: "本文說明如何使用 Azure CLI 命令列，建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 7d51932cb7c0b5b959e3fcf38f651933b619cbda
ms.contentlocale: zh-tw
ms.lasthandoff: 06/16/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則
伺服器等級防火牆規則可讓系統管理員從特定的 IP 位址或 IP 位址範圍，管理和存取適用於 PostgreSQL 的 Azure 資料庫伺服器。 透過方便的 Azure CLI 命令，您可以建立、更新、刪除、列出及顯示防火牆規則，以管理您的伺服器。 如需「適用於 PostgreSQL 的 Azure 資料庫」防火牆的概觀，請參閱[適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則](concepts-firewall-rules.md)

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [「適用於 PostgreSQL 的 Azure 資料庫」伺服器和資料庫](quickstart-create-server-database-azure-cli.md)
- 安裝 [Azure CLI 2.0](/cli/azure/install-azure-cli) 命令列公用程式，或在瀏覽器中使用 Azure Cloud Shell。

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>設定「適用於 PostgreSQL 的 Azure 資料庫」的防火牆規則
[az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) 命令可用於設定防火牆規則。

## <a name="list-firewall-rules"></a>列出防火牆規則 
若要列出伺服器上現有的伺服器防火牆規則，請執行 [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#list) 命令。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
輸出會列出規則 (若有的話)，預設為 JSON 格式。 您可以使用參數 `--output table`，以更容易閱讀的資料表格式呈現輸出。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>建立防火牆規則
若要在伺服器上建立新的防火牆規則，請執行 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) 命令。 

此範例允許所有 IP 位址範圍存取伺服器 **mypgserver-20170401.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
若要允許單一 IP 位址進行存取，請提供相同的位址作為起始 IP 和結束 IP，如這個範例所示。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "AllowSingleIpAddress" --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
成功時，命令輸出會列出您已建立之防火牆規則的詳細資料，預設為 JSON 格式。 如果失敗，輸出就會改為顯示錯誤訊息文字。

## <a name="update-firewall-rule"></a>更新防火牆規則 
請使用 [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#update) 命令更新伺服器上現有的防火牆規則。 提供現有防火牆規則的名稱作為輸入，以及要更新的起始 IP 和結束 IP 屬性。
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
成功時，命令輸出會列出您已更新之防火牆規則的詳細資料，預設為 JSON 格式。 如果失敗，輸出就會改為顯示錯誤訊息文字。
> [!NOTE]
> 如果防火牆規則不存在，更新命令則會相關規則。

## <a name="show-firewall-rule-details"></a>顯示防火牆規則詳細資料
您也可以執行 [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#show) 命令，以顯示伺服器現有的防火牆規則詳細資料。
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
成功時，命令輸出會列出您已指定之防火牆規則的詳細資料，預設為 JSON 格式。 如果失敗，輸出就會改為顯示錯誤訊息文字。

## <a name="delete-firewall-rule"></a>刪除防火牆規則
若要從伺服器撤銷 IP 範圍的存取，請執行 [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#delete) 命令以刪除現有防火牆規則。 提供現有防火牆規則的名稱。
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
成功時，沒有任何輸出。 發生錯誤時，就會傳回錯誤訊息文字。

## <a name="next-steps"></a>後續步驟
- 同樣地，您可以透過網頁瀏覽器，[使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-portal.md)
- 進一步了解[適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則](concepts-firewall-rules.md)
- 如需連線至「適用於 PostgreSQL 的 Azure 資料庫」伺服器的說明，請參閱[「適用於 PostgreSQL 的 Azure 資料庫」的連線庫](concepts-connection-libraries.md)

