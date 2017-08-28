---
title: "Azure AD v2 JS SPA 指引設定 - 簡介 | Microsoft Docs"
description: "JavaScript SPA 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 4f40ba0eb6ce3e55a1e5458bc6e83060fcf6624c
ms.contentlocale: zh-tw

---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>從 JavaScript 單一頁面應用程式 (SPA) 呼叫 Microsoft Graph API

本指南示範 JavaScript 單一頁面應用程式 (SPA)如何登入個人公司及學校帳戶，取得存取權杖，以及呼叫 Microsoft Graph API 或需要來自 Azure Active Directory v2 端點之存取權杖的其他 API。

### <a name="how-this-guide-works"></a>本指南使用方式

![本指南產生之範例應用程式的運作方式](media/active-directory-singlepageapp-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>相關資訊

本指南建立的範例應用程式可讓 JavaScript SPA 查詢 Microsoft Graph API，或查詢可接受來自 Azure Active Directory v2 端點之權杖的 Web API。 針對這個案例，在使用者登入之後，系統會透過授權標頭要求一個存取權杖，並將其新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|說明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library for JavaScript 預覽|

> [!NOTE]
> msal.js 的目標是 Azure Active Directory v2 端點 - 它會啟用個人、學校及公司帳戶以登入和取得權杖。 Azure Active Directory v2 端點具有[一些限制](..\active-directory-v2-limitations.md)。 如果您只對於學校和公司帳戶感到興趣，請使用 adal.js 和 V1 端點。 若要了解 v1 和 v2 端點之間的差異，請參閱 [v1-v2 比較](..\active-directory-v2-compare.md)。

<!--end-collapse-->

