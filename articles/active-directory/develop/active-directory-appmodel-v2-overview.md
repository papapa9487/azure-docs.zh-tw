---
title: "Azure Active Directory v2.0 端點 | Microsoft Docs"
description: "建置具備 Microsoft 帳戶和 Azure Active Directory 登入之應用程式的簡介。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 1e286044fb1a1b367fcac2dc14c47f68d5ed120d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>以單一應用程式登入 Microsoft 帳戶和 Azure AD 使用者
在過去，想要同時支援個人之 Microsoft 帳戶和得自 Azure Active Directory 之公司帳戶的應用程式開發人員必須整合這兩個不同的系統。  **Azure AD v2.0 端點**引進了新的驗證 API 版本，可讓您在進行過一次簡單的整合後，就能同時登入這兩種帳戶。  使用 v2.0 端點的應用程式也可以利用這其中一種帳戶，從 [Microsoft Graph](https://graph.microsoft.io) 取用 REST API。

## <a name="getting-started"></a>開始使用
從下列清單選擇您最愛的平台，使用我們的開放原始碼程式庫與架構來建置應用程式。  或者，您可以使用 OAuth 2.0 和 OpenID Connect 通訊協定文件，直接傳送及接收通訊協定訊息，而不使用驗證庫。

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>新功能
當您想要了解 v2.0 端點可執行哪些動作以及不可執行哪些動作時，此處提供的資訊非常實用。

* 了解 [您可以使用 v2.0 端點建置的應用程式類型](active-directory-v2-flows.md)。
* 了解使用 v2.0 端點的 [限制和條件約束](active-directory-v2-limitations.md) 。
* 請觀賞這部關於 v2.0 端點的概觀影片︰

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>參考
下列連結有助於深入探索平台：

* [v2.0 通訊協定參考](active-directory-v2-protocols.md)
* [v2.0 權杖參考](active-directory-v2-tokens.md)
* [v2.0 程式庫參考](active-directory-v2-libraries.md)
* [v2.0 端點的範圍和同意](active-directory-v2-scopes.md)
* [Microsoft Graph](https://graph.microsoft.io)

## <a name="help--support"></a>說明及支援
以下是取得在 Azure Active Directory 開發之協助的最佳位置。

* [Stack Overflow 的 `azure-active-directory` 和 `adal` 標籤](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)
* [Azure Active Directory 的意見反應](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)


> [!NOTE]
> 如果您只需要從 Azure Active directory 登入公司及學校帳戶，則應該先閱讀我們的 [Azure AD 開發人員指南](active-directory-developers-guide.md)。  v2.0 端點的適用對象是明確需要登入 Microsoft 個人帳戶的開發人員。

