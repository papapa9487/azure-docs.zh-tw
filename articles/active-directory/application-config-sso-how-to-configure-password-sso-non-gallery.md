---
title: "如何為不在資源庫內的應用程式設定密碼單一登入 | Microsoft Docs"
description: "當不在資源庫內的自訂應用程式未列在 Azure AD 應用程式庫時，如何為它設定以密碼為基礎的安全單一登入"
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
ms.openlocfilehash: f629ec99824199306ebf825901beaa99d83d434d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>如何為不在資源庫內的應用程式設定密碼單一登入

除了 Azure AD 應用程式庫中找到的選項，當您想要的應用程式未在此處列出時，您也可以選擇新增**不在資源庫內的應用程式**。 使用這項功能，您可以新增任何已存在於組織中的應用程式，或您可能從尚不屬於 [Azure AD 應用程式庫](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery)的供應商取得的任何第三方應用程式。

當您新增不在資源庫內的應用程式之後，接著就可以在 [Azure 入口網站](https://portal.azure.com/)中選取企業應用程式的 [單一登入] 瀏覽項目，以設定此應用程式使用的單一登入方法。

可供您使用的其中一個單一登入方法是[以密碼為基礎的單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)選項。 透過**新增不在資源庫內的應用程式**經驗，您可以整合任何呈現 HTML 使用者名稱和密碼輸入欄位的應用程式 (即使它不在我們預先整合的應用程式集之內)。

運作方式是採用存取面板擴充中的頁面抓取技術，這可讓我們自動偵測使用者名稱和密碼輸入欄位，安全地儲存這些欄位供您的特定應用程式執行個體使用。 然後，當使用者在應用程式存取面板上瀏覽至該應用程式時，就安全地將使用者名稱和密碼重播給這些欄位。

這是開始將任何一種應用程式快速整合至 Azure AD 的絕佳方法，可讓您︰

-   整合**坊間任何應用程式**和您的 Azure AD 租用戶 (只要應用程式會呈現 HTML 使用者名稱和密碼輸入欄位)

-   針對已經與 Azure AD 整合的應用程式，安全地儲存和重播使用者名稱和密碼，以啟用**使用者的單一登入**

-   針對任何應用程式**自動偵測輸入**欄位，萬一自動偵測找不到這些欄位，您還可以使用存取面板瀏覽器擴充來手動偵測

-   針對需要使用者名稱和密碼以外的更多欄位才能登入的應用程式，**支援需要多個登入欄位的應用程式**

-   針對使用者在[應用程式存取面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)輸入認證時所看到的使用者名稱和密碼輸入欄位，**自訂標籤**

-   針對**使用者**在[應用程式存取面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)手動輸入的任何現有應用程式帳戶，允許他們提供自己的使用者名稱和密碼

-   允許**商務群組的成員**使用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能，指定要指派給使用者的使用者名稱和密碼

-   允許**管理員**在[將使用者指派至應用程式](#_How_to_configure_1)時，使用更新憑證功能指定要指派給使用者的使用者名稱和密碼

-   允許**管理員**在[將群組指派至應用程式](#assign-an-application-to-a-group-directly)時，使用更新憑證功能指定一群人使用的共用使用者名稱或密碼

以下說明如何針對您使用**新增不在資源庫內的應用程式**經驗所新增的任何應用程式，啟用[以密碼為基礎的單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)。

## <a name="overview-of-steps-required"></a>所需步驟的概觀

若要設定 Azure AD 資源庫中的應用程式，您必須：

-   [新增不在資源庫內的應用程式](#add-a-non-gallery-application)

-   [設定應用程式使用密碼單一登入](#configure-the-application-for-password-single-sign-on)

-   [將應用程式指派至使用者或群組](#assign-the-application-to-a-user-or-a-group)

    -   [將使用者直接指派至應用程式](#assign-a-user-to-an-application-directly)

    -   [將應用程式直接指派至群組](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>新增不在資源庫內的應用程式

若要從 Azure AD 資源庫新增應用程式，請依照下列步驟執行：

1.  開啟 [Azure 入口網站](https://portal.azure.com)，然後以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [企業應用程式] 刀鋒視窗右上角的 [新增] 按鈕

6.  按一下 [不在資源庫內的應用程式]。

7.  在 [名稱] 文字方塊中輸入應用程式的名稱。 選取 [新增]。

稍候片刻，您便能看見應用程式的設定刀鋒視窗。

## <a name="configure-the-application-for-password-single-sign-on"></a>設定應用程式使用密碼單一登入

若要設定應用程式使用單一登入，請依照下列步驟執行：

1.  開啟 [**Azure 入口網站**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

  * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您要設定單一登入的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [單一登入]。

8.  選取 [以密碼為基礎的登入] 模式。

9.  輸入**登入 URL**。 這是使用者輸入使用者名稱和密碼來登入的 URL。 確保在 URL 看得到登入欄位。

10. 將使用者指派至應用程式。

11. 此外，您也可以選取使用者資料列，按一下 [更新認證]，然後代表使用者輸入使用者名稱和密碼，以代表使用者提供認證。 否則，系統會提示使用者在啟動時自行輸入認證。

## <a name="assign-a-user-to-an-application-directly"></a>將使用者直接指派至應用程式

若要直接將一或多個使用者指派至應用程式，請依照下列步驟執行︰

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

15. 按一下 [指派] 按鈕，將應用程式指派至選取的使用者。

## <a name="assign-an-application-to-a-group-directly"></a>將應用程式直接指派至群組

若要直接將一或多個群組指派至應用程式，請依照下列步驟執行︰

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式]，以檢視所有應用程式的清單。

  * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  從清單中選取您想要指派使用者的應用程式。

7.  應用程式載入之後，按一下應用程式左邊瀏覽功能表中的 [使用者和群組]。

8.  按一下 [使用者和群組] 清單頂端的 [新增] 按鈕，以開啟 [新增指派] 刀鋒視窗。

9.  從 [新增指派] 刀鋒視窗按一下 [使用者和群組] 選取器。

10. 在 [依姓名或電子郵件地址搜尋] 搜尋方塊中，輸入您有興趣指派之群組的**完整群組名稱**。

11. 將滑鼠停留在清單中的**群組**上方，以顯示**核取方塊**。 按一下群組設定檔照片或標誌旁邊的核取方塊，將使用者新增至 [已選取] 清單。

12. **選擇性︰**如果您想要**新增多個群組**，請在 [依姓名或電子郵件地址搜尋] 搜尋方塊中，輸入另一個**完整群組名稱**，然後按一下核取方塊，將此群組新增至 [已選取] 清單。

13. 當您完成選取群組時，按一下 [選取] 按鈕，將它們新增到要指派給應用程式的使用者和群組清單。

14. **選擇性︰**按一下 [新增指派] 刀鋒視窗中的 [選取角色] 選取器，以選取要指派給您已選取群組的角色。

15. 按一下 [指派] 按鈕，將應用程式指派給選取的群組。

稍待片刻，您已選取的使用者便能在存取面板中啟動這些應用程式。

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](active-directory-application-proxy-sso-using-kcd.md)
