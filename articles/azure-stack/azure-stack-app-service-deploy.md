---
title: "部署 App Service：Azure Stack | Microsoft Docs"
description: "部署 Azure Stack 中之 App Service 的詳細指導方針"
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
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: 522e5a334b5165344b66524d03f0d85468b81332
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>將 App Service 資源提供者新增到 Azure Stack

身為 Azure Stack 雲端操作員，您可以讓使用者具有建立 Web 和 API 應用程式的能力。 若要這樣做，您必須如本文所述，先將 [App Service 資源提供者](azure-stack-app-service-overview.md)新增至您的 Azure Stack 部署。 安裝 App Service 資源提供者之後，您可以將它併入至您的供應項目和方案中。 然後使用者可以訂閱以取得服務，並開始建立應用程式。

> [!IMPORTANT]
> 在執行安裝程式之前，請確定您已依照[開始之前](azure-stack-app-service-before-you-get-started.md)中的指導方針進行。
> 
>



## <a name="run-the-app-service-resource-provider-installer"></a>執行 App Service 資源提供者安裝程式

將 App Service 資源提供者安裝到 Azure Stack 環境最多可能需要一小時的時間。 在此過程中，安裝程式將會：

* 在指定的 Azure Stack 儲存體帳戶中建立 Blob 容器。
* 建立適用於 App Service 的 DNS 區域與項目。
* 註冊 App Service 資源提供者。
* 註冊 App Service 資源庫項目。

若要部署 App Service 資源提供者，請遵循下列步驟：

1. 以系統管理員身分 (azurestack\CloudAdmin) 執行 appservice.exe。

2. 按一下 [在您的 Azure Stack 雲端上部署 App Service]。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image01.png)

3. 檢閱並接受 Microsoft 軟體授權條款，然後按 [下一步]。

4. 檢閱並接受協力廠商授權條款，然後按 [下一步]。

5. 請確定 App Service 雲端組態資訊正確。 如果您在 Azure Stack 開發組件部署期間使用了預設設定，在這裡可以接受預設值。 不過，如果部署 Azure Stack 時自訂了選項，則必須編輯此視窗中的值以反映那些選項。 例如，如果您使用網域尾碼 mycloud.com，您的端點必須變更為 management.mycloud.com。確認您的資訊之後，按 [下一步]。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image02.png)

6. 在下一個頁面上：
    1. 按一下 [Azure Stack 訂用帳戶] 方塊旁邊的 [連線] 按鈕。
        - 如果您使用 Azure Active Directory (Azure AD)，請輸入部署 Azure Stack 時所提供的 Azure AD 管理員帳戶和密碼。 按一下 [登入] 。
        - 如果您使用 Active Directory 同盟服務 (AD FS)，請提供您的管理帳戶。 例如： cloudadmin@azurestack.local。 輸入您的密碼，然後按一下 [登入]。
    2. 在 [Azure Stack 訂用帳戶] 方塊中，選取您的訂用帳戶。
    3. 在 [Azure Stack 位置] 方塊中，選取對應到您要部署之區域的位置。 例如，如果要部署至 Azure Stack 開發套件，請選取 [本機]。
    4. 針對您的 App Service 部署輸入**資源群組名稱**。 根據預設值，它是設定為 **APPSERVICE\<REGION\>**。
    5. 輸入您想要 App Service 在安裝期間建立的**儲存體帳戶名稱**。 根據預設值，它是設定為 **appsvclocalstor**。
    6. 按一下 [下一步] 。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image03.png)

7. 輸入檔案共用的資訊，然後按 [下一步]。 檔案共用的位址必須使用檔案伺服器的完整網域名稱 (例如 \\\appservicefileserver.local.cloudapp.azurestack.external\websites) 或 IP 位址 (例如 \\\10.0.0.1\websites)。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image04.png)

8. 在下一個頁面上：
    1. 在 [身分識別應用程式識別碼] 方塊中，輸入您要用於身分識別的應用程式 GUID (來自 Azure AD)。
    2. 在 [身分識別應用程式憑證檔案] 方塊中，輸入 (或瀏覽至) 憑證檔案的位置。
    3. 在 [身分識別應用程式憑證密碼] 方塊中，輸入憑證的密碼。 此密碼是當您使用指令碼來建立憑證時所記下的密碼。
    4. 在 [Azure Resource Manager 根憑證檔案] 方塊中，輸入 (或瀏覽至) 憑證檔案的位置。
    5. 按一下 [下一步] 。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image05.png)

9. 對於三個憑證檔案方塊的每一個，按一下 [瀏覽] 並瀏覽至適當的憑證檔案。 您必須提供每個憑證的密碼。 這些憑證是您在[建立必要的憑證步驟](azure-stack-app-service-deploy.md#create-the-required-certificates)中建立的憑證。 輸入所有資訊之後，按 [下一步]。

    | Box | 憑證檔案名稱範例 |
    | --- | --- |
    | **App Service 預設 SSL 憑證檔案** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API SSL 憑證檔案** | api.appservice.local.AzureStack.external.pfx |
    | **App Service 發行者 SSL 憑證檔案** | ftp.appservice.local.AzureStack.external.pfx |

    如果您在建立憑證時使用不同的網域尾碼，您的憑證檔案名稱不會使用 *local.AzureStack.external*。 而是會改用您的自訂網域資訊。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image06.png)    

10. 針對用於裝載 App Service 資源提供者資料庫的伺服器執行個體，輸入 SQL Server 詳細資料，然後按 [下一步]。 按 [下一步]，安裝程式即會驗證 SQL 連線屬性。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image07.png)    

11. 檢閱角色執行個體和 SKU 選項。 填入的預設值為 ASDK 部署中每個角色的最少執行個體數目和最低 SKU。 系統會提供 vCPU 和記憶體的需求摘要，以協助您規劃部署。 進行選擇之後，按一下 [下一步]。

    > [!NOTE]
    > 對於生產環境部署，請按照 [Azure Stack 中的 Azure App Service 伺服器角色的容量規劃](azure-stack-app-service-capacity-planning.md)中的指導方針進行。
    > 
    >

    | 角色 | 最少執行個體 | 最低 SKU | 注意事項 |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A1 - (1 vCPU, 1792 MB) | 管理及維護 App Service 雲端的健全狀況。 |
    | 管理 | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | 管理 App Service Azure Resource Manager 和 API 端點、入口網站擴充功能 (管理員、租用戶、Functions 入口網站)，以及資料服務。 為了支援容錯移轉，已將建議的執行個體增加為 2 個。 |
    | 發佈者 | 1 | Standard_A1 - (1 vCPU, 1792 MB) | 透過 FTP 和 Web 部署發佈內容。 |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | 將要求傳送至 App Service 應用程式。 |
    | 共用背景工作 | 1 | Standard_A1 - (1 vCPU, 1792 MB) | 裝載 Web 或 API 應用程式和 Azure Functions 應用程式。 建議您新增更多執行個體。 身為操作員，您可以定義您的供應項目，並選擇任何 SKU 層。 各層必須具有至少一個 vCPU。 |

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image08.png)    

    > [!NOTE]
    > **Windows Server 2016 Core 不是支援的平台映像，無法搭配 Azure Stack 上的 Azure App Service 使用**。

12. 在 [選取平台映像] 方塊中，從可以在適用於 App Service 雲端的運算資源提供者的可用映像中，選擇您的部署 Windows Server 2016 虛擬機器映像。 按一下 [下一步] 。

13. 在下一個頁面上：
     1. 輸入背景工作角色虛擬機器系統管理員使用者名稱和密碼。
     2. 輸入其他角色的虛擬機器系統管理員使用者名稱和密碼。
     3. 按一下 [下一步] 。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image09.png)    

14. 在摘要頁面上：
    1. 確認您所做的選擇。 若要進行變更，請使用 [上一步] 按鈕來瀏覽先前的頁面。
    2. 如果設定均正確，請選取核取方塊。
    3. 若要開始部署，請按一下 [下一步]。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image10.png)    

15. 在下一個頁面上：
    1. 追蹤安裝進度。 根據預設的選取項目而定，部署 Azure App Service on Azure Stack 大約需要 60 分鐘。
    2. 當安裝程式順利完成之後，請按一下 [結束]。

    ![App Service 安裝程式](media/azure-stack-app-service-deploy/image11.png)    


## <a name="validate-the-app-service-on-azure-stack-installation"></a>確認 Azure Stack 上的 App Service 安裝

1. 在 Azure Stack 管理入口網站中，前往 [管理 - App Service]。

2. 在狀態下的概觀，查看 [狀態]是否顯示為 [所有角色都已準備完成]。

    ![App Service 管理](media/azure-stack-app-service-deploy/image12.png)    

## <a name="test-drive-app-service-on-azure-stack"></a>測試 Azure Stack 上的 App Service

當您部署並註冊 App Service 資源提供者之後，請測試它，以確定使用者可以部署 Web 及 API 應用程式。

> [!NOTE]
> 您需要在方案內建立含有 Microsoft.Web 命名空間的供應項目。 然後，您需要具有訂閱此供應項目的租用戶訂用帳戶。 如需詳細資訊，請參閱[建立供應項目](azure-stack-create-offer.md)和[建立方案](azure-stack-create-plan.md)。
>
您*必須*具有租用戶訂用帳戶，才能建立使用 Azure Stack 上之 App Service 的應用程式。 服務管理員只能在管理入口網站內完成的功能，與 App Service 的資源提供者管理有關。 這些功能包括新增容量、設定部署來源，以及新增背景工作層和 SKU。
>
若要建立 Web、API 及 Azure Functions 應用程式，您必須使用租用戶入口網站，並具有租用戶訂用帳戶。

1. 在 Azure Stack 租用戶入口網站中，按一下 [新增] > [Web + 行動] > [Web 應用程式]。

2. 在 [Web 應用程式] 刀鋒視窗中，於 [Web 應用程式] 方塊中輸入名稱。

3. 按一下 [資源群組] 下方的 [新增]。 在 [資源群組] 方塊中輸入名稱。

4. 按一下 [App Service 方案/位置] > [新建]。

5. 在 [App Service 方案] 刀鋒視窗中，於 [App Service 方案] 方塊中輸入名稱。

6. 按一下 [定價層] > [免費 - 共用] 或 [共用 - 共用] > [選取] > [確定] > [建立]。

7. 新 Web 應用程式的磚隨即出現在儀表板上。 按一下此磚。

8. 在 [Web 應用程式] 刀鋒視窗中，按一下 [瀏覽] 以檢視此應用程式的預設網站。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>部署 WordPress、DNN 或 Django 網站 (選擇性)

1. 在 Azure Stack 租用戶入口網站中，按一下 [+]、移至 Azure Marketplace、部署 Django 網站，然後等待順利完成。 Django Web 平台會使用以檔案系統為基礎的資料庫。 它不需要任何額外的資源提供者，例如 SQL 或 MySQL。

2. 如果您也會部署 MySQL 資源提供者，就可以從 Marketplace 部署 WordPress 網站。 當系統提示您提供資料庫參數時，請使用您選擇的使用者名稱和伺服器名稱，以 User1@Server1 形式輸入使用者名稱。

3. 如果您也會部署 SQL Server 資源提供者，就可以從 Marketplace 部署 DNN 網站。 當系統提示您提供資料庫參數時，請選擇執行 SQL Server 且已連線到您資源提供者之電腦中的資料庫。

## <a name="next-steps"></a>後續步驟

您也可以嘗試其他[平台即服務 (PaaS) 服務](azure-stack-tools-paas-services.md)。

- [SQL Server 資源提供者](azure-stack-sql-resource-provider-deploy.md)
- [MySQL 資源提供者](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
