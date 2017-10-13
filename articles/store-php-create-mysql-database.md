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
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>在 Azure 中建立 MySQL 資料庫並連接到此資料庫
本教學課桯將示範如何在 [Azure 入口網站](https://portal.azure.com)中建立 MySQL 資料庫 (提供者是 [ClearDB](http://www.cleardb.com/))，以及如何從在 [Azure App Service](app-service/app-service-web-overview.md) 中執行的 PHP Web 應用程式連接到此資料庫。

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
   * **定價層**︰按一下 定價層，然後選取定價選項 (水星 層是免費的)，然後按一下選取。
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

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱 [PHP 開發人員中心](/develop/php/)。
