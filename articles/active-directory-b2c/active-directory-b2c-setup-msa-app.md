---
title: "Azure Active Directory B2C：Microsoft 帳戶設定 | Microsoft Docs"
description: "在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Microsoft 帳戶的取用者提供註冊和登入。"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 06407322-142c-4cb3-9106-a8d752c4c853
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 59879dc0b3fc1d7af3e2a1f67f1701f451de9126
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C：針對具有 Microsoft 帳戶的取用者提供註冊和登入
## <a name="create-a-microsoft-account-application"></a>建立 Microsoft 帳戶應用程式
若要在 Azure Active Directory (Azure AD) B2C 中使用 Microsoft 帳戶做為識別提供者，您必須建立 Microsoft 帳戶應用程式，然後對其提供正確參數。 您需要 Microsoft 帳戶才能執行此動作。 如果您沒有該帳戶，您可以在 [https://www.live.com/](https://www.live.com/)上申請。

1. 移至 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，並使用您的 Microsoft 帳戶認證登入。
2. 按一下 [新增應用程式] 。
   
    ![Microsoft 帳戶 - 加入新的應用程式](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. 為應用程式提供 [名稱]，然後按一下 [建立應用程式]。
   
    ![Microsoft 帳戶 - 應用程式名稱](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. 複製 [應用程式識別碼] 的值。您必須使用此值，將 Microsoft 帳戶於租用戶中設定為識別提供者。
   
    ![Microsoft 帳戶 - 應用程式識別碼](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. 按一下 [新增平台]，然後選擇 [Web]。
   
    ![Microsoft 帳戶 - 新增平台](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Microsoft 帳戶 - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. 在 [重新導向 URI] 欄位中輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 使用您的租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 **{tenant}**。
   
    ![Microsoft 帳戶 - 重新導向 URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. 按一下 [應用程式密碼] 區段底下的 [產生新密碼]。 複製螢幕上顯示的新密碼。 您必須使用此值，將 Microsoft 帳戶於租用戶中設定為識別提供者。 此密碼是重要的安全性認證。
   
    ![Microsoft 帳戶 - 產生新密碼](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Microsoft 帳戶 - 新密碼](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. 勾選 [進階選項] 區段底下顯示 [Live SDK 支援] 的方塊。 按一下 [儲存] 。
   
    ![Microsoft 帳戶 - Live SDK 支援](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>將 Microsoft 帳戶於租用戶中設定為識別提供者
1. 遵循下列步驟以 [瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) (位於 Azure 入口網站上)。
2. 在 B2C 功能刀鋒視窗中，按一下 [ **身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [新增]  。
4. 針對身分識別提供者組態，提供容易辨識的 **名稱** 。 例如，輸入 "MSA"。
5. 按一下 [識別提供者類型]、選取 [Microsoft 帳戶]，然後按一下 [確定]。
6. 按一下 [設定此身分識別提供者]  ，然後輸入您先前建立的 Microsoft 帳戶應用程式的應用程式識別碼和密碼。
7. 依序按一下 [確定] 與 [建立]，以儲存您的 Microsoft 帳戶設定。

