---
title: "Azure AD v2 ASP.NET Web 伺服器快速入門 - 測試 | Microsoft Docs"
description: "使用 OpenID Connect 標準，搭配傳統網頁瀏覽器型應用程式，在 ASP.NET 方案上實作 Microsoft 登入"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: bfa2563a6a58370d9a611440017441a751b46244
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>測試您的程式碼

按 `F5` 以在 Visual Studio 中執行您的專案。 瀏覽器將會開啟並將您導向至 *http://localhost:{port}*，其中會顯示 [使用 Microsoft 登入] 按鈕。 接著請按一下該按鈕來登入。

當您準備好進行測試時，請使用公司或學校 (Azure Active Directory)，或個人 (live.com、outlook.com) 帳戶登入。 

![[使用 Microsoft 登入] 瀏覽器視窗](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![[使用 Microsoft 登入] 瀏覽器視窗](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>預期的結果
登入之後，系統會將使用者重新導向至您網站的首頁，也就是您在 Microsoft 應用程式註冊入口網站中在應用程式註冊資訊中指定的 HTTPS URL。 此網頁現在會顯示「{使用者} 您好」 和一個登出連結，以及一個用於查看使用者宣告的連結 (這是之前所建立之授權控制器的連結)。

### <a name="see-users-claims"></a>查看使用者的宣告
選取超連結來查看使用者的宣告。 這會帶您前往只會向已驗證使用者提供的控制器和檢視。

#### <a name="expected-results"></a>預期的結果
 您應該會看到一個表格，其中包含已登入使用者的基本屬性：

| 屬性 | 值 | 描述|
|---|---|---|
| 名稱 | {使用者完整名稱} | 使用者的名字和姓氏
|使用者名稱 | <span>user@domain.com</span>| 用來識別已登入使用者的使用者名稱
| 主體| {主體}|用來跨網站唯一識別使用者登入的字串|
| 租用戶識別碼| {Guid}| 唯一代表使用者之 Azure Active Directory 組織的 *guid*。|

此外，您也會看到一個表格，其中包含驗證要求中包括的所有使用者宣告。 如需識別碼權杖中所有宣告的清單和說明，請參閱這篇[文章]：(https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "識別碼權杖的宣告清單")。


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>對具有 *[Authorize]* 屬性 (選用) 的方法進行存取測試
在這個步驟中，您將會以匿名使用者的身分對已驗證的控制器進行存取測試：<br/>
選取連結以將使用者登出，並完成登出程序。<br/>
現在在您的瀏覽器中輸入 http://localhost:{port}/authenticated 來存取您使用 `[Authorize]` 屬性保護的控制器

#### <a name="expected-results"></a>預期的結果
您應該會收到提示，要求您進行驗證以查看檢視。

## <a name="additional-information"></a>其他資訊

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>保護您的整個網站
若要保護您的整個網站，請將 `AuthorizeAttribute` 新增至 `Global.asax` `Application_Start` 方法中的 `GlobalFilters`：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **如何只將來自一個組織的使用者限制為登入您的應用程式**

> 根據預設值，下列帳戶可以登入您的應用程式：個人帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及來自已經整合 Azure Active Directory 之公司或組織的公司與學校帳戶。 

> 如果您想要讓應用程式只接受來自某一個 Azure Active Directory 組織的登入，請將 *web.config* 中的 `Tenant` 參數從 `Common` 取代為組織的租用戶名稱 (例如 *contoso.onmicrosoft.com*)。完成之後，請將您 [OWIN 啟動類別] 中的 `ValidateIssuer` 引數變更為 `true`。

> 若只要允許來自某一特定組織清單的使用者登入，請將 `ValidateIssuer` 設定為 true 並使用 `ValidIssuers` 參數指定組織清單。

> 另一個選項是實作自訂方法，使用 IssuerValidator 參數驗證簽發者。 如需 `TokenValidationParameters` 的詳細資訊，請參閱[這篇 ](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN 文章") MSDN 文章。

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]