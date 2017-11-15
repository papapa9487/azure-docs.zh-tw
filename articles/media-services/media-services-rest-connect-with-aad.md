---
title: "使用 Azure AD 驗證搭配 REST 存取 Azure 媒體服務 API | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 驗證搭配 REST 存取 Azure 媒體服務 API。"
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: e5d7a5ec1c28a552420aba5e2cd6c8c7bbf4213d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>使用 Azure AD 驗證搭配 REST 存取 Azure 媒體服務 API

Azure 媒體服務團隊針對 Azure 媒體服務的存取發行了 Azure Active Directory (Azure AD) 驗證支援。 團隊也宣佈針對媒體服務的存取淘汰 Azure 存取控制服務驗證的計劃。 由於每個 Azure 訂用帳戶及每個媒體服務帳戶均附屬於一個 Azure AD 租用戶，因此，Azure AD 驗證支援有許多安全性優勢。 如需此變更和移轉 (如果您的應用程式使用媒體服務 .NET SDK) 的詳細資訊，請參閱下列部落格文章和文件：

- [Azure 媒體服務宣佈支援 Azure AD 驗證，取代存取控制驗證](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation) \(英文\)
- [使用 Azure AD 驗證存取 Azure 媒體服務 API](media-services-use-aad-auth-to-access-ams-api.md)
- [使用 Azure AD 驗證搭配 Microsoft .NET 存取 Azure 媒體服務 API](media-services-dotnet-get-started-with-aad.md)
- [利用 Azure 入口網站開始使用 Azure AD 驗證](media-services-portal-get-started-with-aad.md)

某些客戶在開發媒體服務解決方案時深受下列條件限制：

*   他們使用的不是 Microsoft .NET 或 C# 程式設計語言，或使用非 Windows 的執行階段環境。
*   Azure AD 程式庫 (如 Active Directory 驗證程式庫) 不適用於該程式設計語言，或無法用於客戶的執行階段環境。

某些客戶已使用 REST API 開發可執行存取控制驗證和存取 Azure 媒體服務的應用程式。 對於這些客戶，您需要的是一種方式：只要使用 REST API 即可執行 Azure AD 驗證和後續的 Azure 媒體服務存取。 您不需要任何 Azure AD 程式庫或媒體服務 .NET SDK。 在本文中，我們描述一種解決方案，並提供此案例的範例程式碼。 由於程式碼全是 REST API 呼叫，不需要任何 Azure AD 或 Azure 媒體服務程式庫，因此程式碼可以輕鬆地轉譯成任何其他的程式設計語言。

> [!IMPORTANT]
> 目前，媒體服務支援 Azure 存取控制服務驗證模型。 不過，存取控制驗證將在 2018 年 6 月 1 日被取代。 建議您儘速移轉至 Azure AD 驗證模型。


## <a name="design"></a>設計

在本文中，我們使用下列驗證和授權設計：

*  授權通訊協定：OAuth 2.0。 OAuth 2.0 是一種涵蓋驗證和授權的 Web 安全性標準。 OAuth 2.0 受 Google、Microsoft、Facebook 和 PayPal 支援。 它於 2012 年 10 月獲得認可。 Microsoft 穩固地支援 OAuth 2.0 和 OpenID Connect。 有多種服務和用戶端程式庫均支援這些標準，包括 Azure Active Directory、Open Web Interface for .NET (OWIN) 及 Azure AD 程式庫。
*  授與類型：用戶端認證授與類型。 用戶端認證是 OAuth 2.0 中的四個授與類型之一。 它通常用於 Azure AD Microsoft Graph API 存取。
*  驗證模型：服務主體。 另一種驗證模型為使用者或互動式驗證。

在使用媒體服務的 Azure AD 驗證和授權流程中，共有四種相關的應用程式或服務。 下表說明這些應用程式和服務以及流程：

|應用程式類型 |應用程式 |Flow|
|---|---|---|
|用戶端 | 客戶應用程式或解決方案 | 此應用程式 (實際上是其 Proxy) 註冊於 Azure 訂用帳戶和媒體服務帳戶所在的 Azure AD 租用戶中。 已註冊應用程序的服務主體在媒體服務帳戶的存取控制 (IAM) 方面會被授與「擁有者」或「參與者」角色。 服務主體由應用程式用戶端識別碼和用戶端祕密表示。 |
|身分識別提供者 (IDP) | 作為 IDP 的 Azure AD | 作為 IDP 的 Azure AD 驗證已註冊應用程式的服務主體 (用戶端識別碼和用戶端祕密)。 此驗證是在內部以隱含方式執行。 與在用戶端認證流程中一樣，受驗證的是用戶端而非使用者。 |
|安全性權杖服務 (STS)/OAuth 伺服器 | 作為 STS 的 Azure AD | IDP (或 OAuth 2.0 術語中的 OAuth 伺服器) 驗證後，作為 STS/OAuth 伺服器的 Azure AD 會核發存取權杖或 JSON Web 權杖 (JWT)，以用於存取中介層資源：在此案例中，是媒體服務 REST API 端點。 |
|資源 | 媒體服務 REST API | 每個媒體服務 REST API 呼叫，都是由作為 STS 或 OAuth 伺服器的 Azure AD 核發的存取權杖所授權。 |

如果您執行範例程式碼並擷取 JWT 或存取權杖，JWT 會具有下列屬性：

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

以下是 JWT 和上表中四個應用程式或服務中之屬性之間的對應：

|應用程式類型 |應用程式 |JWT 屬性 |
|---|---|---|
|用戶端 |客戶應用程式或解決方案 |appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6"。 您將在下一節註冊至 Azure AD 之應用程式的用戶端識別碼。 |
|身分識別提供者 (IDP) | 作為 IDP 的 Azure AD |idp："https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/"  GUID 是 Microsoft 租用戶 (microsoft.onmicrosoft.com) 的識別碼。 每個租用戶都有專屬的唯一識別碼。 |
|安全性權杖服務 (STS)/OAuth 伺服器 |作為 STS 的 Azure AD | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/"。 GUID 是 Microsoft 租用戶的識別碼 (microsoft.onmicrosoft.com)。 |
|資源 | 媒體服務 REST API |aud: "https://rest.media.azure.net"。 存取權杖的收件者或對象。 |

## <a name="steps-for-setup"></a>設定的步驟

若要註冊和設定 Azure Active Directory (AAD) 應用程式，以及取得呼叫 Azure 媒體服務 REST API 端點的索引鍵，請參閱[使用 Azure 入口網站開始使用 Azure AD 驗證](media-services-portal-get-started-with-aad.md)


## <a name="info-to-collect"></a>要收集的資訊

若要準備 REST 編碼，請收集下列資料點以包含在程式碼中：

*   作為 STS 端點的 Azure AD： https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token。 JWT 存取權杖是從這個端點要求。 除了作為 IDP，Azure AD 也可作為 STS。 Azure AD 會核發存取資源所需的 JWT (存取權杖)。 JWT 權杖含有各種宣告。
*   作為資源或對象的 Azure 媒體服務 REST API：https://rest.media.azure.net。
*   用戶端識別碼：請參閱[設定步驟](#steps-for-setup)中的步驟 2。
*   用戶端祕密：請參閱[設定步驟](#steps-for-setup)中的步驟 2。
*   格式如下的媒體服務帳戶 REST API 端點：

    https://[media_service_account_name].restv2.[data_center].media.azure.net/API 

    您的應用程式中發出之所有媒體服務 REST API 呼叫，都是針對此端點。 例如，https://willzhanmswjapan.restv2.japanwest.media.azure.net/API。

您可以接著將這五個參數放入 web.config 或 app.config 檔案，以在您的程式碼中使用。

## <a name="sample-code"></a>範例程式碼

您可以在[存取 Azure 媒體服務要進行的 Azure AD 驗證：均透過 REST API](https://github.com/willzhan/WAMSRESTSoln) \(英文\) 中找到範例程式碼。

範例程式碼有兩個部分：

*   DLL 程式庫專案，含有 Azure AD 驗證與授權的所有 REST API 程式碼。 它也含有向媒體服務 REST API 端點發出 REST API 呼叫的方法 (使用存取權杖)。
*   主控台測試用戶端，會起始 Azure AD 驗證，並呼叫不同的媒體服務 REST API。

範例專案有三個特點：

*   僅利用 REST API 透過用戶端認證授與進行 Azure AD 驗證。
*   僅利用 REST API 存取 Azure 媒體服務。
*   僅利用 REST API 存取 Azure 儲存體 (例如用來建立媒體服務帳戶)。


## <a name="where-is-the-refresh-token"></a>重新整理權杖是什麼？

某些讀者可能會問：重新整理權杖是什麼？ 為何不在此使用重新整理權杖？

重新整理權杖的用途不是重新整理存取權杖。 它被設計為當稍早的權杖到期時略過使用者驗證，並仍可獲得有效的存取權杖。 比重新整理權杖更適合的名稱可能是像「略過使用者重新登入權杖」的名稱。

如果您使用 OAuth 2.0 授權授與流程 (使用者名稱和密碼，可代表使用者)，重新整理權杖可協助您在不需要求使用者介入的情況下取得更新的存取權杖。 不過，針對本文中說明的 OAuth 2.0 用戶端認證授與流程，用戶端是代表自己的身分。 您不需要使用者介入，而且授權伺服器不需要提供您重新整理權杖。 如果您對 **GetUrlEncodedJWT** 方法進行偵錯，您會發現來自權杖端點的回應具有存取權杖，而非重新整理權杖。

## <a name="next-steps"></a>後續步驟

開始使用[上傳檔案至您的帳戶](media-services-dotnet-upload-files.md)。
