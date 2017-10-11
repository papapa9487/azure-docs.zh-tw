---
title: "Azure Active Directory 中企業應用程式管理的新功能 | Microsoft Docs"
description: "了解 Azure Active Directory 中企業應用程式管理的新功能。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 0c32a6719292aa903aa32dfdc4a31114e7a28346
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Azure Active Directory 中企業應用程式管理的新功能 

Azure Active Directory (Azure AD) 有增強的企業應用程式管理工具，其中有些新特性和功能可讓應用程式的管理變得更簡單且有效率。

以下是 [Azure 入口網站](https://portal.azure.com)中的一些 Azure AD 增強功能。

- 改進的應用程式資源庫經驗，簡化了應用程式建立模型，並支援您使用的所有應用程式類型。 
- 全新的快速啟動體驗，可協助您進行應用程式的試驗。 
- 只要按幾下滑鼠就能設定自助式原則。 
- 應用程式 Proxy 的改進、單一登入組態，以及創造自己的應用程式體驗，可讓您執行比以往更多的作業。

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Azure Active Directory 應用程式資源庫的改進功能

新增您喜愛的應用程式，不論這些應用程式是來自[應用程式資源庫](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)、您延伸到雲端的自訂應用程式，或您正在開發的全新應用程式。  按一下 [企業應用程式] 概觀或 [所有應用程式] 刀鋒視窗上的 [新增]，即可開始使用此全新體驗。
 
  ![新增應用程式](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

在資源庫中，您會看到支援使用者佈建的所有精選應用程式顯示在最顯著的位置。  您可以瀏覽各種不同的類別，深入鑽研您想知道的應用程式，您也可以使用搜尋體驗，快速找出您想要整合的應用程式。

  ![應用程式資源庫](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>從一個位置新增自訂應用程式

除了從資源庫新增預先整合的應用程式，您在傳統管理入口網站中使用的所有自訂應用程式組態體驗現在均可用於在新的入口網站中。 不論您是使用應用程式 Proxy 從內部部署延伸應用程式、整合自己的密碼或同盟 SSO 應用程式，或是使用 Application Registry 建立全新的應用程式，您都可以從此單一位置達成目的。

  ![新增自己的應用程式](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**若要開始新增自己的應用程式**：

1. 按一下應用程式資源庫頂端的 [新增您自己的連結]。 
2. 您的面前會出現兩個選項︰[部署現有的應用程式] 或 [開發新的應用程式]。 繼續閱讀，以了解這兩個選項之間的差異以及如何使用它們。

### <a name="deploying-existing-applications"></a>部署現有的應用程式

1. 如果您已在執行應用程式，而只是想要將它整合至 Azure AD 中以便單一登入或佈建，選擇 [部署現有的應用程式] 選項。 挑選您應用程式的名稱，按一下 [新增]。
2. 就這麼簡單！ 您不需要知道應用程式的所有詳細資料，您現在可以隨時瀏覽左側功能表並依照您的喜好設定應用程式，即可設定新應用程式的運作方式。

  ![按一下滑鼠即可新增現有的應用程式](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>開發新的應用程式

1. 如果您正在開發新的應用程式，下列方法可讓您在資源庫中輕鬆取得 Application Registry︰
2. 從 [應用程式資源庫] 按一下 [新增您自己的] 選項，選取 [開發現有的應用程式] 選項，您會看到應用程式新增體驗的快速連結。

  ![按幾下滑鼠即可新增最近開發的應用程式](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>使用 Application Registry 新增應用程式後，您會看到該應用程式出現在企業應用程式清單中，您可以在其中設定單一登入及管理新應用程式的存取原則。

  ![管理企業應用程式之下新應用程式的存取](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>快速啟動︰立即開始使用新的應用程式 

新增應用程式之後，不論它已預先整合或是您自己的應用程式，我們都已建立特別量身訂製的快速啟動體驗，讓您快速地開始新的應用程式體驗。 如果您有系統地遵循每個選項，我們將逐步引導您使用 UI，並且示範如何透過新應用程式的試驗盡快快速啟動。 
 
  ![新的應用程式快速啟動經驗](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 您可以隨時瀏覽這項新的快速啟動體驗，而對任何應用程式而言，只要按一下應用程式左導覽功能表中的 [快速啟動]。


## <a name="updated-application-proxy-configuration"></a>已更新的應用程式 Proxy 組態
現在，假設您新增的其中一個新應用程式正在內部部署環境中執行，而且您想要將它與 Azure AD 整合。  有關新的 Azure AD 入口網站中新應用程式設定體驗的其中一個酷炫新功能就是，藉由從應用程式 Proxy 組態中分割應用程式的登入模式，您現在即可輕鬆地將您公司網路中執行的密碼 SSO 或同盟應用程式直接公開至雲端，而不必建立多個應用程式執行個體。

除此之外，您現在也可以設定您新增的任何新應用程式 (包括支援原生 Windows 驗證經驗的應用程式)，以便直接從新的入口網站搭配 Azure AD 應用程式 Proxy 使用。

  ![設定應用程式以使用整合式 Windows 驗證登入選項](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

若要開始設定採用應用程式 Proxy 的原生 Windows 驗證應用程式︰
1. 在單一登入導覽項目上按一下，然後選擇登入設定刀鋒視窗中的 [整合式 Windows 驗證] 並設定您喜好的設定。
2. 除了支援這些新的驗證模式以外，您現在也可以從自訂網域上傳憑證，以支援在組織內安全端點上執行的應用程式。  
 
   ![上傳要搭配應用程式 Proxy 使用的憑證](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. 若要針對您最愛的內部部署應用程式上傳新憑證，請按一下左側導覽功能表中的 [應用程式 Proxy] 選項，按一下 [憑證] 選取器，然後上傳憑證檔案，以便用來加密我們的雲端端點對您的應用程式的要求。

## <a name="advanced-federated-single-sign-on-configuration"></a>進階同盟單一登入組態

對於目前使用同盟應用程式的使用者而言，SAML 型登入設定刀鋒視窗中有許多新功能。 首先，您現在可以完全自訂、新增、移除及對應在 SAML 權杖中以宣告形式發出的現有使用者屬性。
 
  ![自訂傳遞至同盟應用程式的 SAML 權杖使用者屬性](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


若要查看新的同盟 SSO 組態︰
1. 從左側導覽功能表開啟同盟應用程式的 [單一登入] 刀鋒視窗，並確定已選取 [SAML 型登入] 模式。 
2. 在那裡，啟用 [使用者屬性] 標題底下的核取方塊，以修改傳遞給該應用程式的 SAML 權杖中包含的所有屬性。

您也可以建立、變換及管理用於同盟單一登入的憑證，以及編輯憑證即將到期時可取得通知的對象。 您會在相同單一登入刀鋒視窗上的 [憑證] 標題之下看到這些新選項。
 
  ![建立新憑證、自訂到期通知電子郵件和憑證簽署選項](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>轉送狀態參數
最後，我們還擴充了我們支援的這組 SAML URL 參數以包含 [轉送狀態參數]，這就是使用者在登入完成後將於同盟應用程式內登陸的頁面。 如果您想要將使用者快速送往應用程式內的特定位置，這是非常實用的設定。

  ![設定 SAML 轉送狀態參數](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**若要設定轉送狀態參數**：

1. 在單一登入設定刀鋒視窗上，啟用 [網域和 URL] 標題之下的 [顯示進階 URL 設定] 核取方塊。 
2. 這麼做之後，您就會看到一組新的 URL 輸入方塊出現，讓您進行這項和其他 SAML URL 設定。

## <a name="bring-your-own-password-sso-applications"></a>自備密碼 SSO 應用程式

我們知道並非每個應用程式一開始都支援同盟。 比方說，您新增的其中一個新應用程式或許有自訂登入畫面，可讓使用者以使用者名稱和密碼進行登入。 您仍可使用 [自備應用程式] 功能來整合這些類型的應用程式與 Azure AD，該項功能現在可供您在新的入口網站中進行設定。
 
  ![整合自訂密碼儲存庫存應用程式與 Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**若要查看「自備應用程式」功能**：

1. 為您已新增至 [密碼型登入] 的新自訂應用程式設定單一登入模式之後，輸入應用程式會呈現其登入畫面的 URL，然後按一下 [儲存]。  
2. 這麼做之後，我們會自動擷取使用者名稱和密碼輸入方塊的該 URL，並可讓您使用 Azure AD 並利用存取面板瀏覽器擴充功能將密碼安全地傳輸到該應用程式。

## <a name="configure-self-service-application-access"></a>設定自助式應用程式存取

新增許多新的應用程式之後，您或許想要讓使用者瀏覽這些應用程式並將其新增到自己的存取面板，而不會讓系統管理員身分對您造成困擾。 現在，在此最新版本中，您可以從新的入口網站設定及管理自助式應用程式存取權限。

  ![為密碼 SSO 應用程式設定自助式應用程式存取](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**若要設定和管理自助式應用程式存取**：

1. 首先，您可以從應用程式的左側導覽功能表中選取 [自助式] 選項，並將 [允許使用者要求存取此應用程式？] 選項設定為 [是]。 
2. 這可讓您設定允許誰核准此應用程式的存取權，以及哪個群組將會新增自助式使用者。 此外，如果已針對密碼單一登入設定應用程式，您也會看到另一個選項，可讓您選擇性地允許這些核准者管理指派給應用程式的密碼。

##<a name="feedback"></a>意見反應

我們希望您喜歡使用改進後的 Azure AD 經驗。 請繼續提供意見反應！ 請將您的意見反應和改進想法張貼在我們的[意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal)的**管理員入口網站**區段中。  我們每天都很期待發展酷炫的新功能，並依照您的指導來塑造和定義我們接下來所要發展的項目。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[使用 Azure Active Directory 管理應用程式](active-directory-enable-sso-scenario.md)。



