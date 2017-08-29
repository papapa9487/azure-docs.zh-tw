---
title: "Azure AD 自助式密碼重設概觀 | Microsoft Docs"
description: "Azure AD 中的自助式密碼重設可為您的組織做些什麼？"
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
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: 9f72a4900f20282827ff939aa41bc4f306e547a3
ms.contentlocale: zh-tw
ms.lasthandoff: 05/20/2017

---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>適用於 IT 專業人員的 Azure AD 自助式密碼重設

「自助式」是世界各地許多 IT 部門內通行的行話，其具有不同的意義。 市場中充斥著各式產品，讓您能夠從雲端或內部部署管理內部部署群組、密碼或使用者設定檔。

Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR) 以容易使用和部署突顯本身的特色。 Azure AD 自助式密碼重設結合了一組功能︰

* 允許使用者管理自己的密碼
  * 從任何裝置
  * 在任何位置
  * 在任何時間
* 符合身為系統管理員的您所定義的原則規定

如果您已準備就緒，即可利用我們的[快速入門指引](active-directory-passwords-getting-started.md)開始使用 Azure AD SSPR，並且快速地讓使用者重設自己的密碼。

## <a name="what-is-possible"></a>可行功用

* **自助式密碼變更**可讓使用者或系統管理員變更其密碼，而不需要系統管理員的協助
* **自助式帳戶解除鎖定**可讓使用者解除鎖定自己的帳戶，而不需要系統管理員的協助
* **自助式密碼重設**可讓使用者或系統管理員自動重設其密碼，而不需要系統管理員的協助。 自助式密碼重設需要 Azure AD Premium 或 Basic - [Azure Active Directory 版本](active-directory-editions.md)。
* **由系統管理員起始的密碼重設**可讓系統管理員在 [Azure 入口網站](https://docs.microsoft.com/azure/azure-portal-overview)中重設使用者或其他系統管理員的密碼。
* **密碼管理活動報告**讓系統管理員得以深入了解其組織內發生的密碼重設和註冊活動- [管理報告](active-directory-passwords-reporting.md)
* **密碼回寫**允許從雲端管理內部部署密碼，讓同盟或密碼同步使用者能夠執行或代表執行上述所有案例。 如果要進行密碼回寫，就必須使用 [Azure AD Premium](active-directory-get-started-premium.md)。

## <a name="why-choose-azure-ad-self-service-password-reset"></a>為何選擇 Azure AD 自助式密碼重設

* **降低成本**，技術服務人員和支援輔助密碼重設通常佔 IT 組織費用的 20%
* **改善使用者體驗**和**降低技術服務人員音量**，做法是賦予使用者立即解決其密碼問題的能力，而不需呼叫技術服務人員或開啟支援要求。
* **發揮行動力**因為使用者可隨時隨地重設密碼。

## <a name="azure-ad-self-service-password-reset-availability"></a>Azure AD 自助式密碼重設可用性

視您的訂用帳戶而定，Azure AD 自助式密碼重設會以 3 個層級提供。

* **Azure AD Free** – 僅限雲端的系統管理員可以重設自己的密碼
* **Azure AD Basic** 或任何**付費 Office 365 訂用帳戶** – 僅限雲端的使用者及僅限雲端的系統管理員可以重設自己的密碼
* **Azure AD Premium** – 任何使用者或系統管理員，包括僅限雲端、同盟或密碼同步使用者可以重設自己的密碼。 內部部署密碼需要啟用密碼回寫。

## <a name="azure-ad-self-service-password-reset-a-sum-of-the-parts"></a>Azure AD 自助式密碼重設 (組件的概要)

下列元件已啟用 Azure AD 中的自助式密碼重設：

* **密碼管理組態入口網站**，您可以在其中控制如何透過 Azure 入口網站在租用戶中管理密碼的選項。
* **密碼重設註冊入口網站**使用者可以在其中自行註冊密碼重設
* **密碼重設入口網站**，使用者可以在其中使用系統管理員所定義的挑戰和使用者提供的答案，以重設其密碼
* **使用者密碼變更入口網站**，使用者可以在其中藉由輸入舊密碼並提供新密碼，以變更自己的密碼
* **密碼管理報告**，可供系統管理員在 Azure 入口網站其中檢視及分析其租用戶的密碼活動報告
* **使用 Azure AD Connect 將密碼回寫至內部部署環境**可讓您從雲端進行內部部署、同盟或密碼同步使用者的管理

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD 價格、SLA、更新和藍圖

在下列頁面上可以找到這些主題的詳細資訊

* [**Azure AD 價格詳細資料**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Office 365 價格**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Azure 服務等級協定**](https://azure.microsoft.com/support/legal/sla/)
* [**Microsoft Online Services 的服務等級協定**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Azure 更新**](https://azure.microsoft.com/updates/)
* [**Azure 藍圖**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>後續步驟

下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [**快速入門**](active-directory-passwords-getting-started.md) - 開始執行 Azure AD 自助式密碼管理 
* [**授權**](active-directory-passwords-licensing.md) - 設定 Azure AD 授權
* [**資料**](active-directory-passwords-data.md) -了解所需的資料以及如何將它使用於密碼管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此處提供的指引來規劃 SSPR 並部署給使用者
* [**自訂**](active-directory-passwords-customize.md) - 為您的公司自訂 SSPR 體驗的外觀與風格。
* [**報告**](active-directory-passwords-reporting.md) - 探索您的使用者是否、何時、何地存取 SSPR 功能
* [**技術性深入探討**](active-directory-passwords-how-it-works.md) - 深入探索以了解其運作方式
* [**常見問題集**](active-directory-passwords-faq.md) - 如何？ 原因為何？ 何事？ 何地？ 何人？ 何時？ - 您一直想要詢問之問題的答案
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何解決我們看到的 SSPR 常見問題


