---
title: "Azure Active Directory B2C：Weibo 設定 | Microsoft Docs"
description: "在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Weibo 帳戶的取用者提供註冊和登入。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1860de34-94cb-4ceb-851e-102f930f7230
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: 00c5d3781455c80b33bdbb4c872ae354531baf3e
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C：針對具有 Weibo 帳戶的取用者提供註冊和登入

> [!NOTE]
> 這項功能處於預覽狀態。 請勿在生產環境中使用此識別提供者。
> 

## <a name="create-a-weibo-application"></a>建立 Weibo 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 Weibo 做為識別提供者，您必須建立 Weibo 應用程式，並對其提供正確參數。 您需要 Weibo 帳戶才能執行此動作。 如果您沒有帳戶，您可以取得在 [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us) 上取得一個。

### <a name="register-for-the-weibo-developer-program"></a>註冊 Weibo 開發人員計劃

1. 移至 [Weibo 開發人員入口網站](http://open.weibo.com/)並以您的 Weibo 帳戶認證登入。
2. 登入之後，按一下您在右上角的顯示名稱。
3. 在下拉式清單中，選取 [编辑开发者信息] \(編輯開發人員資訊)。
4. 在表單中輸入所需的資訊，然後按一下 [提交]。
5. 完成電子郵件驗證程序。
6. 移至[識別驗證頁面](http://open.weibo.com/developers/identity/edit)。
7. 在表單中輸入所需的資訊，然後按一下 [提交]。

### <a name="register-a-weibo-application"></a>註冊 Weibo 應用程式

1. 移至[新的 Weibo 應用程式註冊頁面](http://open.weibo.com/apps/new)。
2. 輸入必要的應用程式資訊。
3. 按一下 [创建] \(建立)。
4. 複製**應用程式金鑰**和**應用程式密碼**的值。 稍後您將會需要此資訊。
5. 上傳所需的相片，並輸入所需的資訊。
6. 按一下 [保存以上信息] \(儲存)。
7. 按一下 [高级信息] \(進階資訊)。
8. 按一下 OAuth2.0 [授权设置] \(重新導向 URL) 旁的 [编辑] \(編輯) 欄位。
9. 針對 OAuth2.0 [授权设置] \(重新導向 URL) 輸入 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`。 例如，如果您的 `tenant_name` 是 contoso.onmicrosoft.com，請將 URL 設為 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
10. 按一下 [提交]。  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 Weibo 設為識別提供者
1. 遵循下列步驟以 [瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) (位於 Azure 入口網站上)。
2. 在 B2C 功能刀鋒視窗中，按一下 [ **身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [新增]  。
4. 針對身分識別提供者組態，提供容易辨識的 **名稱** 。 例如，輸入 "Weibo"。
5. 按一下 [識別提供者類型]、選取 [Weibo]，然後按一下 [確定]。
6. 按一下 [設定此識別提供者]。
7. 輸入您稍早複製為**用戶端識別碼**的**應用程式金鑰**。
8. 輸入您稍早複製為**用戶端密碼**的**應用程式密碼**。
9. 依序按一下 [確定] 和 [建立]，儲存您的 Weibo 設定。

