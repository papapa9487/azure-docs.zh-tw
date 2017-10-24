---
title: "Azure Active Directory B2C：使用自訂原則動態地自訂 Azure AD B2C 使用者介面 (UI)"
description: "如何使用會在執行階段動態變更的 HTML/CSS 內容支援多個品牌體驗"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: cbce9b72c25c90dde526ff229f77a85ce9478efa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-using-custom-policies"></a>Azure Active Directory B2C：使用自訂原則設定具有動態內容的 UI
Azure AD B2C 自訂原則可讓您在查詢字串中透過參數傳送。 參數會傳遞至您的 HTML 端點，並且可以動態變更網頁內容。 例如，視您從 Web/行動裝置應用程式傳遞的參數而定，您可以變更 B2C 註冊或登入背景影像。 

## <a name="prerequisites"></a>必要條件
本文著重在如何使用自訂原則來自訂具有**動態內容**的 Azure AD B2C 使用者介面。 若要開始使用自訂原則 UI 自訂，請閱讀[自訂原則中的 UI 自訂](active-directory-b2c-ui-customization-custom.md)文章。 

>[!NOTE]
>
>  [設定自訂原則中的 UI 自訂](active-directory-b2c-ui-customization-custom.md)文章會說明使用自訂原則的 Azure AD B2C UI 自訂基本概念：
> * 頁面使用者介面 (UI) 自訂功能。
> * 協助您使用我們的範例內容測試頁面 UI 自訂功能的工具。
> * 每種頁面中的核心 UI 元素。
> * 執行這項功能時的最佳做法。

## <a name="adding-a-link-to-html5css-templates-to-your-user-journey"></a>將 HTML5/CSS 範本的連結新增至使用者旅程圖

在自訂原則中，內容定義會定義用於指定 UI 步驟的 HTML5 頁面 URI。 例如，登入或註冊頁面。 基礎原則會藉由指向 HTML5 檔案的 URI (指其 CSS) 來定義預設的外觀及操作。 在擴充原則中，您可以藉由覆寫該 HTML5 檔案的 LoadUri 來修改外觀及操作。 因此，在適當情況下，內容定義包含透過製作 HTML5/CSS 檔案定義的外部內容的 URL。 

`ContentDefinitions` 區段包含一系列的 `ContentDefinition` XML 元素。 `ContentDefinition` 元素的識別碼屬性會指定與內容定義相關的頁面類型。 因此將會使用自訂 HTML5/CSS 範本所在的內容。 下表說明 IEF 引擎所能辨識之內容定義識別碼的集合，以及與這些識別碼有關的頁面類型。

| 內容定義識別碼 | 預設 HTML5 範本| 說明 | 
|-----------------------|--------|-------------|
| api.error | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **錯誤頁面**。 在發生例外狀況或錯誤時，系統會顯示此頁面。 |
| api.idpselections | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **識別提供者選取頁面**。 此頁面包含使用者可以在登入期間選擇的識別提供者清單。 這些選項一般是企業識別提供者、社交識別提供者 (如 Facebook 和 Google+) 或本機帳戶。 |
| api.idpselections.signup | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **用於註冊的識別提供者選取**。 此頁面包含使用者可以在註冊期間選擇的識別提供者清單。 這些選項是企業識別提供者、社交識別提供者 (如 Facebook 和 Google+) 或本機帳戶。 |
| api.localaccountpasswordreset | selfasserted.html[](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **忘記密碼頁面**。 此頁面包含一份表單，使用者必須填妥此表單才能開始重設密碼。  |
| api.localaccountsignin | selfasserted.html[](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本機帳戶登入頁面**。 此頁面包含登入表單，可供使用以電子郵件地址或使用者名稱為基礎的本機帳戶進行登入。 此表單可以包含文字輸入方塊和密碼輸入方塊。 |
| api.localaccountsignup | selfasserted.html[](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本機帳戶註冊頁面**。 此頁面包含登入表單，可供註冊以電子郵件地址或使用者名稱為基礎的本機帳戶。 此表單可以包含各種輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單方塊和多選核取方塊。 |
| api.phonefactor | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication 頁面**。 在此頁面上，使用者可以在註冊或登入期間驗證其電話號碼 (藉由使用文字或語音)。 |
| api.selfasserted | selfasserted.html[](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **社交帳戶註冊頁面**。 此頁面包含使用者在使用社交識別提供者的現有帳戶註冊時必須填妥的註冊表單。 此頁面類似於上述的社交帳戶註冊頁面，但密碼輸入欄位除外。 |
| api.selfasserted.profileupdate | updateprofile.html[](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **設定檔更新頁面**。 此頁面包含一份表單，以供使用者用來更新其設定檔。 此頁面類似於社交帳戶註冊頁面，但密碼輸入欄位除外。 |
| api.signuporsignin | unified.html[](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **統一的註冊或登入頁面**。 此頁面可同時處理使用者的註冊和登入。 使用者可以使用企業識別提供者、社交識別提供者 (如 Facebook 或 Google+) 或本機帳戶。  |

## <a name="serving-dynamic-content"></a>提供動態內容
在先前的文章[在自訂原則中設定 UI 自訂](active-directory-b2c-ui-customization-custom.md)中，您將 HTML5 檔案上傳至 Azure Blob 儲存體。 這些 HTML5 檔案都是靜態，並且對每個要求呈現相同的 HTML 內容。 在本文中，我們會使用 ASP.Net Web 應用程式，它可以接受查詢字串參數，並據以回應。 在本逐步解說中，您將：
* 建立會主控 HTML5 範本的 ASP.NET Core Web 應用程式 
* 新增自訂 HTML5 範本 _unified.cshtml_ 
* 將您的 Web 應用程式發佈至 Azure Service 應用程式 
* 設定 Web 應用程式的 CORS
* 覆寫 `LoadUri` 元素以指向 HTML5 檔案

## <a name="step-1-create-an-aspnet-web-app"></a>步驟 1：建立 ASP.NET Web 應用程式

1.  在 Visual Studio 中，選取 [檔案] > [新增] > [專案] 以建立專案。
2.  在 [新增專案] 對話方塊中，選取 [Visual C#] > [Web] > [ASP.NET 核心 Web 應用程式 (.NET Core)]。
3.  為應用程式命名，例如 Contoso.AADB2C.UI，然後選取 [確定]。

    ![建立新的 Visual Studio 專案](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4.  選取 [Web 應用程式] 範本
5.  請確認驗證已設為**沒有驗證**

    ![選取 Web API 範本](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6.  按一下 [確定] 以建立專案

## <a name="step-2-create-mvc-view"></a>步驟 2：建立 MVC 檢視
### <a name="step-21-download-b2c-built-in-html5-template"></a>步驟 2.1 下載 B2C 內建 HTML5 範本
您的自訂 HTML5 範本是基於 B2C 內建 HTML5 範本。 您可以下載 [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml)，或透過[入門套件](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip)下載。 您可以對統一註冊或登入頁面使用 HTML5 檔案。

### <a name="step-22-add-mvc-view"></a>步驟 2.2 新增 MVC 檢視
1. 以滑鼠右鍵按一下 檢視/首頁 資料夾，然後按一下新增 > 新項目。
 ![新增 MVC 新項目](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)
2. 在 [新增項目 - Contoso.AADB2C.UI] 對話方塊中，選取 [Web] > [ASP] > [網路]
3. 點選 [MVC 檢視頁面]
4. 在 [名稱] 方塊中，將名稱變更為 _unified.cshtml_。
5. 按一下 [新增]
 [新增 MVC 檢視]![](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)
6.  如果此檔案尚未開啟，請按兩下檔案將它開啟。 清除 _unified.cshtml_ 的內容。
7. 基於示範目的，我們會移除對版面配置頁面的參考。 將下列程式碼片段新增至 _unified.cshtml_

    ```C#
    @{
        Layout = null;
    }
    ```

8. 開啟您從 AAD B2C 內建 HTML5 範本中下載的 _unified.cshtml_ 檔案。
9. 尋找 `@` 記號並以 `@@` 取代
10. 複製檔案的內容，並將它在版面配置定義下貼上。 您的程式碼應該看起來類似：![新增 HTML5 之後的 unified.cshtml 檔案](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)
### <a name="step-23-change-the-background-image"></a>步驟 2.3 變更背景影像
10. 找出具有識別碼 `background_background_image` 的 `<img>` 元素，並將 `src` 取代為 _https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1_，或您喜歡的任何其他背景影像。
![變更頁面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)
### <a name="step-24-add-you-view-to-mvc-controller"></a>步驟 2.4 將檢視新增至 MVC 控制器
開啟 **Controllers\HomeController.cs**，並新增下列方法。 
```C
public IActionResult unified()
{
    return View();
}
```
此程式碼會指定方法應使用檢視範本檔案來向瀏覽器呈現回應。 因為我們未明確指定檢視範本檔案的名稱，MVC 會預設使用 /Views/Home 資料夾中的 _unified.cshtml_ 檢視檔案。

新增 _unified_ 方法之後，您的程式碼看起來應該是：![對控制器進行變更來呈現檢視](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

偵錯您的 Web 應用程式，並確定 _unified_ 頁面可供存取。 例如， `http://localhost:<Port number>/Home/unified`

### <a name="step-25-publish-to-azure"></a>步驟 2.5：發佈至 Azure
1.  在 [方案總管] 中，以滑鼠右鍵按一下 **Contoso.AADB2C.UI** 專案，然後選取 [發佈]。

    ![發佈至 Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2.  確定已選取 [Microsoft Azure App Service]，然後選取 [發佈]。

    ![建立新的 Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    [建立 App Service] 對話方塊，可協助您建立在 Azure 中執行 ASP.NET Web 應用程式所需的 Azure 資源。

> [!NOTE]
>
>如需如何發佈的詳細資訊，請參閱[在 Azure 中建立 ASP.NET Web 應用程式](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)

3.  在 [Web 應用程式名稱] 中，輸入唯一的應用程式名稱 (有效字元為 `a-z`、`0-9` 和 `-`)。 Web 應用程式的 URL 是 `http://<app_name>.azurewebsites.NET`，其中 `<app_name>` 是您的 Web 應用程式名稱。 您可以接受自動產生的名稱 (這是唯一的)。

    選取 [建立] 開始建立 Azure 資源。

    ![提供 App Service 屬性](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

4.  精靈完成後，它會將 ASP.NET Web 應用程式發佈至 Azure，然後在預設瀏覽器中啟動該應用程式，複製_整合_頁面的 URL。 例如，_https://<app_name>.azurewebsites.net/home/unified_

## <a name="step-3-configure-cors-in-azure-app-service"></a>步驟 3：在 Azure App Service 中設定 CORS
1. 在瀏覽器中導覽至 [Azure 入口網站](https://portal.azure.com/)

2. 按一下 [應用程式服務] ，然後按一下您 API 應用程式的名稱。

    ![在入口網站中選取 API 應用程式](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

3. 在 [設定] 區段中，向下捲動並尋找 **API** 區段，然後按一下 **CORS**。

    ![選取 CORS 設定](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

4. 在文字方塊中，輸入您想要允許的一或多個 JavaScript 呼叫來源 URL。
或輸入星號 (*) 來指定接受所有的原始網域。

5. 按一下 [儲存] 。

    ![按一下 [Save] \(儲存)。](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

按一下 [儲存] 之後，API 應用程式會接受來自指定 URL 的 JavaScript 呼叫。 

## <a name="step-4-html5-template-validation"></a>步驟 4：HTML5 範本驗證
HTML5 範本即可供使用。  不過，它在任何 `ContentDefinition` 中無法使用。 將 `ContentDefinition` 新增到您的自訂原則之前，您必須：
* 確保內容符合 HTML5 規範且可供存取
* 確保內容伺服器已啟用 CORS。

>[!NOTE]
>
>若要確認您要用來裝載內容的網站已啟用 CORS 並測試 CORS 要求，您可以使用 http://test-cors.org/ 網站。 

* 提供的內容是透過 **HTTPS** 保護。
* 對所有連結和 CSS 內容與影像使用**絕對 URL**，例如 https://yourdomain/content。

## <a name="step-5-configure-your-content-definition"></a>步驟 5：設定您的內容定義
設定 `ContentDefinition`
1.  開啟原則的基本檔案 (例如，TrustFrameworkBase.xml)。
2.  尋找 `<ContentDefinitions>` 元素，並複製 `<ContentDefinitions>` 節點的完整內容。
3.  開啟擴充檔案 (例如，TrustFrameworkExtensions.xml)，並尋找 `<BuildingBlocks>` 元素。 如果此元素不存在，請新增。
4.  貼上您複製作為 `<BuildingBlocks>` 元素之子元素的 `<ContentDefinitions>` 節點的完整內容。 
5.  在您複製的 XML 中，尋找包含 `Id="api.signuporsignin"` 的 `<ContentDefinition>` 節點。
6.  將 `LoadUri` 的值從 _~/tenant/default/unified_ 變更為 _https://app_name.azurewebsites.net/home/unified_。您的自訂原則應該看起來類似：![您的內容定義](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>步驟 6：將原則上傳至您的租用戶
1.  在 [Azure 入口網站](https://portal.azure.com)中，切換至[您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後開啟 [Azure AD B2C]
2.  選取 [識別體驗架構]。
3.  開啟 [所有原則]。
4.  選取 [上傳原則]。
5.  勾選 [覆寫已存在的原則] 方塊。
6.  **上傳** TrustFrameworkExtensions.xml，並確定它通過驗證

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>步驟 7：使用 [立即執行] 測試自訂原則
1.  開啟 [Azure AD B2C 設定]，然後移至 [識別體驗架構]。

    >[!NOTE]
    >
    >    [立即執行] 需要在租用戶上至少預先註冊一個應用程式。 
    >    若要了解如何註冊應用程式，請參閱 Azure AD B2C [開始使用](active-directory-b2c-get-started.md)一文或[應用程式註冊](active-directory-b2c-app-registration.md)一文。
    >

2.  開啟 **B2C_1A_signup_signin**，此為您上傳的信賴憑證者 (RP) 自訂原則。 選取 [立即執行]。
3.  您應該能夠看到您的自訂 HTML5 具有您稍早建立的背景 ![您的註冊或登入原則](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-adding-dynamic-content"></a>步驟 8：新增動態內容
本節中，我們會根據名為 _campaignId_ 的查詢字串參數來變更背景。 信賴憑證者應用程式 (Web/行動裝置應用程式) 會將參數傳送至 AAD B2C。 您的原則會讀取參數，並將其值傳送至您的 HTML5 範本 


### <a name="step-81-adding-content-definition-parameter"></a>步驟 8.1 新增內容定義參數

若要新增 `ContentDefinitionParameters` 元素：
1.  開啟您的原則的 SignUpOrSignin 檔案 (例如，SignUpOrSignin.xml)。
2.  尋找 `<UserJourneyBehaviors>` 節點 
4.  在 `<UserJourneyBehaviors>` 內，新增下列 XML 程式碼片段 

    ```XML
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    </ContentDefinitionParameters>
    ```

### <a name="step-82-change-your-code-to-accept-query-string-parameter-and-replace-the-background-image-accordingly"></a>步驟 #8.2 將您的程式碼變更為接受查詢字串參數，並據以取代背景影像
在本節中，我們會修改 HomeController _unified_ 方法，以接受 campaignId 參數。 然後方法會檢查它的值，並據以設定 `ViewData["background"]` 變數。

1. 開啟 **Controllers\HomeController.cs**，並利用下列程式碼片段變更 `unified` 方法：

    ```C#
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. 找出具有識別碼 `background_background_image` 的 `<img>` 元素，並將 `src` 取代為 `@ViewData["background"]`。

    ![變更頁面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3 上傳變更並發佈您的原則
1. 將您的 Visual Studio 專案發佈至 Azure Service 應用程式
2. 上傳 SignUpOrSignin.xml 原則至 AAD B2C
3.  開啟 **B2C_1A_signup_signin**，此為您上傳的信賴憑證者 (RP) 自訂原則。 選取 [立即執行]。 您應該會看到與之前相同的背景影像。
4. 從瀏覽器的網址列複製 URL
5. 將 _campaignId_ 查詢字串參數新增至此 URI。 例如，新增 `&campaignId=hawaii`，如下圖所示 ![變更頁面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)
6. 按 **Enter**，您的頁面會顯示夏威夷背景 ![變更頁面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)
7. 現在，將值變更為 *Tokyo*，然後按 **Enter**。 頁面會顯示東京背景 ![變更頁面背景](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>步驟 9：變更使用者旅程圖的其餘部分
如果您瀏覽至登入頁面，然後按一下 [立即註冊] 連結，您會看到預設的背景，不是您定義的背景。 此行為是因為您只變更了註冊或登入頁面上，但是您也必須變更自行決定內容定義的其餘部分。 若要這樣做，請執行步驟：
* 在步驟 #2 中：
    * 下載 **selfasserted** 檔案。
    * 複製檔案內容。
    * 建立新的檢視 **selfasserted**。
    * 將 **selfasserted** 新增至 **Home** 控制器。
* 在步驟 #4 中 
    * 在您的擴充原則中，尋找包含 `Id="api.selfasserted"`、`Id="api.localaccountsignup"` 和 `Id="api.localaccountpasswordreset"` 的 `<ContentDefinition>` 節點
    *  將 `LoadUri` 屬性設定為您的 selfasserted URI。
* 在步驟 #8.2 中，將您的程式碼變更為接受查詢字串參數 
* 上傳 TrustFrameworkExtensions.xml 原則，並確定它通過驗證。
* 執行原則測試，然後按一下 [立即註冊] 來查看結果

## <a name="optional-download-the-complete-policy-files-and-code"></a>[選用] 下載完整的原則檔案和程式碼
* 建議您在使用自訂原則逐步解說完成使用者入門後，使用自己的自訂原則檔案來建置您的情節，而非使用這些範例檔案。  [參考的範例原則檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)
* 您可以在這裡下載完整程式碼：[供參考的範例 Visual Studio 方案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)




