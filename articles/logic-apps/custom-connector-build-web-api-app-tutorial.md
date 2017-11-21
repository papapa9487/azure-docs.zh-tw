---
title: "從 Web API 建立自訂連接器 - Azure Logic Apps | Microsoft Docs"
description: "從 Web API 建置自訂連接器"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>從 Web API 建立自訂連接器

若要建置您可以在 Azure Logic Apps、Microsoft Flow 或 Microsoft PowerApps 中使用的自訂連接器，請先建立一個可以使用 Azure Web Apps 裝載的 Web API、向 Azure Active Directory 進行驗證，然後以連接器的形式向 Logic Apps、Flow 或 PowerApps 註冊。 本教學課程說明如何透過建置 ASP.NET Web API 應用程式來執行這些工作。 如需說明如何為連接器的觸發程序和動作建構程式碼的模式，請參閱[建立您可以從邏輯應用程式工作流程呼叫的自訂 API](../logic-apps/logic-apps-create-api-app.md)。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2013 或更新版本](https://www.visualstudio.com/vs/)。 本教學課程使用 Visual Studio 2015。

* 您 Web API 的程式碼。 如果您還沒有任何程式碼，請試試這個教學課程：[開始使用 ASP.NET Web API 2 (C#)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) \(英文\)。

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以從[免費 Azure 帳戶](https://azure.microsoft.com/free/)開始著手。 否則，請註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>建立 ASP.NET Web 應用程式並部署到 Azure

針對本教學課程，請建立一個 Visual C# ASP.NET Web 應用程式。 

1. 開啟 Visual Studio，然後選擇 [檔案] > [新增專案]。

   1. 展開 [已安裝]，移至 [範本] > [Visual C#] > [Web]，然後選取 [ASP.NET Web 應用程式]。

   2. 為您的應用程式提供專案名稱、位置及方案名稱，然後選擇 [確定]。

   例如：

   ![建立一個 Visual C# ASP.NET Web 應用程式](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. 在 [新增 ASP.NET Web 應用程式] 方塊中，選取 [Web API] 範本。 如果尚未選取 [雲端中的主機]，請選取。 選擇 [變更驗證] 。

   ![選取 [Web API] 範本、[雲端中的主機]、[變更驗證]](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. 選取 [無驗證]，然後選擇 [確定]。 您可以稍後設定驗證。

   ![選取 [無驗證]](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. 當 [新增 ASP.NET Web 應用程式] 對話方塊出現時，選擇 [確定]。 

5. 在 [建立應用程式服務] 方塊中，檢閱下表中所述的裝載設定，進行您想要的變更，然後選擇 [建立]。 

   [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)代表一組用來裝載您 Azure 訂用帳戶中應用程式的實體資源。 深入了解 [App Service](../app-service/app-service-value-prop-what-is.md)。

   ![建立應用程式服務](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | 您的 Azure 公司或學校帳戶，或您的個人 Microsoft 帳戶 | *your-user-account* | 選取您的使用者帳戶。 | 
   | **Web 應用程式名稱** | *custom-web-api-app-name* 或預設名稱 | 輸入您 Web API 應用程式的名稱，此名字會用在應用程式的 URL 中，例如：http://*web-api-app-name*。 | 
   | **訂用帳戶** | *Azure-subscription-name* | 選取您要使用的 Azure 訂用帳戶。 | 
   | **資源群組** | *Azure-resource-group-name* | 選取現有的 Azure 資源群組，或如果您還沒有資源群組，則建立一個資源群組。 <p>**注意**：Azure 資源群組會組織您 Azure 訂用帳戶中的 Azure 資源。 | 
   | **App Service 方案** | *App-Service-plan-name* | 選取現有的 App Service 方案，或如果您還沒有方案，則建立一個方案。 | 
   |||| 

   如果您要建立 App Service 方案，請指定下列設定：

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **位置** | *deployment-region* | 選取用於部署您應用程式的區域。 | 
   | **大小** | *App-Service-plan-size* | 選取您的方案大小，這會決定服務方案的成本和計算資源容量。 | 
   |||| 

   若要設定您應用程式所需的任何其他資源，請選擇 [探索其他 Azure 服務]。

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **資源類型** | *Azure-resource-type* | 選取和設定您應用程式所需的任何其他資源。 | 
   |||| 

6. 在 Visual Studio 部署您的專案之後，請組建您應用程式的程式碼。

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>建立一個描述您 Web API 的 OpenAPI (Swagger) 檔案

若要將您的 Web API 應用程式連線到 Logic Apps，您需要一個描述 API 作業的 [OpenAPI (以前稱為 Swagger) 檔案](http://swagger.io/)。 您可以使用 [Swagger 線上編輯器](http://editor.swagger.io/)(英文\) 為 API 撰寫您自己的 OpenAPI 定義，但本教學課程會使用名為 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md) \(英文\) 的開放原始碼工具。

1. 如果您還沒有這個工具，請在 Visual Studio 專案中安裝 Swashbuckle Nuget 套件。

   1. 在 Visual Studio 中，選擇 **工具** > **NuGet 套件管理員** > 
   **套件管理器主控台**。

   2. 在 [套件管理器主控台] 主控台中，如果您不是在應用程式的專案目錄中，請移至該目錄 (執行 `Set-Location "project-path"`)，然後執行此 PowerShell Commandlet： 
   
      `Install-Package Swashbuckle`

      例如：

      ![套件管理器主控台，安裝 Swashbuckle](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > 如果您在安裝 Swashbuckle 之後執行應用程式，Swashbuckle 就會在以下 URL 產生 OpenAPI 檔案： 
   >
   > http://*{your-web-api-app-root-URL}*/swagger/docs/v1
   > 
   > Swashbuckle 也會在以下 URL 產生使用者介面： 
   > 
   > http://*{your-web-api-app-root-URL}*/swagger

3. 準備就緒時，將您的 Web API 應用程式發佈至 Azure。 若要從 Visual Studio 發佈，請在 [方案總管] 中您的 Web 專案上按一下滑鼠右鍵，選擇 [發佈]，然後依照提示進行操作。

   > [!IMPORTANT]
   > 作業識別碼重複會導致 OpenAPI 文件無效。 如果您使用的是範例 C# 範本，此範本會「將以下作業識別碼重複兩次」：`Values_Get` 
   > 
   > 若要修正此問題，請將其中一個執行個體變更為 `Value_Get`，然後發佈。

4. 瀏覽至以下位置來取得 OpenAPI 文件： 

   http://*{your-web-api-app-root-URL}*/swagger/docs/v1

   您也可以從本教學課程下載[範例 OpenAPI 文件](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json)。 
   請務必先移除註解 (開頭為 "//")，再使用此文件。

5. 將內容儲存成 JSON 檔案。 視您的瀏覽器而定，您可能必須將文字複製並貼到空白文字檔中。

## <a name="next-steps"></a>後續步驟

* [設定自訂連接器的驗證](../logic-apps/custom-connector-azure-active-directory-authentication.md)











