---
title: "為不在資源庫內的應用程式設定同盟單一登入時遇到的問題 | Microsoft Docs"
description: "解決您可能會在為未列於 Azure AD 應用程式庫中的自訂 SAML 應用程式設定同盟單一登入時遇到的一些常見問題"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 4eb2c04c940dd6ad15a491a331aed76c237f0387
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>為不在資源庫內的應用程式設定同盟單一登入時遇到的問題

如果您在設定應用程式時遇到問題。 確認您已依照[設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)一文中的所有步驟執行。

## <a name="cant-add-another-instance-of-the-application"></a>無法新增應用程式的另一個執行個體

若要新增應用程式的第二個執行個體，您需要能夠：

-   設定第二個執行個體的唯一識別碼。 您無法設定已用於第一個執行個體的相同識別碼。

-   設定與第一個執行個體所使用的憑證不同的憑證。

如果應用程式不支援上述任一種方法。 則您將無法設定第二個執行個體。

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>我可以在何處設定 EntityID (使用者識別碼) 格式

在使用者驗證之後，您將無法選取 Azure AD 要在回應中傳送至應用程式的 EntityID (使用者識別碼) 格式。

Azure AD 會根據應用程式在 SAML AuthRequest 中選取的值或要求的格式，來選取 NameID 屬性 (使用者識別碼) 的格式。 如需詳細資訊，請參閱[單一登入 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章中的＜NameIDPolicy＞一節。

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>我可以在何處取得 Azure AD 的應用程式中繼資料或憑證

若要從 Azure AD 下載應用程式中繼資料或憑證，請依照下列步驟執行：

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您已設定單一登入的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [單一登入]。

8.  移至 [SAML 簽署憑證] 區段，然後按一下 [下載] 資料行值。 根據應用程式設定單一登入時所需的項目，您會看到下載中繼資料 XML 或憑證的選項。

Azure AD 不提供取得中繼資料的 URL。 只能將中繼資料擷取為 XML 檔案。

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>不知道如何自訂傳送至應用程式的 SAML 宣告

若要了解如何自訂傳送至應用程式的 SAML 屬性宣告，請參閱 [Azure Active Directory 中的宣告對應](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)以取得詳細資訊。

## <a name="next-steps"></a>後續步驟
[使用 Azure Active Directory 管理應用程式](active-directory-enable-sso-scenario.md)
