---
title: "Azure Active Directory B2C：自訂原則 | Microsoft Docs"
description: "Azure Active Directory B2C 自訂原則的主題"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1ff398a4-2079-4615-94f1-57de22c0aad6
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 25dada7bc04449c6e527b94d97780d9aef1c33a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C：自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>何謂自訂原則？

自訂原則是定義 Azure AD B2C 租用戶行為的組態檔。 Azure AD B2C 入口網站已經為最常用的身分識別工作預先定義**內建原則**，而自訂原則完全可以由身分識別開發人員編輯，可完成的工作數量幾乎沒有限制。 請繼續閱讀，以判斷自訂原則否是適合您和您的身分識別案例。

## <a name="comparing-built-in-policies-and-custom-policies"></a>比較內建原則和自訂原則

| | 內建原則 | 自訂原則 |
|-|-------------------|-----------------|
|目標使用者 | 所有具備或不具備身分識別專業知識的應用程式開發人員 | 身分識別專業人員︰系統整合人員、顧問和內部身分識別小組。 他們熟悉 OpenIDConnect 流程，也了解身分識別提供者和宣告型驗證 |
|設定方法 | 有 UI 方便使用的 Azure 入口網站 | 直接編輯 XML 檔案，然後上傳至 Azure 入口網站 |
|UI 自訂 | 完整的 UI 自訂，包括 HTML、CSS 和 javascript 支援 (需要自訂網域)<br><br>使用自訂字串支援多語言 | 相同 |
| 屬性自訂 | 標準和自訂屬性 | 相同 |
|權杖和工作階段管理 | 自訂權杖和多個工作階段選項 | 相同 |
|識別提供者| **目前**︰預先定義的本機、社交提供者<br><br>**未來**︰標準式 OIDC、SAML、OAuth | **目前**：標準式 OIDC、OAUTH、SAML<br><br>**未來**：WsFed |
|身分識別工作 (範例) | 使用本機和許多社交帳戶註冊或登入<br><br>自助式密碼重設<br><br>設定檔編輯<br><br>Multi-Factor Auth 案例<br><br>自訂權杖和工作階段<br><br>存取權杖流程 | 使用自訂識別提供者完成與內建原則相同的工作，或使用自訂範圍<br><br>註冊時將使用者佈建在另一個系統中<br><br>使用您自己的電子郵件服務提供者傳送歡迎電子郵件<br><br>使用 B2C 外部的使用者存放區<br><br>透過 API 向受信任的系統驗證使用者所提供的資訊 |

## <a name="policy-files"></a>原則檔

自訂原則以一或多個 XML 格式的檔案表示，各檔案在階層鏈中彼此參考。 XML 元素定義︰宣告結構描述、宣告轉換、內容定義、宣告提供者/技術設定檔和 Userjourney 協調流程步驟等元素。

我們建議使用三種類型的原則檔︰

- **基底檔案**，其中包含大部分的定義，Azure 有提供完整的範例。  建議您儘可能不要變更這個檔案，才有利於原則的疑難排解和長期維護
- **擴充檔案**，其中保存租用戶的唯一組態變更
- **信賴憑證者 (RP) 檔案**，這是以單一工作為主的檔案，直接由應用程式或服務 (也稱為信賴憑證者) 叫用。  如需詳細資訊，請參閱原則檔案定義的相關文件。  每個唯一的工作需要自己的 RP，而根據品牌需求而定，數目可能是「應用程式總數 x 使用案例總數」。


Azure AD B2C 中的內建原則依照上述的 3 檔案模式，但開發人員只能看到信賴憑證者 (RP) 檔案，而入口網站是在背景中變更擴充檔案。

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>使用自訂原則時應該知道的核心概念

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azure 的客戶身分識別和存取管理 (CIAM) 服務。 服務包括︰

1. 特殊用途 Azure Active Directory 形式的使用者目錄，可透過 Microsoft Graph 存取，保存本機帳戶和同盟帳戶的使用者資料 
2. 存取**識別體驗架構**，協調使用者與實體之間的信任，並在它們之間傳遞宣告，以完成身分識別/存取權管理工作 
3. Security Token Service (STS)，發出識別碼權杖、重新整理權杖和存取權杖 (及對等的 SAML 判斷提示)，並驗證它們以保護資源。

Azure AD B2C 依序與識別提供者、使用者、其他系統和本機使用者目錄互動，以達成身分識別工作 (例如：登入使用者、註冊新的使用者、重設密碼)。 建立多方信任和執行這些步驟的基礎平台稱為識別體驗架構，而原則 (也稱為使用者旅程圖或信任架構原則) 會明確定義動作項目、動作、通訊協定及完成的步驟順序。

### <a name="identity-experience-framework"></a>身分識別體驗架構

完全可設定、原則導向、雲端式的 Azure 平台，協調標準通訊協定格式 (例如 OpenIDConnect、OAuth、SAML、WSFed) 實體 (大致上是宣告提供者) 和少數非標準格式 (例如：以 REST API 為基礎的系統對系統宣告交換) 實體之間的信任。 I2E 建立方便使用、白標籤的體驗，支援 HTML、CSS 和 javascript。  識別體驗架構目前僅適用於 Azure AD B2C 服務的情況，且優先用於 CIAM 相關的工作。

### <a name="built-in-policies"></a>內建原則

預先定義的設定檔會引導 Azure AD B2C 的行為，以執行最常見的身分識別工作 (也就是使用者註冊、登入，密碼重設)，並與也在 Azure AD B2C 中預先定義受信任關係的受信任方 (例如 Facebook 識別提供者、LinkedIn、Microsoft 帳戶、Google 帳戶) 互動。  未來，內建原則也可能支援自訂企業領域內常用的識別提供者，例如 Azure Active Directory Premium、Active Directory ADFS、Salesforce 識別碼提供者等。


### <a name="custom-policies"></a>自訂原則

組態檔，定義 Azure AD B2C 租用戶中識別體驗架構的行為。 自訂原則可以一或多個 XML 檔案的形式存取 (請參閱原則檔案定義)，當信賴憑證者 (例如應用程式) 叫用時，會由識別體驗架構執行這些檔案。 身分識別開發人員可以直接編輯自訂原則，可完成的工作數量幾乎沒有限制。 設定自訂原則的開發人員必須仔細定義信任關係，以包含中繼資料端點、精確的宣告交換定義，並設定每個識別提供者所需的祕密、金鑰和憑證。

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>識別體驗架構 Trustframeworks 的原則檔案定義

### <a name="policy-files"></a>原則檔

自訂原則以一或多個 XML 格式的檔案表示，各檔案在階層鏈中彼此參考。 XML 元素定義：宣告結構描述、宣告轉換、內容定義、宣告提供者/技術設定檔和使用者旅程圖協調流程步驟等元素。  我們建議使用三種類型的原則檔︰

- **基底檔案**，其中包含大部分的定義，Azure 有提供完整的範例。  建議您儘可能不要變更這個檔案，才有利於原則的疑難排解和長期維護
- **擴充檔案**，其中保存租用戶的唯一組態變更
- **信賴憑證者 (RP) 檔案**，這是以單一工作為主的檔案，直接由應用程式或服務 (也稱為信賴憑證者) 叫用。  如需詳細資訊，請參閱原則檔案定義的相關文件。  每個唯一的工作需要自己的 RP，而根據品牌需求而定，數目可能是「應用程式總數 x 使用案例總數」。

![原則檔類型](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| 原則檔類型 | 範例檔案名稱 | 建議使用 | 繼承自 |
|---------------------|--------------------|-----------------|---------------|
| 基底 |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | 包括核心宣告結構描述、宣告轉換、宣告提供者，以及 Microsoft 所設定的使用者旅程圖<br><br>儘可能不要變更此檔案 | None |
| 擴充 (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | 在此彙總基底檔案的變更<br><br>已修改的宣告提供者<br><br>已修改的使用者旅程圖<br><br>您自己的自訂結構描述定義 | 基底檔案 |
| 信賴憑證者 (RP) | B2C_1A_sign_up_sign_in.xml| 在此變更權杖圖形和工作階段設定| Extensions(EXT) 檔案 |

### <a name="inheritance-model"></a>繼承模型

當應用程式呼叫 RP 原則檔時，B2C 中的識別體驗架構將先從基底原則檔、再從擴充原則檔、最後從 RP 原則檔，新增所有元素，以組合目前生效的原則。  RP 檔案會覆寫擴充原則檔中相同類型和名稱的元素，而擴充原則檔會覆寫基底原則檔。

Azure AD B2C 中的**內建原則**依照上述的 3 檔案模式，但開發人員只能看到信賴憑證者 (RP) 檔案，而入口網站是在背景中變更擴充檔案。  整個 Azure AD B2C 共用一個基底原則檔，此檔案由 Azure B2C 小組控制，且經常更新。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始使用自訂原則](active-directory-b2c-get-started-custom.md)
