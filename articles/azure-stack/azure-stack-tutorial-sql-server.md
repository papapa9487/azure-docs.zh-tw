---
title: "將 SQL 資料庫提供給您的 Azure Stack 使用者 | Microsoft Docs"
description: "此教學課程說明如何安裝 SQL Server 資源提供者，並建立供應項目以讓 Azure Stack 使用者建立 SQL 資料庫。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 7/03/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: f774888ba3921d0688feddac669ed1dca4667441
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>將 SQL 資料庫提供給您的 Azure Stack 使用者

身為 Azure Stack 雲端系統管理員，您可以建立供應項目以讓您的使用者 (租用戶) 建立 SQL 資料庫，以搭配其雲端原生應用程式、網站與工作負載使用。 透過將對這些自訂隨選雲端式資料庫的存取權提供給您的使用者，您可以節省其時間與資源。 若要設定，您將必須：

> [!div class="checklist"]
> * 部署 SQL Server 資源提供者
> * 建立優惠
> * 測試供應項目

## <a name="deploy-the-sql-server-resource-provider"></a>部署 SQL Server 資源提供者

部署程序於[在 Azure Stack 上使用 SQL 資料庫](azure-stack-sql-resource-provider-deploy.md)文章中詳細說明，而且由下列主要步驟組成：

1. [部署 SQL 資源提供者]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider)。
2. [驗證部署]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal)。
3. 透過連線到主控 SQL 伺服器來提供容量。

## <a name="create-an-offer"></a>建立優惠

1.  [設定配額](azure-stack-setting-quotas.md)並將它命名為 *SQLServerQuota*。 選取 [命名空間] 欄位的 [Microsoft.SQLAdapter]。
2.  [建立方案](azure-stack-create-plan.md)。 將它命名為 *TestSQLServerPlan*，選取 [Microsoft.SQLAdapter] 服務，並選取 [SQLServerQuota] 配額。

    > [!NOTE]
    > 若要讓使用者建立其他應用程式，方案中可能需要有其他服務。 例如，Azure Functions 要求方案必須包括 **Microsoft.Storage** 服務，而 Wordpress 則需要 **Microsoft.MySQLAdapter**。
    > 
    >

3.  [建立供應項目](azure-stack-create-offer.md)，將它命名為 **TestSQLServerOffer**，然後選取 [TestSQLServerPlan] 方案。

## <a name="test-the-offer"></a>測試供應項目

既然您已部署 SQL Server 資源提供者並建立供應項目，您能以使用者身分登入並訂閱該供應項目，然後建立資料庫。

### <a name="subscribe-to-the-offer"></a>訂閱該供應項目
1. 以租用戶身分登入 Azure Stack 入口網站 (https://portal.local.azurestack.external)。
2. 按一下 [取得訂用帳戶]，然後在 [顯示名稱] 下輸入 **TestSQLServerSubscription**。
3. 按一下 [選取服務] > [TestSQLServerOffer] > [建立]。
4. 按一下 [更多服務] > [訂用帳戶] > [TestSQLServerSubscription] > [資源提供者]。
5. 按一下 **Microsoft.SQLAdapter** 提供者旁的 [註冊]。

### <a name="create-a-sql-database"></a>建立 SQL 資料庫

1. 按一下 [+] > [資料 + 儲存體] > [SQL Database]。
2. 將欄位維持為預設值，或您可以使用這些範例：
    - **資料庫名稱**：SQLdb
    - **大小上限 (MB)**：100
    - **訂用帳戶**：TestSQLOffer
    - **資源群組**：SQL-RG
3. 按一下 登入設定，輸入資料庫認證，然後按一下確定。
4. 按一下 [SKU] > 選取您為 SQL 主控伺服器 建立的 SQL SKU > [確定]。
5. 按一下 [建立] 。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 部署 SQL Server 資源提供者
> * 建立優惠
> * 測試供應項目

請前進到下一個教學課程，以了解如何：

> [!div class="nextstepaction"]
> [將 Web、行動裝置與 API 應用程式提供給您的使用者]( azure-stack-tutorial-app-service.md)

