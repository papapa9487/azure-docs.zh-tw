---
title: "使用入口網站向 Azure AD v2.0 端點註冊應用程式 | Microsoft Docs"
description: "如何向 Microsoft 註冊 app，以使用 v2.0 端點啟用登入及存取 Microsoft 服務"
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: 
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: e6202aa8665c906382666fe08a561421e50e0a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>如何使用 v2.0 端點註冊 App
若要建置同時接受 MSA 與 Azure AD 登入的應用程式，您必須先向 Microsoft 註冊應用程式。  您目前無法使用任何現有的 app 搭配 Azure AD 或 MSA - 您需要建立一個全新的 app。

> [!NOTE]
> v2.0 端點並非支援每個 Azure Active Directory 案例和功能。  若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

## <a name="visit-the-microsoft-app-registration-portal"></a>造訪 Microsoft App 註冊入口網站
第一件事就是先瀏覽至 [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。  這是新的 app 註冊入口網站，可供您管理有關 Microsoft app 的所有一切。

使用個人或公司或學校的 Microsoft 帳戶進行登入。  如果您沒有任何帳戶，請註冊新的個人帳戶。 請繼續進行，這不需要很長的時間 - 我們會在此等候。

完成了嗎？ 您現在應該看一下您的 Microsoft 應用程式清單，該清單有可能空白。  讓我們改變這點。

按一下 [新增應用程式]，並為它命名。  入口網站將會指派全域唯一的應用程式識別碼給您的應用程式，以便稍後在您的程式碼中使用。  如果您的應用程式包含的伺服器端元件需要用來呼叫 API (例如：Office、Azure 或您自己的 Web API) 的存取權杖，您也會想在此處建立**應用程式密碼**。

接下來，加入您的 app 將使用的平台。

* 針對 web 架構的 app，提供可傳送登入訊息的 **重新導向 URI** 。
* 針對行動應用程式，複製系統自動為您建立的預設重新導向 URI。

(選擇性) 您可以在 [設定檔] 區段中自訂登入頁面的外觀及操作方式。  繼續之前，請務必按一下 [儲存]  。

> [!NOTE]
> 當您使用 [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 建立應用程式時，系統將會在您用來登入入口網站的帳戶的家用租用戶中註冊該應用程式。  這表示您不能使用個人的 Microsoft 帳戶，在 Azure AD 租用戶中註冊應用程式。  如果您明確地想要在特定的租用戶中註冊應用程式，請使用原本在該租用戶中建立的帳戶登入。
> 
> 

## <a name="build-a-quick-start-app"></a>建置快速啟動應用程式
您現在已有 Microsoft app，您可以完成我們提供的其中一個 v2.0 快速啟動教學課程。  以下是一些建議：

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

