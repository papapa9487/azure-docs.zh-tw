---
title: "如何針對 Azure Active Directory 中的 SAML 型應用程式單一登入進行偵錯 | Microsoft Docs"
description: "了解如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式  "
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: asmalser
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: 31447d597296bac57481dc2acb4a95ee3a104161
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式
當偵錯 SAML 型的應用程式整合時，使用 [Fiddler](http://www.telerik.com/fiddler) 之類的工具查看 SAML 要求、SAML 回應和發行給應用程式實際的 SAML 權杖，通常很有幫助。 您可以藉由檢查 SAML 權杖，確保所有必要的屬性、SAML 主體中的使用者名稱和簽發者 URI 都能如預期通過。

![][1]

包含 SAML 權杖的 Azure AD 回應，通常是 HTTP 302 從 https://login.windows.net 重新導向並傳送至已設定的應用程式**回覆 URL** 後，發生的那一個。 

您可以選取這一行，然後在右窗格中選取 [偵測器] > [WebForms]，檢視 SAML 權杖。 在這裡以滑鼠右鍵按一下 [SAMLResponse] 值並選取 [傳送至 TextWizard]。 然後選取 [轉換] 功能表的 [從 Base64]，解碼權杖並查看其內容。

**注意**：為查看這個 HTTP 要求的內容，Fiddler 可能會提示您設定解密 HTTPS 流量，請務必這麼做。

## <a name="related-articles"></a>相關文章
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](../active-directory-apps-index.md)
* [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](../active-directory-saas-custom-apps.md)
* [如何為預先整合的應用程式自訂在 SAML 權杖中發出的宣告](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png