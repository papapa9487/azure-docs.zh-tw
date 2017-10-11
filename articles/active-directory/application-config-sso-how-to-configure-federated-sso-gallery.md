---
title: "如何為 Azure AD 資源庫應用程式設定同盟單一登入 | Microsoft Docs"
description: "如何為現有的 Azure AD 資源庫應用程式設定同盟單一登入，並使用教學課程快速開始使用"
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
ms.openlocfilehash: 1b1d00718981b2c7d11f5b88428d02e16dd0b34d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>如何為 Azure AD 資源庫應用程式設定同盟單一登入

Azure AD 資源庫中所有透過企業單一登入功能啟用的應用程式都提供逐步教學課程。 您可以存取[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)，以取得詳細的逐步指引。

## <a name="overview-of-steps-required"></a>所需步驟的概觀
若要設定 Azure AD 資源庫中的應用程式，您必須：

-   [從 Azure AD 資源庫新增應用程式](#add-an-application-from-the-azure-ad-gallery)

-   [在 Azure AD 中設定應用程式的中繼資料值 (登入 URL、識別碼、回覆 URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [選取使用者識別碼並新增要傳送到應用程式的使用者屬性](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [擷取 Azure AD 中繼資料與憑證](#download-the-azure-ad-metadata-or-certificate)

-   [在應用程式中設定 Azure AD 中繼資料值 (登入 URL、簽發者、登出 URL 與憑證)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [將使用者指派給應用程式](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫新增應用程式

若要從 Azure AD 資源庫新增應用程式，請依照下列步驟執行：

1.  開啟 [Azure 入口網站](https://portal.azure.com)，然後以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [企業應用程式] 刀鋒視窗右上角的 [新增] 按鈕。

6.  在 [從資源庫新增] 區段的 [輸入名稱] 文字方塊中，輸入應用程式名稱。

7.  選取您要設為單一登入的應用程式。

8.  新增應用程式之前，您可以從 [名稱] 文字方塊變更其名稱。

9.  按一下 [新增] 按鈕以新增應用程式。

稍候片刻，您便能看見應用程式的設定刀鋒視窗。

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫為應用程式設定單一登入

若要設定應用程式使用單一登入，請依照下列步驟執行：

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您要設定單一登入的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [單一登入]。

8.  從 [模式] 下拉式清單選取 [SAML 登入]。

9.  在 [網域及 URL] 中輸入必要值。 這些值應從應用程式廠商處取得。

   1. 若要將應用程式設定為 SP 啟始的 SSO，則登入 URL 為必要值。 對於某些應用程式，識別碼也是必要值。

   2. 若要將應用程式設定為 IdP 啟始的單一登入，則 [登入 URL] 為必要值。 對於某些應用程式，識別碼也是必要值。

10. **選擇性：**如果您想要看到非必要值，請按一下 [顯示進階 URL 設定]。

11. 在 [使用者屬性] 中，從 [使用者識別碼] 下拉式清單選取使用者的唯一識別碼。

12. **選擇性：**按一下 [檢視和編輯所有其他使用者屬性]，以編輯當使用者登入時要以 SAML 權杖傳送至應用程式的屬性。

  新增屬性：
   
   1. 按一下 [新增屬性]。 輸入 [名稱]，然後從下拉式清單選取 [值]。

   1. 按一下 [儲存]。 您會在資料表中看到新屬性。

13. 按一下 [設定 &lt;應用程式名稱&gt;]，以存取如何在應用程式中設定單一登入的文件。 此外，您會有透過該應用程式設定 SSO 所需的中繼資料 URL 與憑證。

14. 按一下 [儲存] 儲存組態。

15. 將使用者指派給應用程式。

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>選取使用者識別碼並新增要傳送到應用程式的使用者屬性

若要選取使用者識別碼或新增使用者屬性，請依照下列步驟執行：

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您已設定單一登入的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [單一登入]。

8.  在 [使用者屬性] 區段下，從 [使用者識別碼] 下拉式清單選取使用者的唯一識別碼。 所選的選項必須符合應用程式中預期的值，才能驗證使用者。

  >[!NOTE] 
  >Azure AD 會根據應用程式在 SAML AuthRequest 中選取的值或要求的格式，來選取 NameID 屬性 (使用者識別碼) 的格式。 如需詳細資訊，請參閱[單一登入 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章中的＜NameIDPolicy＞一節。
  >
  >

9.  若要新增使用者屬性，按一下 [檢視和編輯所有其他使用者屬性]，以編輯當使用者登入時要以 SAML 權杖傳送至應用程式的屬性。

   新增屬性：
  
   1. 按一下 [新增屬性]。 輸入**名稱**，然後從下拉式清單選取**值**。

   2. 按一下 [儲存] 。 您會在資料表中看到新屬性。

## <a name="download-the-azure-ad-metadata-or-certificate"></a>下載 Azure AD 中繼資料或憑證

若要從 Azure AD 下載應用程式中繼資料或憑證，請依照下列步驟執行：

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

  *  若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您已設定單一登入的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [單一登入]。

8.  移至 [SAML 簽署憑證] 區段，然後按一下 [下載] 資料行值。 根據應用程式設定單一登入時所需的項目，您會看到下載中繼資料 XML 或憑證的選項。

Azure AD 不提供取得中繼資料的 URL。 只能將中繼資料擷取為 XML 檔案。

## <a name="assign-users-to-the-application"></a>將使用者指派給應用程式

若要直接將一或多個使用者指派至應用程式，請依照下列步驟執行：

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，以**全域管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

  * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  從清單中選取您想要指派使用者的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [使用者和群組]。

8.  按一下 [使用者和群組] 清單頂端的 [新增] 按鈕，以開啟 [新增指派] 刀鋒視窗。

9.  從 [新增指派] 刀鋒視窗按一下 [使用者和群組] 選取器。

10. 在 [依姓名或電子郵件地址搜尋] 搜尋方塊中，輸入您有興趣指派之使用者的**全名**或**電子郵件地址**。

11. 將滑鼠停留在清單中的**使用者**上方，以顯示**核取方塊**。 按一下使用者設定檔照片或標誌旁邊的核取方塊，將使用者新增至 [已選取] 清單。

12. **選擇性︰**如果您想要**新增多位使用者**，請在 [依姓名或電子郵件地址搜尋] 搜尋方塊中，輸入另一個**全名**或**電子郵件地址**，然後按一下核取方塊，將此使用者新增至 [已選取] 清單。

13. 當您完成選取使用者時，按一下 [選取] 按鈕，將他們新增到要指派至應用程式的使用者和群組清單。

14. **選擇性︰**按一下 [新增指派] 刀鋒視窗中的 [選取角色] 選取器，以選取要指派給您已選取使用者的角色。

15. 按一下 [指派] 按鈕，將應用程式指派給選取的使用者。

稍待片刻，您已選取的使用者就能夠使用解決方案描述一節所述的方法，啟動這些應用程式。

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>自訂傳送至應用程式的 SAML 宣告

若要了解如何自訂傳送至應用程式的 SAML 屬性宣告，請參閱 [Azure Active Directory 中的宣告對應](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)以取得詳細資訊。

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](active-directory-application-proxy-sso-using-kcd.md)



