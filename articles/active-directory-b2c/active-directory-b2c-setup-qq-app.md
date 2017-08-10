---
title: "Azure Active Directory B2C：QQ 設定 | Microsoft Docs"
description: "在受 Azure Active Directory B2C 保護的應用程式中，針對具有 QQ 帳戶的取用者提供註冊和登入。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 18c2cf94-8004-4de1-81c2-e45be65ce12d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: b32e81494b8c84799485f154ae43ad30af394caa
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C：針對具有QQ 帳戶的取用者提供註冊和登入

> [!NOTE]
> 這項功能處於預覽狀態。
> 

## <a name="create-a-qq-application"></a>建立 QQ 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 QQ 做為識別提供者，您必須建立 QQ 應用程式，並對其提供正確參數。 您需要 QQ 帳戶才能執行此動作。 如果您沒有帳戶，您可以在 [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) 上取得一個。

### <a name="register-for-the-qq-developer-program"></a>註冊 QQ 開發人員計劃

1. 移至 [QQ 開發人員入口網站](http://open.qq.com)並以您的 QQ 帳戶認證登入。
2. 登入之後，請移至 [http://open.qq.com/reg](http://open.qq.com/reg)，將您自己註冊為開發人員。
3. 在功能表中，選取 [个人] \(個人開發人員)。
4. 在表單中輸入所需的資訊，然後按 [下一步]。
5. 完成電子郵件驗證程序。

> [!NOTE]
> 在註冊為開發人員之後，您必須等待數天以進行核准。 

### <a name="register-a-qq-application"></a>註冊 QQ 應用程式

1. 移至 [https://connect.qq.com/index.html](https://connect.qq.com/index.html)。
2. 按一下 [应用管理] \(應用程式管理)。
3. 按一下 [创建应用] \(建立應用程式)。
4. 輸入必要的應用程式資訊。
5. 按一下 [创建应用] \(建立應用程式)。
6. 輸入必要資訊。
7. 針對 [授权回调域] \(回呼 URL) 欄位，輸入 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`。 例如，如果您的 `tenant_name` 是 contoso.onmicrosoft.com，請將 URL 設為 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
8. 按一下 [创建应用] \(建立應用程式)。
9. 在 [確認] 頁面上，按一下 [应用管理] \(應用程式管理) 以返回應用程式管理頁面。
10. 在您剛建立的應用程式旁邊，按一下 [查看] \(檢視)。
11. 按一下 [修改] \(編輯)。
12. 從頁面頂端複製**應用程式識別碼**和**應用程式金鑰**。

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 QQ 設為識別提供者
1. 遵循下列步驟以 [瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) (位於 Azure 入口網站上)。
2. 在 B2C 功能刀鋒視窗中，按一下 [ **身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [新增]  。
4. 針對身分識別提供者組態，提供容易辨識的 **名稱** 。 例如，輸入 "QQ"。
5. 按一下 [識別提供者類型]、選取 [QQ]，然後按一下 [確定]。
6. 按一下 [設定此識別提供者]。
7. 輸入您稍早複製為**用戶端識別碼**的**應用程式金鑰**。
8. 輸入您稍早複製為**用戶端密碼**的**應用程式密碼**。
9. 依序按一下 [確定] 和 [建立]，儲存您的 QQ 設定。


