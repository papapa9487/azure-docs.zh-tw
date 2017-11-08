---
title: "Azure AD 自助式密碼重設概觀 | Microsoft Docs"
description: "Azure AD 自助式密碼重設可為貴組織做些什麼？"
services: active-directory
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
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>適用於 IT 專業人員的 Azure AD 自助式密碼重設

Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR) 可讓使用者在需要的時間和地點自行重設密碼，同時允許系統管理員控制其密碼重設的方式。 使用者不再需要僅為了重設其密碼而呼叫技術支援中心。

* **自助式密碼變更** – 使用者知道他們的密碼，但需要變更為新的項目。
* **自助式密碼重設** – 使用者無法登入，且需要使用一或多個下列已驗證的驗證方法來重設其密碼。
   * 傳送簡訊至已驗證的行動電話
   * 撥打電話至已驗證的行動電話或辦公室電話
   * 傳送電子郵件給已驗證的次要電子郵件帳戶
   * 回答其安全性問題
* **自助帳戶解除鎖定** – 使用者無法使用其密碼登入已遭鎖定，且需要在不需要系統管理員介入的情況下使用其驗證方法解除鎖定其帳戶。

## <a name="why-choose-azure-ad-self-service-password-reset"></a>為何選擇 Azure AD 自助式密碼重設

* **降低成本**，技術服務人員輔助密碼重設通常佔 IT 組織支援呼叫的 20%。 
* **改善使用者體驗**和**降低技術服務人員音量**，做法是賦予使用者立即解決其密碼問題的能力，而不需呼叫技術服務人員或開啟支援要求。
* **發揮行動力**因為使用者可隨時隨地重設密碼。
* **維持控制**安全性原則。 系統管理員可以持續強制執行其現有的原則。

如果您已準備就緒，即可利用我們的[快速入門指引](active-directory-passwords-getting-started.md)開始使用 Azure AD SSPR，並且快速地讓使用者重設自己的密碼。

## <a name="azure-ad-self-service-password-reset-availability"></a>Azure AD 自助式密碼重設可用性

視您的訂用帳戶而定，Azure AD 自助式密碼重設會以 3 個層級提供。

* **Azure AD Free** – 僅限雲端的系統管理員可以重設自己的密碼。
* **Azure AD Basic** 或任何**付費 Office 365 訂用帳戶** – 僅限雲端的使用者可以重設自己的密碼。
* **Azure AD Premium** – 任何使用者或系統管理員，包括僅限雲端、同盟或密碼同步使用者可以重設自己的密碼。 內部部署密碼需要啟用密碼回寫。

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD 價格、SLA、更新和藍圖

您可在下列網站中找到關於授權、定價和未來計劃的更多詳細資訊。

* [**Azure AD 價格詳細資料**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Office 365 價格**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Azure 服務等級協定**](https://azure.microsoft.com/support/legal/sla/)
* [**Microsoft Online Services 的服務等級協定**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Azure 更新**](https://azure.microsoft.com/updates/)
* [**Azure 藍圖**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>後續步驟

* 準備好開始使用 SSPR 嗎？ [設定 Azure AD 自助式密碼重設](active-directory-passwords-getting-started.md).
* 使用我們[**首度發行指南**](active-directory-passwords-best-practices.md)中的指引來為使用者計劃成功的 SSPR 部署。
* [重設或變更您的密碼](active-directory-passwords-update-your-own-password.md)。
* [註冊自助式密碼重設](active-directory-passwords-reset-register.md)。