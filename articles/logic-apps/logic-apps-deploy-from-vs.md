---
title: "在 Visual Studio 中建立、建置和部署邏輯應用程式 - Azure Logic Apps | Microsoft Docs"
description: "建立 Visual Studio 專案，讓您能夠設計、建置和部署 Azure Logic Apps。"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: e7f5cf483d22e4c60dedbe5176ceb0bc8b2b6e66
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>在 Visual Studio 中設計、建置和部署 Azure Logic Apps

雖然 [Azure 入口網站](https://portal.azure.com/)提供一種絕佳的方式讓您能夠建立和管理 Azure Logic Apps，但您可以使用 Visual Studio 來設計、建置和部署邏輯應用程式。 Visual Studio 提供豐富的工具 (例如邏輯應用程式設計工具)，讓您能夠用來建立邏輯應用程式、設定部署和自動化範本，並部署至任何環境。 

若要開始使用 Azure Logic Apps，請先了解[如何在 Azure 入口網站中建立第一個邏輯應用程式](logic-apps-create-a-logic-app.md)。

## <a name="installation-steps"></a>安裝步驟

若要安裝和設定適用於 Azure Logic Apps 的 Visual Studio 工具，請遵循下列步驟。

### <a name="prerequisites"></a>必要條件

* [Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) 或 Visual Studio 2015
* [最新的 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 或更新版本)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Web 的存取權 (若使用內嵌的設計工具)

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>安裝適用於 Azure Logic Apps 的 Visual Studio 工具

在您安裝必要條件之後︰

1. 開啟 Visual Studio。 在 [工具] 功能表上，選取 [擴充功能和更新]。
2. 展開 [線上] 類別讓您可以在線上搜尋。
3. 瀏覽或搜尋 [Logic Apps]，直到您找出 [Azure Logic Apps Tools for Visual Studio]。
4. 若要下載並安裝擴充功能，按一下 [下載] 按鈕。
5. 在安裝好之後重新啟動 Visual Studio。

> [!NOTE]
> 您也可以直接從 Visual Studio Marketplace 下載 [Azure Logic Apps Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) 與 [Azure Logic Apps Tools for Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio)。

完成安裝之後，您就可以搭配使用 Azure 資源群組專案與邏輯應用程式設計工具。

## <a name="create-your-project"></a>建立專案

1. 在 [檔案] 功能表上，移至 [新增]，然後選取 [專案]。 或者如果要將您的專案新增至現有的方案，請移至 [新增]，然後選取 [新增專案]。

    ![[檔案] 功能表](./media/logic-apps-deploy-from-vs/filemenu.png)

2. 在 [新增專案] 視窗中，尋找 [雲端]，然後選取 [Azure 資源群組]。 將您的專案命名，然後按一下 [確定]。

    ![加入新的專案](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. 選取**邏輯應用程式**範本，其可建立空白的邏輯應用程式部署範本以供您使用。 選取您的範本後，按一下 [確定]。

    ![選取 [邏輯應用程式] 範本](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    現在您已將邏輯應用程式新增至方案。 
    在 [方案總管] 中，您的部署檔案應該會出現。

    ![部署檔案](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>利用邏輯應用程式設計工具建立邏輯應用程式

擁有包含邏輯應用程式的 Azure 資源群組專案時，您可以在 Visual Studio 內開啟邏輯應用程式設計工具以建立您的工作流程。 

> [!NOTE]
> 設計工具需要網際網路連線，才能查詢連接器的可用屬性和資料。 例如，如果您使用 Dynamics CRM Online 連接器，設計工具會查詢您的 CRM 執行個體，以顯示可用的自訂與預設屬性。

1. 以滑鼠右鍵按一下 `<template>.json` 檔案，然後選取 [使用邏輯應用程式設計工具來開啟]。 (`Ctrl+L`)

2. 選擇部署範本的 Azure 訂用帳戶、資源群組和位置。

    > [!NOTE]
    > 在設計邏輯應用程式時將會建立 API 連線資源，以在設計期間查詢屬性。 Visual Studio 會使用所選取的資源群組，在設計期間建立這些連線。 若要檢視或變更任何 API 連線，可前往 Azure 入口網站並瀏覽 **API 連線**。

    ![訂用帳戶選擇器](./media/logic-apps-deploy-from-vs/designer_picker.png)

    設計工具會使用 `<template>.json` 檔案中的定義進行轉譯。

4. 建立並設計邏輯應用程式。 會使用變更來更新部署範本。

    ![Visual Studio 中的邏輯應用程式設計工具](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Visual Studio 會針對您的邏輯應用程式需要函式的任何連線，將 `Microsoft.Web/connections` 資源新增至您的資源檔。 這些連線屬性可以在您部署時設定，並在您於 Azure 入口網站的 [API 連線] 中部署之後進行管理。

### <a name="switch-to-json-code-view"></a>切換到 JSON 程式碼檢視

若要顯示以 JSON 格式表示的邏輯應用程式，選取設計工具底部的 [程式碼檢視] 索引標籤。

若要切換回完整資源的 JSON，請以滑鼠右鍵按一下 `<template>.json` 檔案，然後選取 [開啟]。

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>將相依資源的參考新增至 Visual Studio 部署範本

當您想要邏輯應用程式參考相依的資源時，您可以在邏輯應用程式部署範本中使用 [Azure Resource Manager 範本函式](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions)。 例如，您可能想要邏輯應用程式參考您要與邏輯應用程式一起部署的 Azure 函式或整合帳戶。 遵循這些有關如何在部署範本中使用參數的指導方針，以便邏輯應用程式設計工具可正確轉譯。 

您可以在這些類型的觸發程序和動作中使用邏輯應用程式參數︰

*   子工作流程
*   函式應用程式
*   APIM 呼叫
*   API 連線執行階段 URL
*   API 連線路徑

您可以使用範本函式，例如 parameters、variables、resourceId、concat 等等。例如，以下是取代 Azure 函式資源識別碼的方式︰

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

以及您想使用參數的位置︰

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
另一個範例是您可以參數化服務匯流排傳送訊息作業：

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl 是選擇性的，如果存在的話，可以從您的範本中移除。
> 


> [!NOTE] 
> 針對您在使用參數時要運作的邏輯應用程式設計工具，您必須提供預設值，例如︰
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

### <a name="save-your-logic-app"></a>儲存您的邏輯應用程式

若要隨時儲存邏輯應用程式，請移至 [檔案] > [儲存]。 (`Ctrl+S`) 

如果當您儲存應用程式時邏輯應用程式有任何錯誤，它們會出現在 Visual Studio [輸出] 視窗中。

## <a name="deploy-your-logic-app-from-visual-studio"></a>從 Visual Studio 部署邏輯應用程式

設定應用程式之後，只要幾個步驟，就能從 Visual Studio 直接部署。 

1. 在 [方案總管] 中，以滑鼠右鍵按一下您的專案，並移至 [部署] > [新增部署...]

    ![新增部署](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. 當系統提示您時，登入您的 Azure 訂用帳戶。 

3. 現在，您必須選取您要在其中部署邏輯應用程式的資源群組詳細資料。 完成後，選取 [部署]。

    > [!NOTE]
    > 請確定您選取正確的資源群組範本和參數檔案。 例如，如果您想要部署到生產環境中，選擇生產參數檔。

    ![部署到資源群組](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    部署狀態會出現在 [輸出] 視窗中。 
    您可能需要選取 [顯示輸出來源] 清單中的 [Azure 佈建]。

    ![部署狀態輸出](./media/logic-apps-deploy-from-vs/output.png)

未來，您可以在原始檔控制中編輯邏輯應用程式，並利用 Visual Studio 來部署新的版本。

> [!NOTE]
> 如果您直接在 Azure 入口網站中變更定義，則下次從 Visual Studio 部署時會覆寫那些變更。 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>將邏輯應用程式新增到現有的資源群組專案

如果您有現有的資源群組專案，可以在 [JSON 大綱] 視窗中將邏輯應用程式新增至該專案。 您也可以您先前建立的應用程式共同新增另一個邏輯應用程式。

1. 開啟 `<template>.json` 檔案。

2. 若要開啟 [JSON 大綱] 視窗，請前往 [檢視] > [其他視窗] > [JSON 大綱]。

3. 若要將資源新增至範本檔案，按一下 [JSON 大綱] 視窗頂端的 [新增資源]。 或在 [JSON 大綱] 視窗中，以滑鼠右鍵按一下 [資源]，然後選取 [新增資源]。

    ![[JSON 大綱] 視窗](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. 在 [新增資源] 對話方塊中，尋找並選取 [邏輯應用程式]。 為您的邏輯應用程式命名，然後選擇 [新增]。

    ![新增資源](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio Cloud Explorer 管理邏輯應用程式](logic-apps-manage-from-vs.md)
* [檢視常見的範例和案例](logic-apps-examples-and-scenarios.md)
* [了解如何使用 Azure Logic Apps 自動化商務程序](http://channel9.msdn.com/Events/Build/2016/T694)
* [了解如何整合您的系統與 Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
