---
title: "設定 SSL 連線能力，以安全地連線到適用於 MySQL 的 Azure 資料庫 | Microsoft Docs"
description: "有關如何適當設定「適用於 MySQL 的 Azure 資料庫」及相關聯應用程式以適當使用 SSL 連線的指示"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 38e68712699b3e89a10c3d44d8ec313f531fcbdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫
適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將伺服器與應用程式之間的資料流加密，有助於抵禦「中間人」攻擊。

## <a name="step-1-obtain-ssl-certificate"></a>步驟 1：取得 SSL 憑證
從 [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 下載要透過 SSL 與 Azure Database for MySQL 伺服器通訊所需的憑證，並將該憑證檔儲存到本機磁碟機 (在此教學課程中，我們使用 c:\ssl)。
**針對 Microsoft Internet Explorer 和 Microsoft Edge：**在下載完成後，請將憑證重新命名為 BaltimoreCyberTrustRoot.crt.pem。

## <a name="step-2-bind-ssl"></a>步驟 2：繫結 SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>使用 MySQL Workbench 透過 SSL 連線至伺服器
設定使 MySQL Workbench 安全地透過 SSL 連線。 在 [設定新連線] 對話方塊上的 MySQL Workbench 中，瀏覽至 [SSL] 索引標籤。在 [SSL CA 檔案:] 欄位中輸入 **BaltimoreCyberTrustRoot.crt.pem** 的檔案位置。
![儲存自訂的圖格](./media/howto-configure-ssl/mysql-workbench-ssl.png)

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>使用 MySQL CLI 透過 SSL 連線至伺服器
使用 MySQL 命令列介面，執行下列命令：
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>步驟 3：強制在 Azure 中使用 SSL 連線 
### <a name="using-the-azure-portal"></a>使用 Azure 入口網站
使用 Azure 入口網站，瀏覽適用於 MySQL 的 Azure 資料庫伺服器，然後按一下連線安全性。 使用切換按鈕來啟用或停用 強制使用 SSL 連線 設定，然後按一下儲存。 Microsoft 建議一律啟用 [強制使用 SSL 連線] 設定，以增強安全性。
![啟用 ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
您可以在 Azure CLI 中分別使用 Enabled 或 Disabled 值，以啟用或停用 **ssl-enforcement** 參數。
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>步驟 4：確認 SSL 連線
執行 mysql **status** 命令，確認您已使用 SSL 連線至 MySQL 伺服器︰
```dos
mysql> status
```
藉由檢閱輸出確認連線已加密，顯示結果應類似：**SSL: Cipher in use is AES256-SHA** 

## <a name="sample-code"></a>範例程式碼
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python"></a>Python
```
try:
conn = mysql.connector.connect(user='myadmin@myserver4demo',password='yourpassword',database='quickstartdb',host='myserver4demo.mysql.database.azure.com',ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
 print(err)
```

## <a name="next-steps"></a>後續步驟
檢閱[「適用於 MySQL 的 Azure 資料庫」的連線庫](concepts-connection-libraries.md)後面的各種應用程式連線能力選項
