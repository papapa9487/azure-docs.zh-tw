---
title: "在 Azure 中建立 MySQL 資料庫並連接到此資料庫"
description: "了解如何使用 Azure 入口網站來建立 MySQL 資料庫，然後從 Azure 中的 PHP Web 應用程式連接到該資料庫。"
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 017e08b7e1e37c0bcb95ae0d9d702471bb8bc6bc
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>在 Azure 中建立 MySQL 資料庫並連接到此資料庫
本教學課桯將示範如何在 [Azure 入口網站](https://portal.azure.com)中建立 MySQL 資料庫 (提供者是 [ClearDB](http://www.cleardb.com/))，以及如何從在 [Azure App Service](app-service/app-service-value-prop-what-is.md) 中執行的 PHP Web 應用程式連接到此資料庫。

> [!NOTE]
> 您也可以建立 MySQL 資料庫做為 <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">Marketplace 應用程式範本</a>的一部分。
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>在 Azure 入口網站中建立 MySQL 資料庫
若要在 Azure 入口網站中建立 MySQL 資料庫，請執行下列操作：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左側功能表中，按一下 [新增] > [資料 + 儲存體] > [MySQL 資料庫]。

    ![在 Azure 中建立 MySQL 資料庫 - 開始](./media/store-php-create-mysql-database/create-db-1-start.png)
3. 在新增 MySQL 資料庫 [刀鋒視窗](azure-portal-overview.md) 中，依下列方式設定新的 MySQL 資料庫 (刀鋒視窗︰以水平方式開啟的入口網站頁面)：

   * **資料庫名稱**︰輸入可唯一識別的名稱
   * **訂用帳戶**：選擇要使用的訂用帳戶
   * **資料庫類型**︰針對低成本或免費層，請選取 [共用]，或選取 [專用] 以取得專用資源。
   * **資源群組**：將 MySQL 資料庫新增到現有的 [資源群組](azure-resource-manager/resource-group-overview.md) ，或將它放在新的資源群組中。 相同群組中的資源可以方便一起管理。
   * **位置**：選擇靠近您的位置。 當新增到現有的資源群組時，您會被鎖定到該資源群組的位置。
   * **定價層**︰按一下 [定價層]，然後選取定價選項 ([水星] 層是免費的)，然後按一下 [選取]。
   * **法律條款**︰按一下 [法律條款]、檢閱購買詳細資料，然後按一下 [購買]。
   * **釘選到儀表板**︰如果您想要直接從儀表板存取，請選取此選項。 如果您還不熟悉入口網站瀏覽方式，這會特別有用。

     下列螢幕擷取畫面只是一個說明如何設定 MySQL 資料庫的範例。  
     ![在 Azure 中建立 MySQL 資料庫 - 設定](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. 完成設定之後，按一下 [建立] 。

    ![在 Azure 中建立 MySQL 資料庫 - 建立](./media/store-php-create-mysql-database/create-db-3-create.png)

    您會看到一則告訴您已開始部署的快顯通知。

    ![在 Azure 中建立 MySQL 資料庫 - 進行中](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    部署成功之後，您會看到另一則快顯通知。 入口網站也會自動開啟您的 MySQL 資料庫刀鋒視窗。

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>連接到您的 MySQL 資料庫
若要查看新 MySQL 資料庫的連接資訊，請在 Web 應用程式的刀鋒視窗中按一下 [屬性]  。

![在 Azure 中建立 MySQL 資料庫 - MySQL 資料庫刀鋒視窗](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

您現在已可以在任何 Web 應用程式中使用該連接資訊。 [這裡](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)提供範例，示範如何使用來自簡單 PHP 應用程式的連接資訊。

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>連接 Laravel Web 應用程式 (來自開始使用 PHP 教學課程)
假設您剛完成[建立、設定 PHP Web 應用程式並部署到 Azure](app-service-web/app-service-web-php-get-started.md) 教學課程，並且擁有一個在 Azure 中執行的 [Laravel](https://www.laravel.com/) Web 應用程式。 您可以將資料庫功能輕鬆新增到您的 Laravel 應用程式。 只要依照下列步驟操作即可：

> [!NOTE]
> 下列步驟是假設您已完成 [建立、設定 PHP Web 應用程式並部署到 Azure](app-service-web/app-service-web-php-get-started.md)教學課程。
>
>

1. 將您本機開發環境中的 Laravel 應用程式設定成指向 MySQL 資料庫。 若要這樣做，請從 Laravel 應用程式的根目錄開啟 `.env` ，然後設定 MySQL 資料庫選項。

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

   > [!NOTE]
   > 在 [屬性] 刀鋒視窗中，MySQL 資料庫的名稱可能是也可能不是 [資料庫名稱] 欄位中顯示的名稱。 最好是檢查 [連接字串]  欄位中的 Database 參數。    
   >
   > ![在 Azure 中建立 MySQL 資料庫 - 進行中](./media/store-php-create-mysql-database/connect-db-1-database-name.png)
   >
   >
2. 確認您現在是否具備 MySQL 存取權的最快方式是使用 [Laravel 的預設驗證架構](https://laravel.com/docs/5.2/authentication#authentication-quickstart)。
   請在命令列終端機中，從您 Laravel 應用程式的根目錄執行下列命令︰

         php artisan migrate
         php artisan make:auth

    第一個命令會根據 `database/migrations` 目錄中預先定義的移轉，在 Azure 中建立資料表，第二個命令則會針對使用者註冊和驗證，建立基本檢視和路由的架構。
3. 現在，執行開發伺服器：

        php artisan serve
4. 在瀏覽器中，瀏覽至 http://localhost:8000 ，然後依照所示的方式註冊新使用者︰

    ![連接到 Azure 中的 MySQL 資料庫 - 註冊使用者](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    依照 UI 提示來完成註冊。 完成註冊之後，系統會您將登入。

    ![連接到 Azure 中的 MySQL 資料庫 - 註冊使用者](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    您現在已在 Azure 中針對 MySQL 資料庫開發您的應用程式。
5. 現在，您只需要將您的 `.env` 設定複寫到 Azure Web 應用程式即可。 執行下列 Azure CLI 命令：

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

6. 接著，認可先前執行 `php artisan make:auth`時所做的本機變更並推送到 Azure。

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master
7. 瀏覽至該 Azure Web 應用程式。

        azure site browse
8. 使用您稍早建立的使用者認證來登入。

    ![連接到 Azure 中的 MySQL 資料庫 - 瀏覽至 Azure Web 應用程式](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    登入之後，您應該會看到友善的登入後畫面。

    ![連接到 Azure 中的 MySQL 資料庫 - 登入](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    恭喜您，您在 Azure 中的 PHP Web 應用程式現在已在從 MySQL 資料庫存取資料。

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱 [PHP 開發人員中心](/develop/php/)。

