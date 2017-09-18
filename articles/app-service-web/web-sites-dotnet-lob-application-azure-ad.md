---
title: "使用 Azure Active Directory 驗證建立企業營運 Azure 應用程式 | Microsoft Docs"
description: "了解如何在 Azure App Service 中建立使用 Azure Active Directory 進行驗證的 ASP.NET MVC 特定業務應用程式"
services: app-service\web, active-directory
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: ad947bdb-4463-43ff-a5e3-91d9b2169b60
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 09/01/2016
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 209766e4ebc902d78cf0c83fa8fe0f57c4da73e4
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="create-a-line-of-business-azure-app-with-azure-active-directory-authentication"></a>使用 Azure Active Directory 驗證建立企業營運 Azure 應用程式
本文說明如何在 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 中使用[驗證/授權](../app-service/app-service-authentication-overview.md)功能建立 .NET 企業營運應用程式。 文中也會說明如何在應用程式中使用 [Azure Active Directory 圖形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) 來查詢目錄資料。

您所使用的 Azure Active Directory 租用戶可以是純 Azure 目錄。 或者，它可以[與您的內部部署 Active Directory 同步處理](../active-directory/active-directory-aadconnect.md)，以便為內部部署和遠端的工作者建立單一登入體驗。 本文使用 Azure 帳戶的預設目錄。

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>將建置的項目
您將在應用程式服務 Web Apps 中建置簡單的「建立/讀取/更新/刪除」(CRUD) 特定業務應用程式，它使用下列功能追蹤工作項目：

* 比對 Azure Active Directory 驗證使用者
* 使用 [Azure Active Directory 圖形 API](http://msdn.microsoft.com/library/azure/hh974476.aspx)
* 使用 ASP.NET MVC 的「不需要驗證」  範本

如果您在 Azure 中的企業營運應用程式需要角色型存取控制 (RBAC)，請參閱 [後續步驟](#next)。

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>您需要什麼
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

您需要下列項目完成本教學課程：

* Azure Active Directory 租用戶與不同的群組中的使用者
* 在 Azure Active Directory 租用戶建立應用程式的權限
* Visual Studio 2013 Update 4 或更新版本
* [Azure SDK 2.8.1 或更新版本](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>

## <a name="create-and-deploy-a-web-app-to-azure"></a>對 Azure 建立和部署 Web 應用程式
1. 從 Visual Studio，按一下 [檔案] > [新增] > [專案]。
2. 選取 [ASP.NET Web 應用程式]、為專案命名，然後按一下 [確定]。
3. 選取 [MVC] 範本，然後將驗證變更為 [不需要驗證]。 確定已選取 [雲端中的主機]，然後按一下 [確定]。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)
4. 在 [建立 App Service] 對話方塊中，按一下 [新增帳戶]\(然後是下拉式清單中的 [新增帳戶]) 來登入 Azure 帳戶。
5. 登入後，請設定 Web 應用程式。 建立資源群組和新的 App Service 方案，方法是按一下各自的 [新增]  按鈕。 按一下 [瀏覽其他 Azure 服務]  以繼續。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)
6. 在 [服務] 索引標籤上，按一下 **[+]** 來為應用程式新增 SQL Database。 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)
7. 在 [設定 SQL Database] 中，按一下 [新增] 以建立 SQL Server 執行個體。
8. 在 [設定 SQL Server] 中，設定 SQL Server 執行個體。 然後依序按一下 [確定]、[確定] 和 [建立]，開始在 Azure 中建立應用程式。
9. 在 [Azure App Service 活動] 中，當應用程式建立完成時您便會看到。 按一下 **[立即將&lt;應用程式名稱**> 發佈至此 Web 應用程式]，然後按一下[發佈]。 
   
    當 Visual Studio 完成後，它會在瀏覽器中開啟發佈應用程式。 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>

## <a name="configure-authentication-and-directory-access"></a>設定驗證和目錄存取
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左側功能表中，按一下 [應用程式服務] > **&lt;*應用程式名稱*>** > [驗證/授權]。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)
3. 按一下 [於] > [Azure Active Directory] > [Express] > [確定] 以開啟 Azure Active Directory 驗證。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)
4. 按一下命令列中的 [儲存]  。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)
   
    成功儲存驗證設定後，請嘗試在瀏覽器中再次瀏覽至應用程式。 預設設定會強制對整個應用程式執行驗證。 如果您還未登入，系統會將您重新導向至登入畫面。 登入之後，您會看到應用程式已受到 HTTPS 的保護。 接著，您必須啟用目錄資料的存取權。 
5. 瀏覽至 [傳統入口網站](https://manage.windowsazure.com)。
6. 從左側功能表中，按一下 [Active Directory] > [預設目錄] > [應用程式] > **&lt;*應用程式名稱*>**。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)
   
    這是 App Service 為您建立以便啟用授權/驗證功能的 Azure Active Directory 應用程式。
7. 按一下 [使用者] 和 [群組] 以確定目錄中有一些使用者和群組。 如果沒有，請建立一些測試使用者和群組。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)
8. 按一下 [設定]  以設定此應用程式。
9. 向下捲動至 [金鑰]  區段，並選取持續時間來新增金鑰。 然後按一下 [委派的權限] 並選取 [讀取目錄資料]。 
   按一下 [儲存] 。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)
10. 設定儲存好之後，往回捲動至 [金鑰] 區段，然後按一下 [複製] 按鈕以複製用戶端金鑰。 
    
     ![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)
    
    > [!IMPORTANT]
    > 如果您現在離開此頁面，就無法再存取此用戶端金鑰。
    > 
    > 
11. 接著，您必須使用此金鑰設定 Web 應用程式。 使用 Azure 帳戶登入 [Azure 資源總管](https://resources.azure.com) 。
12. 在頁面頂端按一下 [讀取/寫入]  以在 Azure 資源總管中進行變更。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)
13. 尋找應用程式的驗證設定，位置在 subscriptions > **&lt;*subscriptionname*>** > **resourceGroups** > **&lt;*resourcegroupname*>** > **providers** > **Microsoft.Web** > **sites** > **&lt;*appname*>** > **config** > **authsettings**。
14. 按一下 [ **編輯**]。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)
15. 在編輯窗格中，設定 `clientSecret` 和 `additionalLoginParams` 屬性，如下所示。
    
        ...
        "clientSecret": "<client key from the Azure Active Directory application>",
        ...
        "additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
        ...
16. 按一下頂端的 [PUT]  以提交變更。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)
17. 現在，若要測試是否有可供存取 Azure Active Directory 圖形 API 的授權權杖，請直接在瀏覽器中瀏覽到 **https://&lt;*appname*>.azurewebsites.net/.auth/me**。 如果一切設定皆正確，您應該會在 JSON 回應中看到 `access_token` 屬性。
    
    `~/.auth/me` URL 路徑由 App Service 驗證/授權進行管理，以提供您與驗證的工作階段相關的所有資訊。 如需詳細資訊，請參閱 [Azure App Service 中的驗證與授權](../app-service/app-service-authentication-overview.md)。
    
    > [!NOTE]
    > `access_token` 具有到期期間。 不過，App Service 驗證/授權會以 `~/.auth/refresh`提供權杖重新整理功能。 如需其使用方式的詳細資訊，請參閱 [App Service 權杖存放區](https://cgillum.tech/2016/03/07/app-service-token-store/)。
    > 
    > 

接下來，您會使用目錄資料進行一些有用的操作。

<a name="bkmk_crud"></a>

## <a name="add-line-of-business-functionality-to-your-app"></a>將企業營運功能新增至應用程式
現在，您可以建立簡單的 CRUD 工作項目追蹤器。  

1. 在 ~\Models 資料夾中，建立稱為 WorkItem.cs 的類別檔案，並以下列程式碼取代 `public class WorkItem {...}`：
   
     using System.ComponentModel.DataAnnotations;
   
     public class WorkItem   {
   
         [Key]
         public int ItemID { get; set; }
         public string AssignedToID { get; set; }
         public string AssignedToName { get; set; }
         public string Description { get; set; }
         public WorkItemStatus Status { get; set; }
     }
   
     public enum WorkItemStatus   {
   
         Open,
         Investigating,
         Resolved,
         Closed
     }
2. 建置專案，讓 Visual Studio 中的建構邏輯可存取新的模型。
3. 將新的建構項目 `WorkItemsController` 新增至 ~\Controllers 資料夾 (以滑鼠右鍵按一下 [控制器]、指向 [新增]，然後選取 [新增建構項目])。 
4. 選取 [使用 Entity Framework，包含檢視的 MVC 5 控制器]，再按一下 [新增]。
5. 選取您建立的模型，接著依序按一下 **[+]** 和 [新增] 來新增資料內容，然後按一下 [新增]。
   
   ![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)
6. 在 ~\Views\WorkItems\Create.cshtml (自動建構的項目) 中尋找 `Html.BeginForm` Helper 方法，並進行下列醒目提示的變更︰  
   
   <pre class="prettyprint">
   @model WebApplication1.Models.WorkItem
   
   @{
    ViewBag.Title = &quot;Create&quot;;
   }
   
   &lt;h2&gt;Create&lt;/h2&gt;
   
   @using (Html.BeginForm(<mark>&quot;Create&quot;, &quot;WorkItems&quot;, FormMethod.Post, new { id = &quot;main-form&quot; }</mark>)) 
   {
    @Html.AntiForgeryToken()
   
    &lt;div class=&quot;form-horizontal&quot;&gt;
        &lt;h4&gt;WorkItem&lt;/h4&gt;
        &lt;hr /&gt;
        @Html.ValidationSummary(true, &quot;&quot;, new { @class = &quot;text-danger&quot; })
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToID, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = &quot;form-control&quot;<mark>, @type = &quot;hidden&quot;</mark> } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.Description, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;form-control&quot; })
                @Html.ValidationMessageFor(model =&gt; model.Status, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            &lt;div class=&quot;col-md-offset-2 col-md-10&quot;&gt;
                &lt;input type=&quot;submit&quot; value=&quot;Create&quot; class=&quot;btn btn-default&quot;<mark> id=&quot;submit-button&quot;</mark> /&gt;
            &lt;/div&gt;
        &lt;/div&gt;
    &lt;/div&gt;
   }
   
   &lt;div&gt;
    @Html.ActionLink(&quot;Back to List&quot;, &quot;Index&quot;)
   &lt;/div&gt;
   
   @section Scripts {
    @Scripts.Render(&quot;~/bundles/jqueryval&quot;)
    <mark>&lt;script&gt;
        // People/Group Picker Code
        var maxResultsPerPage = 14;
        var input = document.getElementById(&quot;AssignedToName&quot;);
   
        // Access token from request header, and tenantID from claims identity
        var token = &quot;@Request.Headers[&quot;X-MS-TOKEN-AAD-ACCESS-TOKEN&quot;]&quot;;
        var tenant =&quot;@(System.Security.Claims.ClaimsPrincipal.Current.Claims
                        .Where(c => c.Type == &quot;http://schemas.microsoft.com/identity/claims/tenantid&quot;)
                        .Select(c => c.Value).SingleOrDefault())&quot;;
   
        var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
   
        // Submit the selected user/group to be asssigned.
        $(&quot;#submit-button&quot;).click({ picker: picker }, function () {
            if (!picker.Selected())
                return;
            $(&quot;#main-form&quot;).get()[0].elements[&quot;AssignedToID&quot;].value = picker.Selected().objectId;
        });
    &lt;/script&gt;</mark>
   }
   </pre>
   
   請注意，`AadPicker` 物件會使用 `token` 和 `tenant` 來進行 Azure Active Directory 圖形 API 呼叫。 您稍後會新增 `AadPicker` 。     
   
   > [!NOTE]
   > 您也可以使用 `~/.auth/me`直接用戶端取得 `token` 和 `tenant`，但這會是額外的伺服器呼叫。 例如：
   > 
   > $.ajax({ dataType: "json", url: "/.auth/me", success: function (data) { var token = data[0].access_token; var tenant = data[0].user_claims .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid') .val; } });
   > 
   > 
7. 對 ~\Views\WorkItems\Edit.cshtml 進行相同的變更。
8. `AadPicker` 物件定義在您必須新增至專案的指令碼中。 在 ~\Scripts 資料夾上按一下滑鼠右鍵、指向 [新增]，然後按一下 [JavaScript 檔案]。 輸入 `AadPickerLibrary` 做為檔名，然後按一下 [確定]。
9. 從[這裡](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js)將內容複製到 ~\Scripts\AadPickerLibrary.js。
   
   在指令碼中， `AadPicker` 物件會呼叫 [Azure Active Directory 圖形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) 以搜尋符合輸入的使用者與群組。  
10. ~\Scripts\AadPickerLibrary.js 也會使用 [jQuery UI 自動完成 Widget](https://jqueryui.com/autocomplete/)。 因此，您需要將 jQuery UI 新增至專案。 以滑鼠右鍵按一下專案，然後按一下 [管理 NuGet 套件] 。
11. 在 NuGet 套件管理員中按一下 [瀏覽]，在搜尋列中輸入 **jquery-ui**，然後按一下 [jQuery.UI.Combined]。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)
12. 在右窗格中按一下 [安裝]，然後按一下 [確定] 繼續進行。
13. 開啟 ~\App_Start\BundleConfig.cs，然後進行下列醒目提示的變更︰  
    
    <pre class="prettyprint">
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;).Include(
                    &quot;~/Scripts/jquery-{version}.js&quot;<mark>,
                    &quot;~/Scripts/jquery-ui-{version}.js&quot;,
                    &quot;~/Scripts/AadPickerLibrary.js&quot;</mark>));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;).Include(
                    &quot;~/Scripts/jquery.validate*&quot;));
    
        // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
        // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
        bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;).Include(
                    &quot;~/Scripts/modernizr-*&quot;));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;).Include(
                    &quot;~/Scripts/bootstrap.js&quot;,
                    &quot;~/Scripts/respond.js&quot;));
    
        bundles.Add(new StyleBundle(&quot;~/Content/css&quot;).Include(
                    &quot;~/Content/bootstrap.css&quot;,
                    &quot;~/Content/site.css&quot;<mark>,
                    &quot;~/Content/themes/base/jquery-ui.css&quot;</mark>));
    }
    </pre>
    
    有更多高效能的方式可管理應用程式中的 JavaScript 和 CSS 檔案。 不過，為了簡單起見，你只會利用載入了每個檢視的套組。
14. 最後，在 ~\Global.asax 中，於 `Application_Start()` 方法內新增下面這一行程式碼。 `Ctrl`+`.` 來加以修正。
    
        AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
    
    > [!NOTE]
    > 您需要這行程式碼，因為預設的 MVC 範本會在某些動作上使用 <code>[ValidateAntiForgeryToken]</code> 裝飾。 基於 [Brock Allen](https://twitter.com/BrockLAllen) 在 [MVC 4、AntiForgeryToken 和宣告](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/)中描述的行為，您的 HTTP POST 可能無法通過防偽權杖驗證，因為：
    > 
    > * Azure Active Directory 不會傳送防偽權杖預設所需要的 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider。
    > * 如果 Azure Active Directory 目錄與 AD FS 進行同步處理，AD FS 信任預設不會傳送 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider 宣告，但您可以手動設定 AD FS 來傳送此宣告。
    > 
    > `ClaimTypes.NameIdentifies` 指定宣告 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`，而 Azure Active Directory 確實會提供此宣告。  
    > 
    > 
15. 現在，發佈您的變更。 以滑鼠右鍵按一下專案，然後按一下 [發佈] 。
16. 按一下 [設定]，確定其中有 SQL Database 的連接字串，選取 [更新資料庫] 以變更模型的結構描述，然後按一下 [發佈]。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)
17. 在瀏覽器中，瀏覽至 https://&lt;appname>.azurewebsites.net/workitems，然後按一下 [建立新的]。
18. 按一下 [AssignedToName]  方塊。 現在您應該可以在下拉式清單中看到 Azure Active Directory 租用戶中的使用者和群組。 您可以輸入以進行篩選，或使用 `Up` 或 `Down` 鍵，或是按一下以選取使用者或群組。 
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)
19. 按一下 [建立]  以儲存變更。 然後，在建立的工作項目上按一下 [編輯]  來觀察相同的行為。

恭喜，您現在已使用目錄存取在 Azure 中執行企業營運應用程式！ 圖形 API 還有其他更多用途。 請參閱 [Azure AD 圖形 API 參考](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog)。

<a name="next"></a>

## <a name="next-step"></a>後續步驟
如果您在 Azure 中的企業營運應用程式需要角色型存取控制 (RBAC)，請參閱 [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) 以取得 Azure Active Directory 團隊提供的範例。 它會說明如何為 Azure Active Directory 應用程式啟用角色，然後使用 `[Authorize]` 裝飾授權使用者。

<!-- If your line-of-business app needs access to on-premises data, see 
[Access on-premises resources using hybrid connections in Azure App Service](web-sites-hybrid-connection-get-started.md). -->

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>進一步資源
* [Azure App Service 中的驗證與授權](../app-service/app-service-authentication-overview.md)
* [在 Azure 應用程式中使用內部部署 Active Directory 進行驗證](web-sites-authentication-authorization.md)
* [在 Azure 中使用 AD FS 驗證建立企業營運應用程式](web-sites-dotnet-lob-application-adfs.md)
* [App Service 驗證和 Azure AD 圖形 API](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
* [Microsoft Azure Active Directory 範例與文件](https://github.com/AzureADSamples)
* [Azure Active Directory 支援的權杖和宣告類型](http://msdn.microsoft.com/library/azure/dn195587.aspx)

