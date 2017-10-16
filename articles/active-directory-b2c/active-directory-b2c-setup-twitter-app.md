---
title: "Azure Active Directory B2C：Twitter 設定 | Microsoft Docs"
description: "在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Twitter 帳戶的取用者提供註冊和登入。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 579a6841-9329-45b8-a351-da4315a6634e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/06/2017
ms.author: parakhj
ms.openlocfilehash: 82a001dd53cdddcf3b360090f3250af593c96fbb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C：針對具有 Twitter 帳戶的取用者提供註冊和登入

> [!NOTE]
> 這項功能處於預覽狀態。
> 

## <a name="create-a-twitter-application"></a>建立 Twitter 應用程式
若要在 Azure Active Directory (Azure AD) B2C 中使用 Twitter 作為識別提供者，您必須建立 Twitter 應用程式，並對其提供正確參數。 您需要 Twitter 開發人員帳戶才能執行這項操作。 如果您沒有該帳戶，您可以在 [https://dev.twitter.com/](https://dev.twitter.com/) 上申請。

1. 移至 [Twitter 開發人員網站](https://dev.twitter.com/)，並以您的認證登入。
2. 按一下 工具和支援 底下的 我的應用程式，然後按一下建立新的應用程式。 
3. 在表單中，提供**名稱**、**描述**和**網站**值。
4. 在 [回呼 URL] 中輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 務必要使用您的租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 **{tenant}**。
5. 勾選方塊以同意 [開發人員合約]，然後按一下 [建立 Twitter 應用程式]。
6. 應用程式建立完成後，按一下 [金鑰和存取權杖]。
7. 複製 [取用者金鑰] 和 [取用者祕密] 的值。 您必須使用這兩個值，將 Twitter 設為租用戶中的身分識別提供者。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 Twitter 設為識別提供者
1. 遵循下列步驟以 [瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) (位於 Azure 入口網站上)。
2. 在 B2C 功能刀鋒視窗中，按一下 [ **身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [新增]  。
4. 針對身分識別提供者組態，提供容易辨識的 **名稱** 。 例如，輸入「Twitter」。
5. 按一下 [識別提供者類型]、選取 [Twitter]，然後按一下 [確定]。
6. 按一下 [設定此識別提供者]，然後輸入 Twitter 的**取用者金鑰**來作為**用戶端識別碼**，以及輸入 Twitter 的**取用者祕密**來作為**用戶端祕密**。
7. 依序按一下 [確定] 和 [建立]，儲存您的 Twitter 設定。

