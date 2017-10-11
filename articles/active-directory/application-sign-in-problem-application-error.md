---
title: "登入後應用程式頁面的錯誤 | Microsoft Docs"
description: "如何解決當應用程式本身發生錯誤時 Azure AD 登入方面的問題"
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
ms.openlocfilehash: a8cd93256f79ece268ec3411dfbdf590f4b24447
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="error-on-an-applications-page-after-signing-in"></a>登入後應用程式頁面的錯誤

在此案例中，Azure AD 已將使用者登入，但應用程式顯示不允許使用者成功完成登入流程的錯誤。 在此案例中，應用程式不接受由 Azure AD 發出的回應。

有一些可能的原因會造成應用程式不接受 Azure AD 的回應。 如果應用程式中的錯誤不夠清楚，無法得知回應中遺漏了什麼，則：

-   如果應用程式是 Azure AD 資源庫，請確認您已依照[如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)一文中所有的步驟進行。

-   使用如 [Fiddler](http://www.telerik.com/fiddler) 等工具擷取 SAML 要求、SAML 回應和 SAML 權杖。

-   將 SAML 回應與應用程式廠商分享，以得知遺漏的項目。

## <a name="missing-attributes-in-the-saml-response"></a>SAML 回應中遺漏屬性

若要在 Azure AD 組態中新增要以 Azure AD 回應傳送的屬性，請依照下列步驟執行：

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您要設定單一登入的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [單一登入]。

8.  按一下 [使用者屬性] 區段下方的 [檢視和編輯所有其他使用者屬性]，以編輯當使用者登入時要以 SAML 權杖傳送至應用程式的屬性。

   若要新增屬性：

   * 按一下 [新增屬性]。 輸入 [名稱]，然後從下拉式清單選取 [值]。

   * 按一下 [儲存]。 您會在資料表中看到新屬性。

9.  儲存組態。

下次使用者登入應用程式，Azure AD 會以 SAML 回應傳送新的屬性。

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>應用程式預期不同的使用者識別碼值或格式

登入應用程式失敗，因為 SAML 回應中遺漏如角色等屬性，或因為應用程式所預期的 EntityID 屬性格式不同。

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>在 Azure AD 應用程式組態中新增屬性：

若要變更的使用者識別碼值，請依照下列步驟執行︰

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您要設定單一登入的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [單一登入]。

8.  在 [使用者屬性] 下方，從 [使用者識別碼] 下拉式清單選取使用者的唯一識別碼。

## <a name="change-entityid-user-identifier-format"></a>變更 EntityID (使用者識別碼) 格式

如果應用程式預期 EntityID 屬性為另一種格式。 接著，在使用者驗證之後，您將無法選取 Azure AD 要在回應中傳送至應用程式的 EntityID (使用者識別碼) 格式。

Azure AD 會根據由應用程式以 SAML AuthRequest 選取的值或要求的格式，來選取 NameID 屬性 (使用者識別碼) 的格式。 如需詳細資訊，請參閱 NameIDPolicy 區段下方的[單一登入 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)一文。

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>應用程式所預期的 SAML 回應簽章方法不同

若要變更 Azure Active Directory 數位簽署 SAML 權杖的部分， 請依照下列步驟執行：

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

  * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您要設定單一登入的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [單一登入]。

8.  按一下 [SAML 簽署憑證] 區段下方的 [Show advanced certificate signing settings (顯示進階憑證設定)]。

9.  選取應用程式所預期的適當**簽署選項**：

  * 簽署 SAML 回應

  * 簽署 SAML 回應及判斷提示

  * 簽署 SAML 判斷提示

下次使用者登入應用程式，Azure AD 會簽署所選的 SAML 回應部分。

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>應用程式預期的簽署演算法是 SHA-1

根據預設，Azure AD 會使用最安全的演算法簽署 SAML 權杖。 不建議將簽署演算法變更為 SHA-1，除非應用程式需要。

若要變更簽署演算法，請依照下列步驟執行：

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您要設定單一登入的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [單一登入]。

8.  按一下 [SAML 簽署憑證] 區段下方的 [Show advanced certificate signing settings (顯示進階憑證設定)]。

9.  在 [簽署演算法] 中選取 [SHA-1]。

下次使用者登入應用程式，Azure AD 會使用 SHA-1 演算法簽署 SAML 權杖。

## <a name="next-steps"></a>後續步驟
[如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
