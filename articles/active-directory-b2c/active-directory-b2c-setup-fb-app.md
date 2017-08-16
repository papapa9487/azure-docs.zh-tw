---
title: "Azure Active Directory B2C：Facebook 設定 | Microsoft Docs"
description: "在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Facebook 帳戶的取用者提供註冊和登入。"
services: active-directory-b2c
documentationcenter: 
author: sromeroz
manager: krassk
editor: sromeroz
ms.assetid: b875f235-a1d2-4abb-b9f0-b89beac38a32
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/7/2017
ms.author: sromeroz
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 8c2154fcf33537358b549395d15b4ba937371cd0
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C：針對具有 Facebook 帳戶的取用者提供註冊和登入
## <a name="create-a-facebook-application"></a>建立 Facebook 應用程式
若要在 Azure Active Directory (Azure AD) B2C 中使用 Facebok 做為身分識別提供者，您必須建立 Facebook 應用程式，並對其提供正確的參數。 您需要 Facebook 帳戶來執行此動作。 如果您沒有帳戶，您可以在 [https://www.facebook.com/](https://www.facebook.com/)上取得。

1. 前往 [Facebook for developers (開發人員專用的 Facebook)](https://developers.facebook.com/) 網站，並以您的 Facebook 帳戶認證登入。
2. 如果您尚未這麼做，您需要註冊為 Facebook 開發人員。 若要這樣做，請按一下 **[註冊]**  \(位於頁面右上角)、接受 Facebook 的原則，然後完成註冊步驟。
3. 按一下 [我的應用程式]，然後按一下 [新增應用程式]。 
4. 在表單中，提供**顯示名稱**和有效的**連絡人電子郵件**。
5. 按一下 [建立應用程式識別碼]。 這可能會要求您接受 Facebook 平台原則，並完成線上安全性檢查。
6. 在左欄中，按一下 [設定]，然後選取 [基本] \(如果尚未選取)。
7. 選取一個**類別**。 
8. 按一下 [+ 新增平台]，然後選取 [網站]。
   
    ![Facebook - 設定](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - 設定 - 網站](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. 在頁面底部的 [網站 URL] 欄位中輸入 `https://login.microsoftonline.com/`，然後按一下 [儲存變更]。
   
    ![Facebook - 網站 URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. 複製 [應用程式識別碼] 的值。 按一下 [顯示]，並複製 [應用程式密碼] 的值。 您必須同時使用這兩個值，將 Facebook 設定為您租用戶中的身分識別提供者。 **應用程式密碼**是重要的安全性認證。
   
    ![Facebook - 應用程式識別碼和應用程式密碼](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. 在左側導覽中按一下 [+ 新增產品]，然後按一下 [Facebook 登入] 的 [設定] 按鈕。
   
    ![Facebook - Facebook 登入](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. 在 [Facebook 登入] 下，按一下右側導覽中的 [設定]

    ![Facebook - Facebook 登入設定](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. 在 [用戶端 OAuth 設定] 區段的 [有效的 OAuth 重新導向 URI] 欄位中輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 使用您的租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 **{tenant}**。 按一下頁面底部的 [儲存變更]。
    
    ![Facebook - OAuth 重新導向 URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. 若要讓您的 Facebook 應用程式可供 Azure AD B2C 使用，您必須將其設定為對外公開。 若要執行此動作，請在左側導覽中按一下 [應用程式檢閱]，然後將頁面頂端的開關切換為 [是]，並按一下 [確認]。
    
    ![Facebook - App 公開](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>將 Facebook 設定為您租用戶中的身分識別提供者
1. 遵循下列步驟以 [瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) (位於 Azure 入口網站上)。
2. 在 B2C 功能刀鋒視窗中，按一下 [ **身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [新增]  。
4. 針對身分識別提供者組態，提供容易辨識的 **名稱** 。 例如，輸入「Facebook」。
5. 按一下 [識別提供者類型]、選取 [Facebook]，然後按一下 [確定]。
6. 按一下 [設定此識別提供者]，然後在 [用戶端識別碼] 與 [用戶端密碼] 欄位中，分別輸入您先前建立之 Facebook 應用程式的應用程式識別碼和應用程式密碼。
7. 依序按一下 [確定] 與 [建立]，以儲存您的 Facebook 設定。

> [!NOTE]
> 將「識別提供者」新增至您的租用戶不會修改您現有的原則。 請記得加入剛才建立的識別提供者，以更新您的原則。
>
