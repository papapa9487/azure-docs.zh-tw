---
title: "將 Web、行動裝置與 API 應用程式提供給您的 Azure Stack 使用者 | Microsoft Docs"
description: "此教學課程說明如何安裝 App Service 資源提供者，並建立供應項目以為您的 Azure Stack 使用者提供建立 Web、行動裝置與 API 應用程式的能力。"
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
ms.topic: article
ms.date: 7/03/2017
ms.author: erikje
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 11ecaa8ec017a9eb1285928e3d3d367ddfc43022
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="make-web-mobile-and-api-apps-available-to-your-azure-stack-users"></a>將 Web、行動裝置與 API 應用程式提供給您的 Azure Stack 使用者

身為 Azure Stack 雲端系統管理員，您可以建立供應項目，以讓您的使用者 (租用戶) 建立 Azure Functions 與 Web、行動裝置與 API 應用程式。 透過將對這些隨選雲端式應用程式的存取權提供給您的使用者，您可以節省其時間與資源。 若要設定，您將必須：

> [!div class="checklist"]
> * 部署 App Service 資源提供者
> * 建立供應項目
> * 測試供應項目

## <a name="deploy-the-app-service-resource-provider"></a>部署 App Service 資源提供者

1. [準備 Azure Stack 開發套件主機](azure-stack-app-service-before-you-get-started.md)。 這包括部署 SQL Server 資源提供者，而這是建立某些應用程式的必要條件。
2. [下載安裝程式與協助程式指令碼](azure-stack-app-service-deploy.md#download-the-required-components)。
3. [執行協助程式指令碼以建立必要憑證](azure-stack-app-service-deploy.md#create-certificates-required-by-app-service-on-azure-stack)。
4. [安裝 App Service 資源提供者](azure-stack-app-service-deploy.md#use-the-installer-to-download-and-install-app-service-on-azure-stack) (安裝資源提供者並讓所有背景工作角色出現將需要數小時)。
5. [驗證安裝](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation)。

## <a name="create-an-offer"></a>建立供應項目

例如，您可以建立供應項目，以讓使用者 建立 DNN Web 內容管理系統。 它需要 SQL Server 服務 (您已透過安裝 SQL Server 資源提供者而啟用)。

1.  [設定配額](azure-stack-setting-quotas.md)並將它命名為 *AppServiceQuota*。 選取 [命名空間] 欄位的 [Microsoft.Web]。
2.  [建立方案](azure-stack-create-plan.md)。 將它命名為 *TestAppServicePlan*，選取 [Microsoft.SQL] 服務，並選取 [AppService 配額] 配額。

    > [!NOTE]
    > 若要讓使用者建立其他應用程式，方案中可能需要有其他服務。 例如，Azure Functions 要求方案必須包括 **Microsoft.Storage** 服務，而 Wordpress 則需要 **Microsoft.MySQL**。
    > 
    >

3.  [建立供應項目](azure-stack-create-offer.md)，將它命名為 **TestAppServiceOffer**，然後選取 [TestAppServicePlan] 方案。

## <a name="test-the-offer"></a>測試供應項目

既然您已部署 App Service 資源提供者並建立供應項目，您能以使用者身分登入並訂閱該供應項目，然後建立應用程式。 針對此範例，我們將建立 DNN 平台內容管理系統。 您必須先建立 SQL 資料庫，然後再建立 DNN Web 應用程式。

### <a name="subscribe-to-the-offer"></a>訂閱該供應項目
1. 以租用戶身分登入 Azure Stack 入口網站 (https://portal.local.azurestack.external)。
2. 按一下 [取得訂用帳戶] > 在 [顯示名稱] 下輸入 **TestAppServiceSubscription** > [選取服務] > [TestAppServiceOffer] > [建立]。

### <a name="create-a-sql-database"></a>建立 SQL 資料庫

1. 按一下 [+] > [資料 + 儲存體] > [SQL Database]。
2. 將欄位維持為預設值，下列欄位除外：
    - **資料庫名稱**：DNNdb
    - **大小上限 (MB)**：100
    - **訂用帳戶**：TestAppServiceOffer
    - **資源群組**：DNN-RG
3. 按一下 [登入設定]輸入資料庫認證，然後按一下 [確定]**OK**。 稍後您將在這些步驟中用到這些認證。
4. 按一下 [SKU] > 選取您為 SQL 主控伺服器 建立的 SQL SKU > [確定]。
5. 按一下 [建立]。

### <a name="create-a-dnn-app"></a>建立 DNN 應用程式    

1. 按一下 [+] > [查看全部] > [DNN 平台預覽] > [建立]。
2. 在 [應用程式名稱] 下輸入 *DNNapp*，然後在 [訂用帳戶] 下選取 [TestAppServiceOffer]。
3. 按一下 [設定必要設定] > [新建] > 輸入 [App Service 方案] 名稱。
4. 按一下 [定價層] > [F1 免費] > [選取] > [確定]。
5. 按一下 [資料庫] 並輸入您稍早建立之 SQL 資料庫的資訊。
6. 按一下 [建立]。

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 部署 App Service 資源提供者
> * 建立供應項目
> * 測試供應項目

請前進到下一個教學課程，以了解如何：

> [!div class="nextstepaction"]
> [將應用程式部署到 Azure 和 Azure Stack](azure-stack-solution-pipeline.md)

