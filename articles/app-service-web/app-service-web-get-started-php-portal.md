---
title: "五分鐘內在 Azure 入口網站中部署 WordPress 應用程式 | Microsoft Docs"
description: "藉由部署 WordPress 應用程式，了解在 App Service 中執行 Web 應用程式有多麼簡單。 立即看到結果。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.contentlocale: zh-tw
ms.lasthandoff: 03/01/2017

---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>五分鐘內在 Azure 入口網站中部署 WordPress 應用程式

本教學課程示範如何在幾分鐘內，將您的第一個 [WordPress](https://wordpress.org/) Web 應用程式部署至 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

![WordPress 網站](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>必要條件
您需要 Microsoft Azure 帳戶。 如果您沒有這類帳戶，可以[註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 您可以[試用 App Service](https://azure.microsoft.com/try/app-service/)，而不需要 Azure 帳戶。 建立入門 App，並試用長達一小時。不需要信用卡，也不需簽定合約。
> 
> 

## <a name="deploy-the-wordpress-app"></a>部署 WordPress 應用程式
1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 開啟 [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress)。

    此連結是在 Azure 入口網站中立即設定新 WordPress 應用程式的捷徑。

3. 在 [應用程式名稱] 中，輸入 Web 應用程式名稱。 如果名稱在 `azurewebsites.net` 網域中是唯一的，您將在方塊中看到綠色核取記號。
   
5. 在 [資源群組] 中，按一下 [新建] 來建立新的[資源群組](../azure-resource-manager/resource-group-overview.md)，然後為它命名。

6. 在 [資料庫提供者] 中，選取 [CleaDB]。

7. 按一下 [App Service 方案/位置] > [新建]。 設定 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)，如下所示：

    - 在 [App Service 方案] 中，輸入所需的名稱。
    - 在 [位置] 中，選擇要裝載方案的位置。
    - 按一下 [定價層]，然後選取 [F1 免費] 或另一個適合您的層級，然後按一下 [選取]。
    - 按一下 [確定] 。

8. 按一下 [資料庫] > [新建]。 設定 SQL Database，如下所示：

    - 在 [資料庫名稱] 中，輸入資料庫名稱。 
    - 在 [位置] 中，選擇與 App Service 方案相同的位置。
    - 按一下 [定價層]，然後選取 [Mercury] 或另一個適合您的層級，然後按一下 [選取]。
    - 按一下 [法律條款]，然後按一下 [購買]。
    - 按一下 [確定] 。

9. 按一下 [建立] 。

    Azure 現在會根據您的設定建立 WordPress 應用程式。 您應該會看到 [部署已開始...] 通知。

    ![已開始部署 - Azure App Service 中的第一個 WordPress](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>啟動和管理 WordPress Web 應用程式

當 Azure 完成應用程式部署時，您會看到另一個通知。

![已成功部署 - Azure App Service 中的第一個 WordPress](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. 按一下通知。 如果您錯過了通知，一律可按一下通知鈴來存取該通知 (![通知鈴 - Azure App Service 中的第一個 WordPress](./media/app-service-web-get-started-dotnet-portal/notification.png))。

    您現在應該會看到 Web 應用程式的管理[刀鋒視窗](../azure-resource-manager/resource-group-portal.md#manage-resources) (*刀鋒視窗*︰水平開啟的入口網站頁面)。

3. 在 [概觀] 頁面頂端，按一下 [瀏覽]。
   
    ![瀏覽 - Azure App Service 中的第一個 WordPress](./media/app-service-web-get-started-php-portal/browse.png)

    現在您會看到 WordPress 的 [歡迎使用] 頁面。 設定 WordPress 安裝，然後開始使用！

    ![WordPress 設定 - Azure App Service 中的第一個 WordPress](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>後續步驟
* [建立、設定和部署 PHP Web 應用程式至 Azure](app-service-web-php-get-started.md) - 了解您在 Azure 中執行任何 PHP Web 應用程式所需的基本技巧，例如：

    * 從 PowerShell/Bash 在 Azure 中建立及設定 App。
    * 設定 PHP 版本。
    * 使用不在應用程式根目錄中的啟動檔案。
    * 啟用編寫器自動化。
    * 存取環境特有的變數。
    * 針對常見錯誤進行疑難排解。

* [將程式碼部署至 Azure App Service](web-sites-deploy.md) - 了解如何從 FTP 或從原始檔控制儲存機制進行部署。
* [在您的第一個 Web 應用程式中新增功能](app-service-web-get-started-2.md) - 加強您 Azure App 的功能。 驗證您的使用者。 根據需求加以調整。 設定一些效能警示。 都只要點幾下滑鼠就能完成。

