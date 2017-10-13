---
title: "設計您第一個適用於 MySQL 資料庫的 Azure 資料庫 - Azure 入口網站 | Microsoft Docs"
description: "本教學課程說明如何使用「Azure 入口網站」來建立和管理「適用於 MySQL 的 Azure 資料庫」伺服器。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.topic: tutorial
ms.date: 06/06/2017
ms.custom: mvc
ms.openlocfilehash: 92ae809646cb956b0b7cb71cf2a21610bb2d610f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>設計您第一個適用於 MySQL 資料庫的 Azure 資料庫
「適用於 MySQL 的 Azure 資料庫」是一個受管理的服務，可讓您在雲端執行、管理及調整高可用性 MySQL 資料庫。 使用 Azure 入口網站，您可以輕鬆管理伺服器和設計資料庫。

在本教學課程中，您將使用 Azure 入口網站來學習如何：

> [!div class="checklist"]
> * 建立適用於 MySQL 的 Azure 資料庫
> * 設定伺服器防火牆
> * 使用 mysql 命令列工具來建立資料庫
> * 載入範例資料
> * 查詢資料
> * 更新資料
> * 還原資料

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
開啟您的慣用網頁瀏覽器，然後瀏覽至 [Microsoft Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。

## <a name="create-an-azure-database-for-mysql-server"></a>建立適用於 MySQL 的 Azure 資料庫伺服器
建立的「適用於 MySQL 的 Azure 資料庫」伺服器會有一組已定義的[計算和儲存體](./concepts-compute-unit-and-storage.md)資源。 伺服器會建立在 [Azure 資源群組](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)內。

1. 瀏覽至 [資料庫] > [Azure Database for MySQL]。 如果您在 [資料庫] 類別底下找不到「MySQL 伺服器」，請按一下 [查看全部] 以顯示所有可用的資料庫服務。 您也可以在搜尋方塊中輸入 [Azure Database for MySQL] 以快速找到此服務。
![2-1 瀏覽至 MySQL](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2. 按一下 Azure Database for MySQL 圖格，然後按一下建立。

在我們的範例中，於「適用於 MySQL 的 Azure 資料庫」表單中填入下列資訊：

| **設定** | **建議的值** | **欄位描述** |
|---|---|---|
| *伺服器名稱* | myserver4demo  | 伺服器名稱必須是全域唯一的。 |
| *訂用帳戶* | mysubscription | 從下拉式清單中選取訂用帳戶。 |
| *資源群組* | myresourcegroup | 建立資源群組或使用現有的資源群組。 |
| *伺服器管理員登入* | myadmin | 設定管理帳戶名稱。 |
| *密碼* |  | 設定強式管理帳戶密碼。 |
| *確認密碼* |  | 確認管理帳戶密碼。 |
| <bpt id="p1">*</bpt>Location<ept id="p1">*</ept> |  | 選取可用的區域。 |
| *版本* | 5.7 | 選擇最新版本。 |
| *設定效能* | 基本、50 個計算單位、50 GB  | 選擇 **[定價層]** 、 **[計算單位]** 、**[儲存體]** \(GB) ，然後按一下 **[確定]**。 |
| 釘選到儀表板 | 勾選 | 建議您核取此方塊，讓您在稍後能輕鬆地找到伺服器 |
然後按一下 [建立] 。 在一兩分鐘內，新的「適用於 MySQL 的 Azure 資料庫」伺服器就會在雲端執行。 您可以按一下工具列上的 [通知] 按鈕來監視部署程序。

## <a name="configure-firewall"></a>設定防火牆
「適用於 MySQL 的 Azure 資料庫」會受到防火牆保護。 依預設，伺服器與其內部資料庫的所有連線皆會遭拒。 第一次連線到 Azure Database for MySQL 之前，請設定防火牆來新增用戶端的公用網路 IP 位址 (或 IP 位址範圍)。

1. 按一下您新建立的伺服器，然後按一下連線安全性。
   ![3-1 連線安全性](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)
2. 您可以在這裡 [新增我的 IP]，或設定防火牆規則。 請記得在建立規則後按一下 [儲存]。
您現在可以使用 mysql 命令列工具或 MySQL Workbench GUI 工具來連線到伺服器。

> [!TIP]
> 「適用於 MySQL 的 Azure 資料庫」伺服器會透過連接埠 3306 進行通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 3306 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 1433，否則您無法連線至 Azure MySQL 伺服器。

## <a name="get-connection-information"></a>取得連線資訊
請從 Azure 入口網站取得 Azure Database for MySQL 伺服器的完整 [伺服器名稱] 和 [伺服器管理員登入名稱]。 您將使用 mysql 命令列工具搭配此完整伺服器名稱來連線到伺服器。 

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下左側功能表中的 [所有資源]，輸入名稱，然後搜尋您的 Azure Database for MySQL 伺服器。 選取伺服器名稱以檢視詳細資料。

2. 在 [設定] 標題之下，按一下 [屬性]。 記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。 您可以按一下每個欄位旁邊的 [複製] 按鈕，以複製到剪貼簿。
   ![4-2 伺服器屬性](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

在此範例中，伺服器名稱為 myserver4demo.mysql.database.azure.com，而伺服器管理員登入為 myadmin@myserver4demo。

## <a name="connect-to-the-server-using-mysql"></a>使用 mysql 來連線到伺服器
使用 [mysql 命令列工具](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)來建立對「適用於 MySQL 的 Azure 資料庫」伺服器的連線。 您可以從 Azure Cloud Shell 在瀏覽器中，或從自己的電腦使用本機安裝的 mysql 工具執行 mysql 命令列工具。 若要啟動 Azure Cloud Shell，請按一下本文中程式碼區塊的 `Try It` 按鈕，或請造訪 Azure 入口網站並按一下頂端右側工具列的 `>_` 圖示。 

輸入要連線的命令：
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

## <a name="create-a-blank-database"></a>建立空白資料庫
連線到伺服器之後，請建立一個要搭配運作的空白資料庫。
```sql
CREATE DATABASE mysampledb;
```

在提示字元，執行下列命令以將連線切換到這個新建立的資料庫：
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>在資料庫中建立資料表
既然您已知道如何連線到「適用於 MySQL 資料庫的 Azure 資料庫」，我們可以了解一下如何完成一些基本工作。

首先，我們可以建立資料表並在其中載入一些資料。 我們將建立一個儲存清查資訊的資料表。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>將資料載入到資料表
既然我們已有資料表，我們可以在其中插入一些資料。 在開啟的命令提示字元視窗，執行下列查詢以插入幾列資料。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

您現在已將兩列範例資料插入到先前建立的資料表。

## <a name="query-and-update-the-data-in-the-tables"></a>查詢並更新資料表中的資料
執行下列查詢，以從資料庫資料表中擷取資訊。
```sql
SELECT * FROM inventory;
```

您也可以更新資料表中的資料。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

當您擷取資料時，資料列會相應地更新。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>將資料庫還原至先前的時間點
假設您不小心刪除重要的資料庫資料表，而無法輕易地復原資料。 Azure Database for MySQL 可讓您將伺服器還原至某個時間點，並在新的伺服器中建立資料庫的複本。 您可以使用這個新的伺服器來復原已刪除的資料。 下列步驟會將範例伺服器還原到新增資料表之前的時間點。

1. 在 Azure 入口網站中，找出您的 Azure Database for MySQL。 在 [概觀] 頁面上，按一下工具列上的 [還原]。 [還原] 頁面隨即開啟。

   ![10-1 還原資料庫](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2. 在 [還原] 表單中填入必要資訊。
   
   ![10-2 還原表單](./media/tutorial-design-database-using-portal/10_2-restore-form.png)
   
   - **還原點**：在所列的時間範圍內，選取您想要還原的時間點。 務必將您的當地時區轉換成 UTC。
   - **還原到新伺服器**︰提供要作為還原目的地的新伺服器名稱。
   - **位置**︰此區域與來源伺服器相同，無法加以變更。
   - **定價層**︰此定價層與來源伺服器相同，無法加以變更。
   
3. 按一下 [確定]，將伺服器[還原到資料表刪除之前的時間點](./howto-restore-server-portal.md)。 還原伺服器可建立伺服器的新複本 (自您指定的時間點起)。 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您使用 Azure 入口網站來學習如何：

> [!div class="checklist"]
> * 建立適用於 MySQL 的 Azure 資料庫
> * 設定伺服器防火牆
> * 使用 mysql 命令列工具來建立資料庫
> * 載入範例資料
> * 查詢資料
> * 更新資料
> * 還原資料

> [!div class="nextstepaction"]
> [如何將應用程式連線至 Azure Database for MySQL](./howto-connection-string.md)
