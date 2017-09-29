---
title: "為 Azure Stack 上的應用程式服務設定部署來源 | Microsoft Docs"
description: "服務管理員該如何為 Azure Stack 上的 App Service 設定部署來源 (Git、GitHub、BitBucket、DropBox 和 OneDrive)"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: be4b032e8f7370f696c47a7b8e82c0a819529f4d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---

# <a name="configure-deployment-sources"></a>設定部署來源

Azure Stack 上的 App Service 支援從多個原始檔控制提供者進行隨選部署。  這項功能可讓應用程式開發人員能夠直接從其原始檔控制儲存機制進行部署。  為了讓租用戶能夠設定 App Service 以連線至其儲存機制，系統管理員必須先設定 Azure Stack 上的 App Service 與原始檔控制提供者之間的整合。  除了本機 Git 外，也支援下列原始檔控制提供者︰

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>檢視 App Service 管理中的部署來源

1. 以服務管理員身分登入 Azure Stack 管理入口網站 (https://adminportal.local.azurestack.external)。
2. 瀏覽至 [資源提供者]，然後選取 [App Service 資源提供者管理]。![App Service 資源提供者管理][1]
3. 按一下 [原始檔控制組態]。  您會在此看見所有已設定之部署來源的清單。
    ![App Service 資源提供者管理原始檔控制組態][2]

## <a name="configure-github"></a>設定 GitHub

> [!NOTE]
> 您需要有 GitHub 帳戶才能完成此工作。  您可能會想要使用組織的帳戶，而非個人帳戶。

1. 登入 GitHub、瀏覽至 https://www.github.com/settings/developers，然後按一下 [註冊新的應用程式] ![GitHub - 註冊新的應用程式][3]
2. 輸入 [應用程式名稱]，例如，Azure Stack 上的 App Service
3. 輸入 [首頁 URL]。  **首頁 URL 必須是 Azure Stack 入口網站位址**，例如 https://portal.local.azurestack.external
4. 輸入 [應用程式描述]
5. 輸入 [授權回呼 URL]。  在預設的 Azure Stack 部署中，Url 的形式為 https://portal.local.azurestack.external/tokenauthorize，如果您在不同的網域下執行，請以您的網域取代 azurestack.local ![GitHub - 使用填入的值註冊新的應用程式][4]
6. 按一下 [註冊應用程式]。  您現在會看到列出應用程式之 [用戶端識別碼] 和 [用戶端密碼] 的頁面。
    ![GitHub - 已完成的應用程式註冊][5]
7.  在新的瀏覽器索引標籤或視窗中，以服務管理員身分登入 Azure Stack 管理入口網站 (https://adminportal.local.azurestack.external)。 
8.  瀏覽至 [資源提供者]，然後選取 [App Service 資源提供者管理]。 
9. 按一下 [原始檔控制組態]。
10. 複製 [用戶端識別碼] 和 [用戶端密碼] 並貼到對應的 GitHub 輸入方塊。
11. 按一下 [儲存] 。
12. 如果您不想設定任何其他部署來源，請繼續[排程管理角色的修復](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles)。


## <a name="configure-bitbucket"></a>設定 BitBucket

> [!NOTE]
> 您需要有 BitBucket 帳戶才能完成此工作。  您可能會想要使用組織的帳戶，而非個人帳戶。

1. 登入 BitBucket，然後瀏覽至您帳戶底下的 [整合] ![BitBucket 儀表板 - 整合][7]
2. 按一下 [存取管理] 底下的 [OAuth] 與 [新增取用者] ![BitBucket 新增 OAuth 取用者][8]
3. 輸入取用者的 [名稱]，例如，Azure Stack 上的 App Service
4. 輸入應用程式的 [描述]
5. 輸入 [回呼 URL]。  在預設的 Azure Stack 部署中，回呼 Url 的形式為 https://portal.local.azurestack.external/TokenAuthorize，如果您在不同的網域下執行，請以您的網域取代 azurestack.local。  Url 必須按照此處列出的大小寫，BitBucket 整合才能成功。
6. 輸入 **URL**，此 Url 應為 Azure Stack 入口網站 URL，例如 https://portal.local.azurestack.external
7. 選取所需**權限** -  **存放庫**: **讀取** **Webhook**: **讀取及寫入**
8. 按一下 [儲存] 。  您現在會看到這個新的應用程式，以及 **OAuth 取用者**底下的**金鑰**和**密碼**。
    ![BitBucket 應用程式清單][9]
9.  在新的瀏覽器索引標籤或視窗中，以服務管理員身分登入 Azure Stack 管理入口網站 (https://adminportal.local.azurestack.external)。 
10.  瀏覽至 [資源提供者]，然後選取 [App Service 資源提供者管理]。 
11. 按一下 [原始檔控制組態]。
12. 複製**金鑰**並貼到 BitBucket 的 [用戶端識別碼] 輸入方塊，以及複製**密碼**並貼到 BitBucket 的 [用戶端密碼] 輸入方塊。
13. 按一下 [儲存] 。
14. 如果您不想設定任何其他部署來源，請繼續[排程管理角色的修復](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles)。

## <a name="configure-onedrive"></a>設定 OneDrive

> [!NOTE]
> 目前不支援商務用 OneDrive 帳戶。  您必須有連結到 OneDrive 帳戶的 Microsoft 帳戶，才能完成這項工作。  您可能會想要使用組織的帳戶，而非個人帳戶。

1. 瀏覽至 https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm 並使用 Microsoft 帳戶登入。
2. 按一下 [我的應用程式] 底下的 [新增應用程式]
![OneDrive 應用程式][10]
3. 為新的應用程式註冊輸入**名稱**，輸入 **Azure Stack 上的 App Service**，然後按一下 [建立應用程式]
4. 下一個畫面會列出新應用程式的屬性。 記錄下**應用程式識別碼**
![OneDrive 應用程式屬性][11]
5. 在 [應用程式密碼] 底下按一下 [產生新密碼]，然後記錄**所產生的新密碼** - 這是您的應用程式密碼。
> [!NOTE]
> 請務必記下新密碼，因為一旦您在此階段按一下 [確定] 後就無法再擷取。
6. 在 [平台] 下，按一下 [新增平台]，然後選取 [網站]
7. 輸入 [重新導向 URI]。  在預設的 Azure Stack 部署中，重新導向 URI 的形式為 https://portal.local.azurestack.external/tokenauthorize，如果您在不同的網域下執行，請以您的網域取代 azurestack.local ![OneDrive 應用程式 - 新增 Web 平台][12]
8. 設定 [Microsoft Graph 權限] - [委派權限]
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![OneDrive 應用程式 - Graph 權限][13]
10. 按一下 [儲存] 。
11.  在新的瀏覽器索引標籤或視窗中，以服務管理員身分登入 Azure Stack 管理入口網站 (https://adminportal.local.azurestack.external)。 
12.  瀏覽至 [資源提供者]，然後選取 [App Service 資源提供者管理]。 
13. 按一下 [原始檔控制組態]。
14. 複製**應用程式識別碼**並貼到 OneDrive 的 [用戶端識別碼] 輸入方塊，以及複製**密碼**並貼到 OneDrive 的 [用戶端密碼] 輸入方塊。
15. 按一下 [儲存] 。
16. 如果您不想設定任何其他部署來源，請繼續[排程管理角色的修復](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles)。

## <a name="configure-dropbox"></a>設定 DropBox

> [!NOTE]
> 您必須有 DropBox 帳戶才能完成這項工作。  您可能會想要使用組織的帳戶，而非個人帳戶。

1. 瀏覽至 https://www.dropbox.com/developers/apps ，並使用 DropBox 帳戶登入
2. 按一下 **建立應用程式** 
![Dropbox 應用程式][14]
3. 選取 [DropBox API]
4. 將存取層級設為 [應用程式資料夾]
5. 輸入應用程式的**名稱**。
![Dropbox 應用程式註冊][15]
6. 按一下 [建立應用程式]。  您現在會看到列出應用程式設定的頁面，包括**應用程式金鑰**和**應用程式密碼**。
7. 確認 [應用程式資料夾名稱] 已設為 **Azure Stack 上的 App Service**
8. 設定 [OAuth 2 重新導向 URI]，然後按一下 [新增]。  在預設的 Azure Stack 部署中，重新導向 URI 的形式為 https://portal.local.azurestack.external/tokenauthorize，如果您在不同的網域下執行，請以您的網域取代 azurestack.local ![Dropbox 應用程式設定][16]
9.  在新的瀏覽器索引標籤或視窗中，以服務管理員身分登入 Azure Stack 管理入口網站 (https://adminportal.local.azurestack.external)。 
10.  瀏覽至 [資源提供者]，然後選取 [App Service 資源提供者管理]。 
11. 按一下 [原始檔控制組態]。
12. 複製**應用程式金鑰**並貼到 DropBox 的 [用戶端識別碼] 輸入方塊，以及複製**應用程式密碼**並貼到 DropBox 的 [用戶端密碼] 輸入方塊。
13. 按一下 [儲存] 。
14. 如果您不想設定任何其他部署來源，請繼續[排程管理角色的修復](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles)。

## <a name="schedule-repair-of-management-roles"></a>排程管理角色的修復
為了套用各種部署來源組態中所更新的設定，您必須修復管理角色。  此程序可確保組態值能夠正確套用，並將所設定的部署來源提供給租用戶使用。

1. 在新的瀏覽器索引標籤或視窗中，以服務管理員身分登入 Azure Stack 管理入口網站 (https://adminportal.local.azurestack.external)。
2. 瀏覽至 [資源提供者]，然後選取 [App Service 資源提供者管理]。
3. 按一下 [原始檔控制組態]
4. 複製 [用戶端識別碼] 和 [用戶端密碼] 並貼到對應的 GitHub 輸入方塊。
5. 按一下 [儲存] 
6. 按一下 [角色]
7. 按一下 [管理伺服器]
8. 按一下 [全部修復]，然後選取 [是]。  這項作業會在所有管理伺服器上排程修復作業，以便完成整合。  修復作業會受到控管，以將停機時間降低最低。
    ![App Service 資源提供者管理 - 角色 - 管理伺服器全部修復][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

