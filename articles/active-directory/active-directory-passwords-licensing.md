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
ms.date: 08/30/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 5445a479fd6893048eb8ff356fa829a3dcd5f7d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD 自助式密碼重設的授權需求

為了讓 Azure AD 密碼重設為函式，您**必須至少在組織中被指派一個授權**。 我們不會對密碼重設體驗強制執行每個使用者的授權。 若要符合您的 Microsoft 授權合約規定，您需要將授權指派給任何使用進階功能的使用者。

* **僅限雲端使用者** - Office 365 (O365) 任何付費的 SKU，或 Azure AD Basic
* **雲端**及/或**內部部署使用者** - Azure AD Premium P1 或 P2、Enterprise Mobility + Security (EMS) 或 Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>密碼回寫所需的授權

若要使用密碼回寫，您的租用戶中必須已指派下列其中一項授權。

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!NOTE]
> 獨立的 Office 365 授權方案**不支援密碼回寫**，而且需要上述其中一個方案，這項功能才能運作。

在下列頁面可以找到額外的授權資訊 (包括成本)：

* [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 功能和功用](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>啟用以群組或使用者為基礎的授權

Azure AD 現在支援以群組為基礎的授權，允許系統管理員將大量授權指派給一群使用者，而不是一次指派一個授權。 [指派、驗證授權及解決其問題](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

並非所有位置都可使用某些 Microsoft 服務。 系統管理員必須先指定使用者的 [使用位置]屬性，才能將授權指派給使用者。 可以在 Azure 入口網站中的 [使用者] > [設定檔] > [設定] 區段之下進行授權指派。 **使用群組授權指派時，未指定使用位置的任何使用者在指派期間會繼承目錄的位置。**

## <a name="next-steps"></a>後續步驟

下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [**快速入門**](active-directory-passwords-getting-started.md) - 開始執行 Azure AD 自助式密碼管理 
* [**資料**](active-directory-passwords-data.md) - 了解所需的資料以及如何將它使用於密碼管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此處提供的指引來規劃 SSPR 並將它部署至使用者
* [**自訂**](active-directory-passwords-customize.md) - 為您的公司自訂 SSPR 體驗的外觀與風格。
* [**報告**](active-directory-passwords-reporting.md) - 探索您的使用者是否、何時、何地存取 SSPR 功能
* [**技術性深入探討**](active-directory-passwords-how-it-works.md) - 深入探索以了解其運作方式
* [**常見問題集**](active-directory-passwords-faq.md) - 如何？ 原因為何？ 何事？ 何地？ 何人？ 何時？ - -您一直想要詢問之問題的答案
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何解決我們看到的 SSPR 常見問題
