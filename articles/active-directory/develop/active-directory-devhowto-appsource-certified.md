---
title: "如何讓 AppSource 取得 Azure Active Directory (AD) 認證 | Microsoft Docs"
description: "有關如何讓應用程式 AppSource 取得 Azure Active Directory 認證的詳細資料。"
services: active-directory
documentationcenter: 
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: d8e2f8fc19ff879e6a7b632f033fd0ed9d77392a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>如何讓 AppSource 取得 Azure Active Directory 認證
[Microsoft AppSource](https://appsource.microsoft.com/) 是企業用戶探索、嘗試，及管理企業營運 SaaS 應用程式 (獨立 SaaS 和現有 Microsoft SaaS 產品的附加元件) 的目的地。

若要列出 AppSource 上獨立的 SaaS 應用程式，您的應用程式必須接受來自具備 Azure Active Directory 之任何公司或組織的公司帳戶的單一登入。 登入流程必須使用 [OpenID Connect](./active-directory-protocols-openid-connect-code.md) 或 [OAuth 2.0](./active-directory-protocols-oauth-code.md) 通訊協定。 AppSource 認證不接受 SAML 整合。

## <a name="guides-and-code-samples"></a>指南與程式碼範例
如果您想要了解如何使用 Open ID connect 將您的應用程式與 Azure Active Directory 整合，請遵循 [Azure Active Directory 開發人員指南](./active-directory-developers-guide.md#get-started "開始使用適用於開發人員的 Azure AD") 中的指南和程式碼範例。

## <a name="multi-tenant-applications"></a>多租用戶應用程式

此應用程式接受來自具備 Azure Active Directory 之公司或組織的使用者的單一登入，不需要稱為*多租用戶應用程式*的個別執行個體、設定或部署。 AppSource 建議應用程式實行多重租用，以啟用*單鍵*免費試用體驗。

若要在您的應用程式上啟用多重租用：
- 在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 的應用程式註冊資訊中，將 `Multi-Tenanted` 屬性設為 `Yes` (依預設，Azure 入口網站中建立的應用程式會設為*單一租用戶*)
- 更新您的程式碼，以傳送要求至 '`common`' 端點 (將來自 *https://login.microsoftonline.com/{yourtenant}* 的端點更新為 *https://login.microsoftonline.com/common*)
- 在特定平台上 (例如 ASP.NET)，您還需要更新程式碼，以接受多個簽發者

如需多重租用的詳細資訊，請參閱：[如何使用多租用戶應用程式模式登入任何 Azure Active Directory (AD) 使用者](./active-directory-devhowto-multi-tenant-overview.md)。

### <a name="single-tenant-applications"></a>單一租用戶應用程式
此應用程式僅接受來自定義之 Azure Active Directory 執行個體 (稱為*單一租用戶應用程式*) 的使用者登入。 將每個使用者以*來賓帳戶*的身分新增至應用程式註冊的 Azure Active Directory 執行個體之後，外部使用者 (包括來自其他組織的公司或學校帳戶，或個人帳戶) 可以登入單一租用戶應用程式。 您可以透過 [*Azure AD B2B 共同作業*](../active-directory-b2b-what-is-azure-ad-b2b.md)將使用者以來賓帳戶的身分新增至 Azure Active Directory - 而且可以使用[程式設計的方式](../active-directory-b2b-code-samples.md)來完成。 當您以來賓帳戶的身分，將使用者新增至 Azure Active Directory 時，會向使用者寄送邀請電子郵件，使用者必須按一下邀請電子郵件中的連結，才可接受邀請。 如果傳送邀請給邀請組織中的其他使用者也屬於合作夥伴組織的成員，則不需要接受邀請即可登入。

單一租用戶應用程式可啟用*與我連絡*體驗，但如果您想要啟用 AppSource 建議的單鍵/免費試用體驗，可改在您的應用程式上啟用多重租用。


## <a name="appsource-trial-experiences"></a>AppSource 試用體驗

### <a name="free-trial-customer-led-trial-experience"></a>免費試用 (以客戶為導向的試用體驗) 
*以客戶為導向的試用*是 AppSource 建議的體驗，因為可提供單鍵存取至您的應用程式。 這項體驗看起來如下圖：<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>使用者會在 AppSource 網站中尋找您的應用程式</li><li>選取 [免費試用] 選項</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource 將使用者重新導向至網站中的 URL</li><li>您的網站會自動啟動<i>單一登入</i>流程 (在頁面載入時)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>使用者已重新導向至 Microsoft 登入頁面</li><li>使用者提供認證以登入</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>使用者同意您的應用程式</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>登入完成，而且使用者已重新導向回到您的網站</li><li>使用者啟動免費試用</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>與我連絡 (以合作夥伴為導向的試用體驗)
需要手動或長期操作來佈建使用者/公司時，可使用*合作夥伴試用體驗*：例如，您的應用程式需要佈建虛擬機器、資料庫執行個體，或需要很多時間完成的作業的時候。 在此情況下，使用者選取 [要求試用] 按鈕並填寫表單之後，AppSource 會傳送給您使用者的連絡資訊。 收到此資訊後，您將佈建環境，並將如何存取試用體驗的指示傳送給使用者：<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>使用者會在 AppSource 網站中尋找您的應用程式</li><li>選取 [與我連絡] 選項</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>將連絡人資訊填入表單</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>您會收到使用者資訊</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>設定環境</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>連絡使用者試用資訊</td>
        </tr>
        </table><br/><br/>
        <ul><li>您收到使用者資訊並設定試用執行個體</li><li>將存取您應用程式的超連結傳送給使用者</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>使用者存取您的應用程式，並完成單一登入流程</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>使用者同意您的應用程式</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>登入完成，而且使用者已重新導向回到您的網站</li><li>使用者啟動免費試用</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>詳細資訊
如需 AppSource 試用體驗的詳細資訊，請參閱[這段影片](https://aka.ms/trialexperienceforwebapps)。 
 
## <a name="next-steps"></a>後續步驟

- 如需建立支援 Azure Active Directory 登入之應用程式的詳細資訊，請參閱 [Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- 如需如何列出 AppSource 中的 SaaS 應用程式的資訊，請參閱 [AppSource 合作夥伴資訊](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>取得支援
對於 Azure Active Directory 整合，我們使用 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory) 與社群，以提供支援。 

我們強烈建議您先在 Stack Overflow 上詢問您的問題，並瀏覽現有的問題，以查看先前是否有人已提出您的問題。 請確定您的問題或意見已標記為 `[azure-active-directory]`。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->