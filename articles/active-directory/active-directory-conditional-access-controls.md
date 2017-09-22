---
title: "Azure Active Directory 條件式存取中的控制項 | Microsoft Docs"
description: "了解 Azure Active Directory 條件式存取中的控制項有何作用。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 0f7d847c98e790c542f3a3e666b9a887099a6cbc
ms.contentlocale: zh-tw
ms.lasthandoff: 09/13/2017

---

# <a name="controls-in-azure-active-directory-conditional-access"></a>Azure Active Directory 條件式存取中的控制項 

透過 [Azure Active Directory (Azure AD) 條件式存取](active-directory-conditional-access-azure-portal.md)，您可以控制授權使用者如何存取您的雲端應用程式。 在條件式存取原則中，您可以定義對特定條件 ("when this happens") 做出的回應 ("do this")。 在條件式存取的情境中， 

- "**When this happens**" 稱為**條件陳述式**
- "**Then do this**" 成為**控制項**

![控制](./media/active-directory-conditional-access-controls/11.png)

條件陳述式與控制項的組合代表條件式存取原則。

![控制](./media/active-directory-conditional-access-controls/12.png)

每種控制項都是必須由登入之使用者或系統履行的要求，或是規範使用者在登入後之行為的限制。 

控制項分為兩種： 

- **授與控制項** - 限制存取權限

- **工作階段控制項** - 限制工作階段內的存取權限

本主題說明 Azure AD 條件式存取中的各種控制項。 

## <a name="grant-controls"></a>授與控制

透過授與控制項，您可以藉由選取需要的控制項來全面封鎖存取權限，或允許在滿足其他要求的情況下授與存取權限。 若要實施多個控制項，您可以要求：

- 滿足所有選取的控制項 (*AND*) 
- 滿足一項選取的控制項 (*OR*)

![控制](./media/active-directory-conditional-access-controls/51.png)



### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

您可以使用這個控制項來要求使用者通過 Multi-Factor Authentication 後，才能存取指定的雲端應用程式。 這個控制項支援以下多重要素提供者： 

- Azure Multi-Factor Authentication 

- 結合 Active Directory Federation Services (AD FS) 的內部部署 Multi-Factor Authentication 提供者。
 
對於未獲授權但已取得有效使用者之主要認證存取權的使用者，使用 Multi-Factor Authentication 有助於防止其存取資源。



### <a name="compliant-device"></a>符合規範的裝置

您可以設定以裝置作為基礎的條件式存取原則。 以裝置作為基礎的條件式存取原則目標在於只從信任的裝置授與已設定資源的存取權。 要定義受信任裝置的選項之一，是要求符合規範的裝置。 如需詳細資訊，請參閱[設定 Azure Active Directory 裝置型條件式存取原則](active-directory-conditional-access-policy-connected-applications.md)。

### <a name="domain-joined-device"></a>已加入網域的裝置

設定以裝置作為基礎的條件式存取原則的另一個選項，是要求已加入網域的裝置。 這項需求是指加入內部部署 Active Directory 的 Windows 桌上型電腦、膝上型電腦和企業平板電腦。 如需詳細資訊，請參閱[設定 Azure Active Directory 裝置型條件式存取原則](active-directory-conditional-access-policy-connected-applications.md)。





### <a name="approved-client-app"></a>已核准的用戶端應用程式

由於員工使用行動裝置來處理個人和工作事務，因此即使您不負責管理公司資料，可能也會想要在員工使用裝置存取公司資料時保護資料。
您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來保護公司的資料，並且不受任何行動裝置管理 (MDM) 解決方案影響。


藉由核准的用戶端應用程式，您可以要求嘗試存取雲端應用程式的用戶端應用程式支援 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)。 例如，您可以限制唯有 Outlook 應用程式能存取 Exchange Online。 要求通過核准之用戶端應用程式的條件式存取原則，也稱為[應用程式型條件式存取原則](active-directory-conditional-access-mam.md)。 如需支援的核准用戶端應用程式清單，請參閱[核准的用戶端應用程式需求](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)。



## <a name="session-controls"></a>工作階段控制項

工作階段控制項可讓您限制雲端應用程式內的體驗。 工作階段控制項是由雲端應用程式強制執行，並依賴 Azure AD 對應用程式提供有關工作階段的其他資訊。

![控制](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>使用應用程式強制執行限制

您可以使用這個控制項，要求 Azure AD 將裝置資訊傳遞至雲端應用程式。 這有助於雲端應用程式了解使用者是否來自符合規範的裝置或加入網域的裝置。 此控制項目前僅支援使用 SharePoint 作為雲端應用程式。 視裝置狀態而定，SharePoint 會使用裝置資訊來提供使用者有限或完整的經驗。
若要深入了解如何要求 SharePoint 的有限存取，請參閱[從未受管理的裝置控制存取](https://aka.ms/spolimitedaccessdocs)。



## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。 

