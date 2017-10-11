---
title: "Azure Active Directory B2C：開發人員的自訂原則使用注意事項 | Microsoft Docs"
description: "開發人員在使用自訂原則來設定和維護 Azure AD B2C 時的注意事項"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.openlocfilehash: a5f222e5b11e05286152a9f1cc55d2c3fc27a9dc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Azure Active Directory B2C 自訂原則公開預覽版的版本資訊
所有 Azure Active Directory B2C (Azure AD B2C) 客戶現在已可評估自訂原則功能集 (公開預覽狀態)。 此功能集的適用對象是正在建置最複雜身分識別解決方案的進階身分識別開發人員。  

目前，此功能集需要開發人員直接透過 XML 檔案編輯來設定識別體驗架構。 用這種方式進行設定的效果最好，卻也複雜。 使用識別體驗架構的進階身分識別開發人員應該要規劃投入時間，以完成逐步解說課程和閱讀參考文件。 

## <a name="features-included-in-this-public-preview"></a>此公開預覽版包含的功能
透過公開預覽中所引進的新功能，開發人員可以執行下列工作：<br>

* 使用自訂原則來撰寫和上傳自訂驗證使用者旅程。 
   * 逐步將使用者旅程描述為宣告提供者之間的交換。 
   * 定義使用者旅程中的條件式分支。 
* 將啟用 REST API 功能的服務整合到您的自訂驗證使用者旅程。  
* 新增符合 OpenIDConnect 標準的識別提供者同盟。 <br>
* 新增遵守 SAML 2.0 通訊協定的識別提供者同盟。 

## <a name="terms-of-the-public-preview"></a>公開預覽版的條款

* 建議您只有在進行評估時才使用新功能。<br>
* 新功能不適合在生產環境中使用。<br>
* 服務等級協定 (SLA) 不適用於新功能。 <br>
* 您可以透過標準支援管道來提出支援要求。 <br>
* 正式運作的日期還未確定。<br>
* 若案例和使用者旅程超過 Azure AD B2C 產品可作為客戶身分識別與存取管理 (CIAM) 平台的許可範圍，Microsoft 會自行判斷 (以及因為任何原因) 而將其加上旗標並加以拒絕或施加限制。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自訂原則功能集開發人員的責任
手動設定原則會對 Azure AD B2C 基礎平台授與較低層級的存取權，並建立可完全自訂的唯一信任架構。 自訂識別提供者、信任關係、與外部服務的整合以及逐步工作流程會有各種可能的排列組合，因此使用這些組合的進階開發人員會遇到更嚴格的要求。

為了充分獲得公開預覽版的好處，建議使用自訂原則功能集的開發人員遵守下列方針：
* 熟悉識別體驗引擎 (IEEE) 的設定語言和金鑰/密碼管理。
* 完整掌控案例和自訂整合。
* 有系統地執行案例測試。
* 對至少一個開發和測試環境以及一個生產環境遵循軟體開發和分段進行的最佳做法。
* 掌握與您整合之識別提供者和服務的最新開發資訊。 例如，追蹤密碼的變更，以及服務的排程和未排程變更。
* 設定主動式監控，並監視生產環境的回應能力。
* 保有最新的連絡人電子郵件地址，並持續回應 Microsoft 即時網站小組的電子郵件。
* 在 Microsoft 即時網站小組的建議下及時採取動作。 


>[!NOTE]
>這些功能最後可能會包含在 Azure AD 的內建原則中，以方便所有開發人員存取。

## <a name="next-steps"></a>後續步驟
[開始使用自訂原則](active-directory-b2c-get-started-custom.md)。
