---
title: "授權︰Azure AD SSPR | Microsoft Docs"
description: "Azure AD 自助式密碼重設授權需求"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: b65a4e49097828e7cd54a29e814befd2d2ac5d88
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD 自助式密碼重設的授權需求

為了讓 Azure Active Directory (Azure AD) 密碼重設為函式，您必須至少在組織中被指派一個授權。 我們不會對密碼重設體驗強制執行每個使用者的授權。 若要符合您的 Microsoft 授權合約規定，您需要將授權指派給任何使用進階功能的使用者。

* **僅限雲端使用者**：Office 365 任何付費的 SKU，或 Azure AD Basic
* **雲端**或**內部部署使用者**：Azure AD Premium P1 或 P2、Enterprise Mobility + Security (EMS) 或 Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>密碼回寫所需的授權

若要使用密碼回寫，您的租用戶中必須已指派下列其中一項授權：

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 (方案 E3)
* Microsoft 365 (方案 E5)

> [!WARNING]
> 獨立的 Office 365 授權方案不支援密碼回寫，而且需要您具備上述其中一個方案，這項功能才能運作。

在下列分頁可以找到額外的授權資訊 (包括成本)：

* [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 功能和功用](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>啟用以群組或使用者為基礎的授權

Azure AD 現在支援以群組為基礎的授權。 系統管理員可以將大量授權指派給一群使用者，而不是一次指派一個授權。 如需詳細資訊，請參閱[指派、驗證授權及解決其問題](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)。

並非所有位置都可使用某些 Microsoft 服務。 系統管理員必須先指定使用者的 [使用位置] 屬性，才能將授權指派給使用者。 可以在 Azure 入口網站中的 [使用者]  >  [設定檔]  >  [設定] 區段之下進行授權指派。 使用群組授權指派時，未指定使用位置的任何使用者在指派期間會繼承目錄的位置。

## <a name="next-steps"></a>後續步驟

* [如何完成 SSPR 成功首度發行？](active-directory-passwords-best-practices.md)
* [重設或變更您的密碼](active-directory-passwords-update-your-own-password.md)
* [註冊自助式密碼重設](active-directory-passwords-reset-register.md)
* [SSPR 使用哪些資料，以及您應該為使用者填入哪些資料？](active-directory-passwords-data.md)
* [哪些驗證方法可供使用者使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [使用 SSPR 的原則選項有哪些？](active-directory-passwords-policy.md)
* [什麼是密碼回寫，且為什麼我需要了解它？](active-directory-passwords-writeback.md)
* [如何回報 SSPR 中的活動？](active-directory-passwords-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](active-directory-passwords-how-it-works.md)
* [我認為有中斷。如何針對 SSPR 進行疑難排解？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)

