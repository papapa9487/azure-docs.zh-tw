---
title: "Azure AD v2 Android 快速入門 - 簡介 | Microsoft Docs"
description: "Android 應用程式如何取得存取權杖，以及如何呼叫 Microsoft 圖形 API，或呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>從 Android 應用程式呼叫 Microsoft 圖形 API

本指南示範原生 Android 應用程式如何取得存取權杖，以及如何呼叫 Microsoft 圖形 API 或需要來自 Azure Active Directory v2 端點之存取權杖的其他 API。

在本指南最後，您的應用程式將能夠使用個人帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及使用來自使用 Azure Active Directory 之任何公司或組織的公司與學校帳戶呼叫受保護的 API。  

### <a name="how-this-sample-works"></a>這個範例的運作方式
![這個範例的運作方式](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

本指南所建立的範例是以下列案例為基礎：Android 應用程式是用來查詢接受來自 Azure Active Directory v2 端點之權杖的 Web API (在此案例中為 Microsoft 圖形 API)。 針對這個案例，系統會透過授權標頭將一個權杖新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。

### <a name="pre-requisites"></a>先決條件
* 本引導式設定著重在 Android Studio，但也可以接受任何其他 Android 應用程式開發環境。 
* 需要 Android SDK 21 或更新版本 (建議使用 SDK 25)。
* 這個 Android 版的 Microsoft Authentication Library (MSAL) 版本需要 Google Chrome 或使用「自訂索引標籤」的網頁瀏覽器。

> 注意：Visual Studio 的 Android 模擬器中未包括 Google Chrome。 建議您在使用 API 25 的模擬器，或使用 API 21 或更新版本且已經安裝 Google Chrome 的映像中，測試這個程式碼。


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>如何處理權杖取得作業，以存取受保護的 Web API

使用者完成驗證之後，範例應用程式就會收到一個權杖，可用來查詢 Microsoft 圖形 API 或由 Microsoft Azure Active Directory v2 保護的 Web API。

像 Microsoft 圖形這樣的 API 需要存取權杖，才能允許存取特定資源，例如讀取使用者的設定檔、存取使用者的行事曆，或傳送電子郵件。 您的應用程式可以使用 MSAL 要求存取權杖，以透過指定 API 範圍來存取這些資源。 此存取權杖接著會新增到 HTTP 授權標頭，這些是針對受保護資源發出之每個呼叫的授權標頭。 

MSAL 會為您管理快取和重新整理存取權杖，因此您的應用程式不需要執行這些作業。

### <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|描述|
|---|---|
|[com.microsoft.identity.client (英文)](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|
