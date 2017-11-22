---
title: "使用 Azure 入口網站建立 Azure 資料庫移轉服務執行個體 | Microsoft Docs"
description: "使用 Azure 入口網站建立 Azure 資料庫移轉服務的執行個體"
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/08/2017
ms.openlocfilehash: 4213a0ae73c38dcad403140e3d45e28f2d338020
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-database-migration-service-instance-using-the-azure-portal"></a>使用 Azure 入口網站建立資料庫移轉服務執行個體
在這個快速入門中，使用 Azure 入口網站建立 Azure 資料庫移轉服務的執行個體。  建立服務之後，您可以使用它來將資料從內部部署 SQL Server 移轉到 Azure SQL Database。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站
開啟 Web 瀏覽器並瀏覽至 [Microsoft Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。

## <a name="create-azure-database-migration-service"></a>建立 Azure 資料庫移轉服務
1. 按一下 **+** 以建立新服務。  資料庫移轉服務仍然在預覽狀態。  

1. 在市集中搜尋 "migration"，選取 [資料庫移轉服務 (預覽)]，然後按一下 [建立]。

    ![建立移轉服務](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - 選擇易記又獨特的**服務名稱**，以便識別您的 Azure 資料庫移轉服務執行個體。
    - 選取您要在其中建立資料庫移轉服務的 Azure **訂用帳戶**。
    - 使用唯一的名稱建立新的**網路**。
    - 選擇最接近您的來源或目標伺服器的**位置**。
    - 針對**定價層**選取 [基本：1 個 vCore]。

1. 按一下 [建立] 。

在幾分鐘之後，您的 Azure 資料庫移轉服務就會建立且可供使用。  您會看到如下圖所示的資料庫移轉服務。

![已建立移轉服務](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>清除資源
您可以藉由刪除 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)，清除您在快速入門中建立的資源。  若要刪除資源群組，瀏覽至您建立的資料庫移轉服務，按一下**資源群組**名稱，然後選取 [刪除資源群組]。  這個動作會刪除資源群組中的所有資產，以及群組本身。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [將內部部署 SQL Server 移轉至 Azure SQL DB](tutorial-sql-server-to-azure-sql.md)