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
ms.date: 11/28/2017
ms.openlocfilehash: 7fc4f8521afa41f21cda6576459a0794bef9ad3b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>使用 Azure 入口網站建立 Azure 資料庫移轉服務的執行個體
在本快速入門中，使用 Azure 入口網站建立 Azure 資料庫移轉服務的執行個體。  建立服務之後，您可以使用它來將資料從內部部署 SQL Server 移轉到 Azure SQL Database。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站
開啟 Web 瀏覽器並瀏覽至 [Microsoft Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。

## <a name="register-the-resource-provider"></a>註冊資源提供者
您必須在建立第一個資料庫移轉服務執行個體之前，註冊 Microsoft.DataMigration 資源提供者。

1. 在 Azure 入口網站中，選取 [所有服務]，然後選取 [訂用帳戶]。

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。

3. 搜尋移轉，然後在 Microsoft.DataMigration 的右邊，選取 [註冊]。

![註冊資源提供者](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>建立服務執行個體
1. 按一下 [+ 建立資源] 以建立 Azure 資料庫移轉服務執行個體，此執行個體目前為預覽狀態。

2. 搜尋適合「移轉」的市集，選取 [Azure 資料庫移轉服務]，並在 **Azure 資料庫移轉服務 (預覽)** 畫面中，按一下 [建立]。

3. 在**資料庫移轉服務**畫面上： 

    - 選擇易記又獨特的**服務名稱**，以便識別您的 Azure 資料庫移轉服務執行個體。
    - 選取您要建立執行個體的 Azure **訂用帳戶**。
    - 使用唯一的名稱建立新的**網路**。
    - 選擇最接近您的來源或目標伺服器的**位置**。
    - 針對**定價層**選取 [基本：1 個 vCore]。

    ![建立移轉服務](media/quickstart-create-data-migration-service-portal/dms-create-service.png)
4. 選取 [ **建立**]。

在幾分鐘之後，您的 Azure 資料庫移轉服務執行個體就會建立且可供使用。 您會看到如下圖所示的資料庫移轉服務：

![已建立移轉服務](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>清除資源
您可以刪除 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)，清除您在快速入門中建立的資源。  若要刪除資源群組，請瀏覽至您建立的 Azure 資料庫移轉服務執行個體。 選取**資源群組**名稱，然後選取 [刪除資源群組]。  這個動作會刪除資源群組中的所有資產，以及群組本身。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [將內部部署 SQL Server 移轉至 Azure SQL Database](tutorial-sql-server-to-azure-sql.md)