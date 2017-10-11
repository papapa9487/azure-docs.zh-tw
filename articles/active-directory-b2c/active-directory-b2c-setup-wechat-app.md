---
title: "Azure Active Directory B2C：WeChat 設定 | Microsoft Docs"
description: "在受 Azure Active Directory B2C 保護的應用程式中，針對具有 WeChat 帳戶的取用者提供註冊和登入。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d2424c66-ba68-4d82-847e-d137683527b0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: a54aec23d951610118246e9f70cdd27752ef39a6
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C：針對具有 WeChat 帳戶的取用者提供註冊和登入

> [!NOTE]
> 這項功能處於預覽狀態。
> 

## <a name="create-a-wechat-application"></a>建立 WeChat 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 WeChat 做為識別提供者，您必須建立 WeChat 應用程式，並對其提供正確參數。 您需要 WeChat 帳戶才能執行此動作。 如果您沒有帳戶，您可以透過它們的其中一個行動裝置應用程式來註冊，或使用您的 QQ 號碼，藉以取得一個帳戶。 之後，請利用 WeChat 開發人員計劃註冊您的帳戶。 您可以在 [這裡](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)找到詳細資訊。

### <a name="register-a-wechat-application"></a>註冊 WeChat 應用程式

1. 移至 [https://open.weixin.qq.com/](https://open.weixin.qq.com/) 並登入。
2. 按一下 [管理中心]。
3. 遵循註冊新應用程式的必要步驟。
4. 針對 [授权回调域] \(回呼 URL)，輸入 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`。 例如，如果您的 `tenant_name` 是 contoso.onmicrosoft.com，請將 URL 設為 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
5. 尋找並複製**應用程式識別碼**和**應用程式金鑰**。 稍後您將需要這些資訊。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 WeChat 設為識別提供者
1. 遵循下列步驟以 [瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) (位於 Azure 入口網站上)。
2. 在 B2C 功能刀鋒視窗中，按一下 [ **身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [新增]  。
4. 針對身分識別提供者組態，提供容易辨識的 **名稱** 。 例如，輸入 "WeChat"。
5. 按一下 [識別提供者類型]、選取 [WeChat]，然後按一下 [確定]。
6. 按一下 [設定此識別提供者]。
7. 輸入您稍早複製為**用戶端識別碼**的**應用程式金鑰**。
8. 輸入您稍早複製為**用戶端密碼**的**應用程式密碼**。
9. 依序按一下 [確定] 和 [建立]，儲存您的 WeChat 設定。

