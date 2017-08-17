---
title: "教學課程：Azure Active Directory 與 SAP HANA 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 SAP HANA 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: a7e73f6ee763d1005ad85935cf2d8f6b24ecf116
ms.contentlocale: zh-tw
ms.lasthandoff: 07/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>教學課程：Azure Active Directory 與 SAP HANA 整合

在本教學課程中，您會了解如何整合 SAP HANA 與 Azure Active Directory (Azure AD)。

SAP HANA 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 SAP HANA 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 SAP HANA (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 SAP HANA 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 SAP HANA 單一登入的訂用帳戶
- 執行中的 HANA 執行個體，無論是位於任何公用 IaaS、內部部署、Azure VM 還是 Azure 中的 SAP 大型執行個體
- XSA 管理 Web 介面，以及安裝在 HANA 執行個體上的 HANA Studio。

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用 SAP HANA 的生產環境。 先在應用程式的開發或預備環境中測試整合，然後使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 SAP HANA
2. 設定並測試 Azure AD 單一登入

## <a name="adding-sap-hana-from-the-gallery"></a>從資源庫新增 SAP HANA
若要設定將 SAP HANA 整合到 Azure AD 中，您需要從資源庫將 SAP HANA 新增到受管理的 SaaS app 清單。

**若要從資源庫加入 SAP HANA，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **SAP HANA**，從結果面板中選取 [SAP HANA]，然後按一下 [新增] 按鈕以新增應用程式。 

    ![新增應用程式](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 SAP HANA 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 SAP HANA 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 SAP HANA 中的相關使用者之間建立連結關聯性。

在 SAP HANA 中，將 Azure AD 中**使用者名稱**的值，指派為 **Username** 的值，以建立連結關聯性。

若要使用 SAP HANA 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 SAP HANA 測試使用者](#creating-a-sap-hana-test-user)** - 使 SAP HANA 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 SAP HANA 應用程式中設定單一登入。

**若要使用 SAP HANA 設定 Azure AD 單一登入功能，請執行下列步驟：**

1. 在 Azure 入口網站的 [SAP HANA] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. 在 [SAP HANA 網域與 URL] 區段上，執行下列步驟：

    ![網域與 URL 單一登入資訊](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. 在 [識別碼] 文字方塊中，輸入如下：`HA100` 

    b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [SAP HANA 客戶支援小組](https://cloudplatform.sap.com/contact.html)以取得這些值。 

4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >如果憑證沒有作用，則請按一下 Azure AD 中的 [啟用新憑證] 核取方塊來啟用該憑證。 

5. SAP HANA 應用程式需要特定格式的 SAML 判斷提示。 以下螢幕擷取畫面顯示上述的範例。 我們已將 **使用者識別碼** 對應至 **user.mail** 的 **ExtractMailPrefix()** 函數。 如此一來，使用者電子郵件的首碼值便會是唯一的使用者識別碼。 在每次成功的回應中，都會傳送至 SAP HANA 應用程式。

    ![設定單一登入](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. 在 [單一登入] 對話方塊的 [使用者屬性] 區段中：

    a. 在 [使用者識別碼] 下拉式清單中，選取 [ExtractMailPrefix]。
    
    b. 在 [郵件] 下拉式清單中，選取 [user.mail]。

7. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. 若要在 **SAP HANA** 端設定單一登入，請瀏覽至個別的 HTTPS 端點以登入您的 **HANA XSA Web 主控台**。

    > [!Note]
    > 在預設組態中，URL 會將要求重新導向至登入畫面，這需要已通過驗證之 SAP HANA 資料庫使用者的認證才能完成登入程序。 登入的使用者必須擁有執行 SAML 系統管理工作所需的權限。

9. 在 XSA Web 介面中，瀏覽至 **SAML 識別提供者**，並從該處按一下畫面底部的 [+] 按鈕，以顯示 [新增識別提供者資訊] 窗格，並執行下列步驟：

    ![新增識別提供者](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. 在 [新增識別提供者資訊] 窗格中，將您從 Azure 入口網站下載的中繼資料 XML 內容貼到 [中繼資料] 文字方塊中。

    ![[新增識別提供者] 設定](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. 如果 XML 文件的內容有效，剖析程序就會擷取所需資訊以插入到 [一般資料] 畫面區域中的 [主旨]、[實體識別碼] 和 [簽發者] 欄位，以及 [目的地] 畫面區域中的 URL 欄位，例如 [基底 URL] 和 [單一登入 URL] **\(*)**。

    ![[新增識別提供者] 設定](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. 在 [一般資料] 畫面區域的 [名稱] 方塊中，輸入新 SAML SSO 識別提供者的名稱。

    > [!Note]
    > SAML IDP 的名稱是必要項目且必須是唯一的；舉例來說，如果您在 XS Artifact Administration 工具的 [驗證] 畫面區域中，選取 SAML 來作為要供 SAP HANA XS 應用程式使用的驗證方法，則此名稱會出現在所顯示的可用 SAML IDP 清單中。

10. 儲存新的 SAML 識別提供者的詳細資料。 選擇 [儲存] 來儲存 SAML 識別提供者的詳細資料，並將新的 SAML IDP 加入已知 SAML IDP 的清單中。

    ![[儲存] 按鈕](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. 在 HANA Studio [組態] 索引標籤的系統屬性中，只依 **saml** 來篩選設定，並將 **assertion_timeout** 從 **10 秒**調整為 **120 秒**。

    ![assertion_timeout 設定](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![[新增] 按鈕](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![[使用者] 對話方塊](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。
 
### <a name="creating-a-sap-hana-test-user"></a>建立 SAP HANA 測試使用者

若要讓 Azure AD 使用者能夠登入 SAP HANA，必須將他們佈建到 SAP HANA。
SAP HANA 支援預設啟用的 Just-In-Time 佈建。

如果您需要手動建立使用者，請執行下列步驟：

>[!Note]
>您可以變更使用者所使用的外部驗證。
外部使用者會使用外部系統 (例如 Kerberos 系統) 來進行驗證。 如需外部身分識別的詳細資訊，請連絡[網域系統管理員](https://cloudplatform.sap.com/contact.html)。

1. 以系統管理員身分開啟 [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us)，並為 DB-User 啟用 SAML SSO。

    ![建立使用者](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. 勾選 [SAML] 左邊的不可見核取方塊，並遵循 [設定] 連結。

3. 按一下 [新增] 來新增 SAML IDP，然後按一下 [確定] 來選取適當的 SAML IDP。

4. 新增 [外部身分識別] \(例如這裡的 BrittaSimon)，或選擇 [任何]，然後按一下 [確定]。 BrittaSimon here) or choose **"Any"** and click **OK**.

    >[!Note]
    >如果未核取 [任何] 核取方塊，則 HANA 中的使用者名稱必須完全符合 UPN 中網域尾碼前面的使用者名稱 (也就是 BrittaSimon@contoso.com 會在 HANA 中變成 BrittaSimon)。

5. 為了進行測試，請對使用者指派所有 **"XS"** 角色。

    ![指派角色](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > 您只應該提供適用於您使用案例的權限。

6. 儲存使用者。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SAP HANA 的存取權授與 ，讓 Britta Simon 能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派到 SAP HANA，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [SAP HANA]。

    ![指派使用者](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [SAP HANA] 圖格時，應該會自動登入您的 SAP HANA 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png


