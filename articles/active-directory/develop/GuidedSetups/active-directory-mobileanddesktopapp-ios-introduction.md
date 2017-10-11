---
title: "Azure AD v2 iOS 快速入門 - 簡介 | Microsoft Docs"
description: "iOS (Swift) 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
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
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 948693c8501ecc46a1508e5ea085846d0910783e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>從 iOS 應用程式呼叫 Microsoft Graph API

本指南示範原生 iOS 應用程式 (Swift) 如何取得存取權杖，以及如何呼叫 Microsoft Graph API 或需要來自 Azure Active Directory v2 端點之存取權杖的其他 API。

在本指南最後，您的應用程式將能夠使用個人帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及使用來自使用 Azure Active Directory 之任何公司或組織的公司與學校帳戶呼叫受保護的 API。

> ### <a name="pre-requisites"></a>必要條件
> - 本指南需要 XCode 8.x。 您可以[從這裡](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下載 URL") 下載 XCode。
> - [Carthage](https://github.com/Carthage/Carthage) 以進行套件管理

### <a name="how-this-guide-works"></a>本指南使用方式

![本指南使用方式](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

本指南建立的範例應用程式可讓 iOS 應用程式查詢 Microsoft Graph API，或查詢可接受來自 Azure Active Directory v2 端點之權杖的 Web API。 針對這個案例，系統會透過授權標頭將一個權杖新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>處理權杖取得以存取受保護的 Web API

使用者完成驗證之後，範例應用程式就會收到一個權杖，可用來查詢 Microsoft Graph API 或由 Microsoft Azure Active Directory v2 保護的 Web API。

像 Microsoft 圖形這樣的 API 需要存取權杖，才能允許存取特定資源，例如讀取使用者的設定檔、存取使用者的行事曆，或傳送電子郵件。 您的應用程式可以使用 MSAL，以透過指定 API 範圍來要求存取權杖。 此存取權杖接著會新增到 HTTP 授權標頭，這些是針對受保護資源發出之每個呼叫的授權標頭。

MSAL 會為您管理快取和重新整理存取權杖，因此您的應用程式不需要執行這些作業。


### <a name="nuget-packages"></a>NuGet 套件

本指南會使用以下 NuGet 套件：

|程式庫|說明|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|適用於 iOS 的 Microsoft Authentication Library 預覽|

