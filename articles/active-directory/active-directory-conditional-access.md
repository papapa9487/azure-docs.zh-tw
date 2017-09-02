---
title: "Azure 傳統入口網站中的條件式存取 | Microsoft Docs"
description: "在驗證對應用程式的存取權時，使用 Azure 傳統入口網站中的條件式存取控制來檢查特定的條件。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: da3f0a44-1399-4e0b-aefb-03a826ae4ead
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: calebb
ms.custom: oldportal
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: b19fd8c0c41638ffec5fda32b6b1a855be2abc7b
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017

---
# <a name="conditional-access-in-the-azure-classic-portal"></a>Azure 傳統入口網站中的條件式存取

本主題是關於 Azure 傳統入口網站中的條件式存取。 如需 Azure Active Directory 中的條件式存取的最新資訊，請參閱 [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)。


Azure Active Directory (Azure AD) 中的控制功能可提供一些簡單的方式，協助保護雲端和內部部署環境中的資源。 條件式存取原則 (如 Multi-Factor Authentication) 可以協助防範認證遭竊和遭到網路釣魚的風險。 其他條件式存取原則可協助保護貴組織的資料安全。 例如，除了需要認證以外，您可能還有一個原則：只有在行動裝置管理系統 (如 Microsoft Intune) 中的註冊裝置可以存取貴組織的敏感性服務。

## <a name="prerequisites"></a>必要條件
Azure AD 條件式存取是 [Azure Active Directory Premium](http://www.microsoft.com/identity) 的一項功能。 每個存取已套用條件式存取原則之應用程式的使用者，都必須有 Azure AD Premium 授權。 您可以在[未經授權的使用者報告](https://aka.ms/utc5ix)中深入了解授權需求。

## <a name="how-is-conditional-access-control-enforced"></a>如何強制執行條件式存取控制？
採用條件式存取控制，Azure AD 會檢查您針對使用者存取應用程式所設定的特定條件。 符合存取需求之後，使用者就會通過驗證並可存取應用程式。  

![條件式存取概觀](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>條件
以下是您可以納入條件式存取原則的條件︰

* **群組成員資格**。 根據在群組中的成員資格，控制使用者的存取權。
* **位置**。 利用使用者的位置來觸發 Multi-Factor Authentication，並在使用者未在受信任網路時使用封鎖控制權。
* **裝置平台**。 使用裝置平台 (例如 iOS、Android、Windows Mobile 和 Windows) 做為原則的套用條件。
* **啟用裝置**。 在裝置原則評估期間會驗證裝置狀態 (啟用或停用)。 如果您停用目錄中遺失或遭竊的裝置，它就無法再滿足原則需求。
* **登入和使用者風險**。 您可以將使用 [Azure AD Identity Protection](active-directory-identityprotection.md) 使用於條件式存取風險原則。 條件式存取風險原則有助於根據風險事件和異常登入活動，為您的組織提供進階保護。

## <a name="controls"></a>控制
以下是您可以用來強制執行條件式存取原則的控制項︰

* **Multi-Factor Authentication**。 您可以透過 Multi-Factor Authentication 來要求增強式驗證。 您可以使用多重要素驗證搭配 Azure Multi-Factor Authentication，或使用結合 Active Directory Federation Services (AD FS) 的內部部署多重要素驗證提供者。 對於未獲授權但已取得有效使用者之認證存取權的使用者，使用 Multi-Factor Authentication 有助於防止其存取資源。
* **封鎖**。 您可以套用使用者位置等條件來封鎖使用者存取。 例如，當使用者未在受信任網路時，您可以封鎖其存取。
* **相容的裝置**。 您可以在裝置層級設定條件式存取原則。 您可以設定一個原則：只有已加入網域的電腦或已在行動裝置管理應用程式中註冊的行動裝置可以存取您組織的資源。 例如，您可以使用 Intune 來檢查裝置相容性，然後向 Azure AD 回報，以便在使用者嘗試存取應用程式強制執行。 如需如何使用 Intune 來保護應用程式和資料的詳細指引，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune)。 您也可以使用 Intune 來強制進行遺失或遭竊裝置的資料保護。 如需詳細資訊，請參閱 [使用 Microsoft Intune 搭配完整或選擇性抹除協助保護您的資料](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)。

## <a name="applications"></a>應用程式
您可以在應用程式層級強制執行條件式存取原則。 設定雲端或內部部署環境中應用程式和服務的存取層級。 此原則會直接套用至網站或服務。 系統會對瀏覽器存取以及可存取服務的應用程式強制執行此原則。

## <a name="device-based-conditional-access"></a>裝置型條件式存取
您可以針對已向 Azure AD 註冊且符合特定條件的裝置，限制對應用程式的存取權。 裝置型條件式存取可保護組織的資源，讓使用者無法嘗試從下列裝置存取資源：

* 不明或未受管理的裝置。
* 不符合貴組織設定之安全性原則的裝置。

您可以根據下列需求設定原則︰

* **已加入網域的裝置**。 設定原則來限制只有已加入內部部署 Active Directory 網域而且已向 Azure AD 註冊的裝置才能存取。 此原則適用於 Windows 桌上型電腦、膝上型電腦和企業平板電腦。
  如需有關如何設定讓已加入網域的裝置自動向 Azure AD 註冊的詳細資訊，請參閱[設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-conditional-access-automatic-device-registration-setup.md)。
* **相容的裝置**。 設定原則來限制只有在管理系統目錄中標示為「相容」的裝置才能存取。 此原則可確保只有符合安全性原則 (例如在裝置上強制執行檔案加密) 的裝置會獲允許存取。 您可以使用此原則來限制來自下列裝置的存取︰
  
  * **已加入網域的 Windows 裝置**。 由部署在混合式組態中的 System Center Configuration Manager (在最新分支中) 所管理。
  * **Windows 10 行動裝置版的工作或個人裝置**。 由 Intune 或由支援的協力廠商行動裝置管理系統所管理。
  * **iOS 和 Android 裝置**。 由 Intune 管理。

存取受裝置型憑證授權單位原則保護之應用程式的使用者，必須從符合此原則需求的裝置存取應用程式。 如果在不符合原則需求的裝置上嘗試存取，則會遭到拒絕。

如需有關如何在 Azure AD 中設定裝置型憑證授權單位原則的資訊，請參閱[設定 Azure Active Directory 連線應用程式的裝置型條件式存取原則](active-directory-conditional-access-policy-connected-applications.md)。

## <a name="resources"></a>資源
請參閱下列資源類別和文章，以深入了解如何設定貴組織的條件式存取。

### <a name="multi-factor-authentication-and-location-policies"></a>Multi-Factor Authentication 和位置原則
* [根據群組、位置和 Multi-Factor Authentication 原則開始使用 Azure AD 連線應用程式的條件式存取](active-directory-conditional-access-azuread-connected-apps.md)
* [支援的應用程式和瀏覽器](active-directory-conditional-access-supported-apps.md)

### <a name="device-based-conditional-access"></a>裝置型條件式存取
* [設定裝置型條件式存取原則來控制對 Azure Active Directory 連線應用程式的存取](active-directory-conditional-access-policy-connected-applications.md)
* [設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-conditional-access-automatic-device-registration-setup.md)
* [針對 Azure Active Directory 存取問題進行疑難排解](active-directory-conditional-access-device-remediation.md)
* [使用 Microsoft Intune 協助保護遺失或遭竊裝置上的資料](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### <a name="protect-resources-based-on-sign-in-risk"></a>根據登入風險保護資源
* [Azure AD Identity Protection](active-directory-identityprotection.md)

### <a name="next-steps"></a>後續步驟
* [條件式存取常見問題集](active-directory-conditional-faqs.md)
* [技術參考](active-directory-conditional-access-technical-reference.md)


