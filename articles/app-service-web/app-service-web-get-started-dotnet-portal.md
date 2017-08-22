---
title: "五分鐘內在 Azure 入口網站中部署 Umbraco Web 應用程式 | Microsoft Docs"
description: "藉由部署範例 ASP.NET 應用程式，了解在 App Service 中執行 Web 應用程式有多麼簡單。 立即看到結果。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 6b1dede903083d1771733330a069b6ab533d9f00
ms.contentlocale: zh-tw
ms.lasthandoff: 04/06/2017

---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>五分鐘內在 Azure 入口網站中部署 Umbraco Web 應用程式

本教學課程將協助您在幾分鐘內將 n 個 [Umbraco](https://our.umbraco.org/) Web 應用程式部署至 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

![Umbraco 應用程式](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>必要條件
您需要 Microsoft Azure 帳戶。 如果您沒有這類帳戶，可以[註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 您可以[試用 App Service](https://azure.microsoft.com/try/app-service/)，而不需要 Azure 帳戶。 建立入門 App，並試用長達一小時。不需要信用卡，也不需簽定合約。
> 
> 

## <a name="deploy-the-aspnet-app"></a>部署 ASP.NET 應用程式
1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 開啟 [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS)。

    此連結是在 Azure 入口網站中立即設定新 Umbraco 應用程式的捷徑。

3. 在 [應用程式名稱] 中，輸入 Web 應用程式名稱。 如果名稱在 `azurewebsites.net` 網域中是唯一的，您將在方塊中看到綠色核取記號。
   
5. 在 [資源群組] 中，按一下 [新建] 來建立新的[資源群組](../azure-resource-manager/resource-group-overview.md)，然後為它命名。

7. 按一下 [App Service 方案/位置] > [新建]。 設定 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)，如下所示：

    - 在 [App Service 方案] 中，輸入所需的名稱。
    - 在 [位置] 中，選擇要裝載方案的位置。
    - 按一下 [定價層]，然後選取 [F1 免費] 或另一個適合您的層級，然後按一下 [選取]。
    - 按一下 [確定] 。

    Umbraco CMS 設定現在看起來應該類似下列螢幕擷取畫面：

    ![設定進行中 - Azure App Service 中的第一個 Umbraco](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. 按一下 [SQL Database] > [建立新的資料庫]。 設定 SQL Database，如下所示：

    - 在 [名稱] 中輸入名稱，例如 **myDB**。
    - 按一下 [定價層]，然後選取 [F 免費] 或另一個適合您的層級，然後按一下 [選取]。
    - 按一下 [目標伺服器] > [建立新的伺服器]。 設定資料庫伺服器，如下所示：

        - 在 [伺服器名稱] 中，輸入伺服器名稱。 如果名稱在 `.database.windows.net` 網域中是唯一的，您將在方塊中看到綠色核取記號。
        - 在 [伺服器管理員登入] 中，輸入所需的管理員使用者名稱。
        - 在 [密碼] 和 [確認密碼] 中，輸入所需的密碼。
        - 在 [位置] 中，選取 Web 應用程式所使用的相同位置。
        - 確定已選取 [允許 Azure 服務存取伺服器]。
        - 按一下 [選取] 。
    
    - 按一下 [選取] 。

13. 按一下 [Web 應用程式設定]、指定資料庫使用者名稱和密碼，然後按一下 [確定]。

    Umbraco CMS 設定現在看起來應該類似下列螢幕擷取畫面：

    ![設定完成 - Azure App Service 中的第一個 Umbraco](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. 按一下 [建立] 。
    
    Azure 現在會根據您的設定建立 Umbraco 應用程式。 您應該會看到 [部署已開始...] 通知。

    ![部署成功 - Azure App Service 中的第一個 Umbraco](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>啟動及管理您的 Umrbaco Web 應用程式

當 Azure 完成應用程式部署時，您會看到另一個通知。

![部署成功 - Azure App Service 中的第一個 Umbraco](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. 按一下通知。 如果您錯過了通知，一律可按一下通知鈴來存取該通知 (![通知鈴 - Azure App Service 中的第一個 Umbraco](./media/app-service-web-get-started-dotnet-portal/notification.png))。

    您現在應該會看到 Web 應用程式的管理[刀鋒視窗](../azure-resource-manager/resource-group-portal.md#manage-resources) (*刀鋒視窗*︰水平開啟的入口網站頁面)。

3. 在 [概觀] 頁面頂端，按一下 [瀏覽]。
   
    ![瀏覽 - Azure App Service 中的第一個 Umbraco](./media/app-service-web-get-started-dotnet-portal/browse.png)

    現在您會看到 Umbraco 的 [歡迎使用] 頁面。 設定 Umbraco 安裝，然後開始使用！

    ![Umbraco 設定 - Azure App Service 中的第一個 Umbraco](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>後續步驟
* [使用 Visual Studio 將 ASP.NET Web 應用程式部署至 Azure App Service](app-service-web-get-started-dotnet.md) - 了解如何從 Visual Studio 中，使用任一個隨附的應用程式範本建立新的 Azure Web 應用程式。
* [將程式碼部署至 Azure App Service](web-sites-deploy.md) - 了解如何從 FTP 或從原始檔控制儲存機制進行部署。
* [在您的第一個 Web 應用程式中新增功能](app-service-web-get-started-2.md) - 加強您 Azure App 的功能。 驗證您的使用者。 根據需求加以調整。 設定一些效能警示。 都只要點幾下滑鼠就能完成。

