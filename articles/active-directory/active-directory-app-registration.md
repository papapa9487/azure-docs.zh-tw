---
title: "Azure Active Directory 應用程式註冊 | Microsoft Docs"
description: "本文說明如何使用 Azure 入口網站向 Azure Active Directory 註冊應用程式"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: priyamo
ms.reviewer: elisol
ms.openlocfilehash: 2f2817688beb2028fd0bba8522827d87a0097f21
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>向 Azure Active Directory 租用戶註冊您的應用程式

您可以使用 Azure 入口網站向 Azure Active Directory (Azure AD) 租用戶註冊應用程式。 這會為應用程式建立應用程式識別碼，並加以啟用以接收權杖。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在左側導覽窗格中，選擇 [更多服務]，按一下 [應用程式註冊]，然後按一下 [新增]。
4. 遵照提示進行，並建立新的應用程式。 如果您想要 Web 應用程式或原生應用程式的特定範例，請查看我們的[快速入門](active-directory-developers-guide.md)。
  * 若為 Web 應用程式，請提供屬於應用程式基礎 URL 的**登入 URL**以供使用者登入，例如 `http://localhost:12345`。
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * 若為原生應用程式，請提供 [重新導向 URI]，以供 Azure AD 用來傳回權杖回應。 輸入應用程式特定的值，例如 `http://MyFirstAADApp`
5. 完成註冊後，Azure AD 會為您的應用程式指派一個唯一用戶端識別碼 (應用程式識別碼)。

## <a name="update-application-settings-from-the-azure-portal"></a>從 Azure 入口網站更新應用程式設定

您可以使用 Azure 入口網站，輕鬆地修改現有應用程式的設定。 例如，您可以設定回覆 URL，也就是 Azure AD 發出權杖回應的位置。 您也可以設定其他應用程式的權限，例如允許您的應用程式存取 Microsoft 圖形 API。 您可以透過應用程式設定頁面來執行這一切。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在左側導覽窗格中，選擇 [更多服務]，按一下 [應用程式註冊]，然後從清單中選取您的應用程式。
4. 按一下 [設定] 以開啟應用程式的設定頁面。
  * [屬性] 頁面可讓您修改應用程式的一般資訊。 這包括應用程式名稱、登入 URL 以及登出 URL。
  * [回覆 URL] 頁面可讓您新增回覆 URL，也就是 Azure AD 傳送權杖回應的位置。
  * [擁有者] 頁面可讓您新增應用程式擁有者。
  * [權限] 頁面可讓您設定應用程式的權限。 例如，若要存取 Microsoft 圖形 API，請按一下 [新增] 並在 API 選取器中選取 [Microsoft Graph]，然後選擇所需的權限，例如 [讀取目錄資料]。
  * [金鑰] 頁面可讓您新增應用程式密碼。 密碼只會在建立後立即顯示一次，請務必加以複製以便進一步使用。

## <a name="use-the-inline-manifest-editor"></a>使用內嵌資訊清單編輯器

您可以使用內嵌資訊清單編輯器，修改未在 Azure 入口網站中直接公開的某些應用程式屬性。 例如，您可以使用它來修改應用程式的應用程式識別碼 URI，或啟用 OAuth2.0 隱含流程，而不是預設授權授與代碼流程。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在左側導覽窗格中，選擇 [更多服務]，按一下 [應用程式註冊]，然後從清單中選取您的應用程式。
4. 按一下應用程式頁面中的 [資訊清單]，以開啟內嵌資訊清單編輯器。
5. 您可以直接變更資訊清單並適時予以儲存。 或者，您也可以下載資訊清單以在您喜好的編輯器中開啟，並上傳更新後的資訊清單。

## <a name="next-steps"></a>後續步驟

1. 查看[快速入門](active-directory-developers-guide.md)，以取得使用 Azure AD 執行應用程式驗證的詳細逐步解說。
2. 查看 [GitHub](https://github.com/azure-samples) 中完整的程式碼範例清單。
