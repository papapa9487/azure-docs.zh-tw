---
title: "適用於 Azure Active Directory 中預先整合之應用程式的 SAML 權杖的進階憑證簽署選項 | Microsoft Docs"
description: "瞭解如何在 Azure Active Directory 中，針對預先整合之應用程式使用 SAML 權杖中的進階憑證簽署選項"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 70e495965287a0edcb31493b69311fe28e04f6dc
ms.contentlocale: zh-tw
ms.lasthandoff: 09/19/2017

---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>在 Azure Active Directory 中，針對資源庫應用程式使用 SAML 權杖中的進階憑證簽署選項
目前 Azure Active Directory 支援 Azure AD 應用程式庫中數千個預先整合的應用程式，有 500 個以上的應用程式支援使用 SAML 2.0 通訊協定的單一登入。 當使用者使用 SAML 透過 Azure AD 驗證應用程式時，Azure AD 會將權杖傳送給應用程式 (透過 HTTP POST)。 然後，應用程式會驗證並使用權杖將使用者登入，而不會提示輸入使用者名稱和密碼。 這些 SAML 權杖會使用 Azure AD 中產生的唯一憑證加以簽署。 此 SAML 權杖是使用特定的標準演算法簽署。

Azure Active Directory 為資源庫應用程式使用某些預設設定。 根據應用程式需求設定預設值。

Azure Active Directory 支援進階憑證簽署的設定。 若要選取這些選項，請先選取 **SAML 進階憑證簽署設定**核取方塊，如下所示。

![憑證簽署選項][1]

一旦核取此核取方塊，接著可設定**憑證簽署選項**和**憑證簽署演算法**。

## <a name="certificate-signing-options"></a>憑證簽署選項

以下是 Azure AD 支援的三種憑證簽署選項類型。

1. **簽署 SAML 判斷提示** - 這是適用於大部分資源庫應用程式的預設選項設定。 如果選取此選項，則作為 IDP 的 Azure AD 會使用應用程式的 X509 憑證來簽署 SAML 判斷提示及憑證。 它也會使用可從下列下拉式清單中選取的簽署演算法。

2. **簽署 SAML 回應** - 如果選取此選項，則作為 IDP 的 Azure AD 會使用應用程式的 X509 憑證來簽署 SAML 回應。 它也會使用可從下列下拉式清單中選取的簽署演算法。

3. **簽署 SAML 回應和判斷提示** - 如果選取此選項，則作為 IDP 的 Azure AD 會使用應用程式的 X509 憑證來簽署整個 SAML 權杖。 它也會使用可從下列下拉式清單中選取的簽署演算法。

    ![憑證簽署選項][4]

## <a name="certificate-signing-algorithm"></a>憑證簽署演算法

Azure Active Directory 支援兩種類型的簽署演算法來簽署 SAML 回應。

1. SHA256 - 這是 Azure Active Directory 用來簽署 SAML 回應的預設演算法。 這是最新的演算法，而且視為比 SHA1 更安全。 大部分的應用程式都支援 SHA256 演算法。 如果應用程式只支援 Sha1 作為簽署演算法，則您可以變更它。 否則我們建議使用 SHA256 演算法來簽署 SAML 回應。

    ![SHA256 憑證簽署演算法][3]

2. SHA1 - 這是較舊的演算法並被視為不安全。 如果應用程式僅支援此簽署演算法，您可以在下拉式清單中選取此選項。 搭配使用 Azure AD 與 Sha1 演算法來簽署 SAML 回應。

    ![SHA1 憑證簽署演算法][2]

## <a name="next-steps"></a>後續步驟
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](active-directory-saas-custom-apps.md)
* [SAML 型單一登入疑難排解](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
