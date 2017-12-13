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
ms.openlocfilehash: bd090450fad0be855240788c4cfa9dc58c1c4c6d
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>在單一應用程式中登入 Microsoft 帳戶和 Azure Active Directory 使用者
在過去，應用程式開發人員需要同時支援個人 Microsoft 帳戶和 Azure Active Directory 中的公司帳戶時，必須整合這兩個不同的系統。 Azure Active Directory (Azure AD) v2.0 端點導入新的驗證 API 版本，可簡化此程序。 Azure AD v2.0 端點可使用單一整合從兩種類型的帳戶登入。 使用 Azure AD v2.0 端點的應用程式也可以利用這其中一種帳戶，從 [Microsoft Graph API](https://graph.microsoft.io) 取用 REST API。

## <a name="getting-started"></a>開始使用
從下列清單選擇您最愛的平台，使用 Microsoft 開放原始碼程式庫與架構來建置應用程式。 您也可以使用 OAuth 2.0 和 OpenID Connect 通訊協定直接傳送和接收通訊協定訊息，而不使用驗證程式庫。
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>深入了解 Azure AD v2.0 端點
深入了解您可以使用 Azure AD v2.0 端點執行的內容：

* 探索[您可以使用 Azure AD v2.0 端點來建置的應用程式類型](active-directory-v2-flows.md)。
* 了解使用 Azure AD v2.0 端點的[限制和條件約束](active-directory-v2-limitations.md)。
* 如需 Azure AD v2.0 端點的概觀，請觀看這段影片：

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>其他資源
探索 Azure AD v2.0 端點平台的深入資訊：

* [Azure AD v2.0 通訊協定參考](active-directory-v2-protocols.md)
* [Azure AD v2.0 權杖參考](active-directory-v2-tokens.md)
* [Azure AD v2.0 驗證程式庫參考](active-directory-v2-libraries.md)
* [Azure AD v2.0 端點的範圍和同意](active-directory-v2-scopes.md)
* [Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> 如果您只需要從 Azure Active directory 登入公司及學校帳戶，請先閱讀 [Azure AD 開發人員指南](active-directory-developers-guide.md)。 Azure AD v2.0 端點的適用對象是明確需要登入 Microsoft 個人帳戶的開發人員。

[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
