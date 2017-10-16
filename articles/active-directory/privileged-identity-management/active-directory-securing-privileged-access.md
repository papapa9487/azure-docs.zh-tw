---
title: "保護 Azure AD 的特殊權限存取 | Microsoft 文件"
description: "這個主題說明在 Azure、Azure Active Directory 和 Microsoft Online Services 之間保護特殊權限存取的方法。"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: 849637fb391576954307bc84bb02e1d8579bf1cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="securing-privileged-access-in-azure-ad"></a>保護 Azure AD 中的特殊權限存取
保護特殊權限存取，是有助保護現代組織企業資產很重要的第一個步驟。 特殊權限帳戶是可管理 IT 系統的帳戶。 網路攻擊者會以這些帳戶為目標，來取得組織資料和系統的存取權。 為了保護特殊權限存取，您應該讓帳戶和系統遠離遭遇惡意使用者的風險。

更多使用者開始透過雲端服務取得特殊權限存取。 這包括 Office365 的全域系統管理員、Azure 訂用帳戶系統管理員和有 VM 或 SaaS 應用程式系統管理存取權限的使用者。

Microsoft 建議您遵循 [Securing Privileged Access (保護特殊權限存取)](https://technet.microsoft.com/library/mt631194.aspx)的這份藍圖。

這些原則是否適用使用 Azure Active Directory、Office 365 或其他 Microsoft 服務和應用程式的客戶，取決於使用者帳戶是由 Active Directory 管理驗證，或在 Azure Active Directory 中管理驗證。 下列章節提供支援保護特殊權限存取之 Azure AD 功能的詳細資訊。

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication
若要加強系統管理員驗證的安全性，您應先要求雙步驟驗證再授與權限。 雙步驟驗證是除了使用使用者名稱與密碼之外，需要再利用其他方法驗證身分的驗證方法。 它可以為使用者登入和交易提供第二層安全性。

Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案，有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 透過一系列簡單的驗證選項提供增強式驗證，選項包括：

- 電話通話
- 文字訊息
- 行動應用程式通知
- 行動應用程式驗證碼
- 協力廠商 OATH 權杖

如需 Azure Multi-Factor Authentication 運作方式的概觀，請參閱以下影片：

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]

如需詳細資訊，請參閱 [MFA for Office 365 和 MFA for Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/)。

## <a name="time-bound-privileges"></a>時間界限權限
某些組織可能會發現它們有太多擁有高權限角色的使用者。 使用者可能因為某個特定活動，像是登入服務，而加入角色中，但之後卻不經常使用這些權限。

若要減少權限的曝光時間並增加您對其用法的了解，將使用者的權限限制為只有「即時」(JIT)，或縮短指派這些角色的持續時間，隨後權限將會自動撤銷。 針對 Azure Active Directory、Azure 資源 (預覽) 與 Microsoft 線上服務，您可以使用 [Azure AD Privileged Identity Management (PIM)](http://aka.ms/AzurePIM)。

![PIM 儀表板][2]

## <a name="attack-detection"></a>攻擊偵測
[Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 提供整合的檢視，綜觀會影響組織身分識別的風險事件和潛在弱點。 Identity Protection 會根據風險事件，計算每位使用者的使用者風險層級，讓您設定以風險為基礎的原則來自動保護您組織的身分識別。 這些原則和 Azure Active Directory 與 EMS 提供的其他條件式存取控制，可以自動封鎖使用者或提供建議，包括重設密碼以及強制 Multi-Factor Authentication。

![Azure AD Identity Protection][3]

## <a name="conditional-access"></a>條件式存取
搭配條件式存取控制時，Azure Active Directory 會在驗證使用者時先檢查您選擇的特定條件，然後才允許存取應用程式。 一旦符合這些條件，使用者就會通過驗證並獲允許存取應用程式。

![使用 MFA 設定條件式存取規則][4]

## <a name="related-articles"></a>相關文章
* 啟用 [Azure Multi-Factor Authentication](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* 啟用 [Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md)
* 啟用 [Azure AD Identity Protection](../active-directory-identityprotection.md)
* 啟用[條件式存取控制](../active-directory-conditional-access.md)

如需建置完整安全性藍圖的詳細資訊，請參閱 [Microsoft Cloud Security for Enterprise Architects (Microsoft 的企業架構雲端安全性)](http://aka.ms/securecustomer) 一文的＜Customer responsibilities and roadmap＞(客戶責任和藍圖) 一節。 如需吸引人的 Microsoft 服務詳細資訊，協助您使用任一這些主題，請連絡您的 Microsoft 代表或瀏覽我們的 [Cybersecurity solutions (網路安全性方案) 網頁](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)。

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png
