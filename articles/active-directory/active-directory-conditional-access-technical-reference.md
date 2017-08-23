---
title: "Azure Active Directory 條件式存取的技術參考 | Microsoft Docs"
description: "透過條件式存取控制，Azure Active Directory 會在驗證使用者時以及允許存取應用程式之前，檢查您挑選的特定條件。 一旦符合這些條件，就會驗證使用者並允許存取應用程式。"
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: db7d8b6b2cbe1604fc1b02cc36780ddd83a4d350
ms.contentlocale: zh-tw
ms.lasthandoff: 05/25/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory 條件式存取的技術參考

## <a name="services-enabled-with-conditional-access"></a>已啟用條件式存取功能的服務

各種 Azure AD 應用程式類型都支援「條件式存取」規則。 此清單包括：


* 已向 Azure 應用程式 Proxy 註冊的應用程式
* Azure 遠端應用程式
* 已開發並已向 Azure AD 註冊的企業營運及多租用戶應用程式
* Dynamics CRM
* 來自 Azure AD 應用程式庫的同盟應用程式
* Microsoft Office 365 Yammer
* Microsoft Office 365 Exchange Online
* Microsoft Office 365 SharePoint Online (包括商務用 OneDrive)
* Microsoft Power BI 
* 來自 Azure AD 應用程式庫的密碼 SSO 應用程式
* Visual Studio Team Services
* Microsoft Teams









## <a name="enable-access-rules"></a>啟用存取規則
您可以依據個別應用程式來啟用或停用每個規則。 當規則為 **ON** 時，系統將會針對存取應用程式的使用者啟用並強制執行這些規則。 當規則為 **OFF** 時，系統就不會使用這些規則，也就不會影響使用者的登入體驗。

## <a name="applying-rules-to-specific-users"></a>將規則套用到特定的使用者
您可以透過設定 [套用對象] ，根據安全性群組，將規則套用到特定的幾組使用者。 [套用對象] 可以設定為 [所有使用者] 或 [群組]。 設定為 [所有使用者]  時，會將規則套用到任何可以存取應用程式的使用者。 [群組]  選項可允許選取特定的安全性和通訊群組，系統將只會針對這些群組強制執行規則。

部署規則時，通常會先將它套用到一組有限的使用者，即試驗群組的成員。 完成後，規則就可以套用至 [所有使用者] 。 這會造成規則對組織中的所有使用者強制執行。

您也可以使用 [例外]  選項來免除對選取的群組套用原則。 這些群組的任何成員即使出現在包含群組中，也不必套用原則。

## <a name="at-work-networks"></a>「工作時」網路
使用「工作時」網路的條件式存取規則會依賴 Azure AD 中已設定的可信任 IP 位址範圍，或者從 AD FS 中使用「公司網路內部」宣告。 這些規則包含：

* 不在工作時需要多重要素驗證
* 不工作時封鎖存取

用於指定「工作時」網路的選項

1. 在 [Multi-Factor Authentication 組態頁面](../multi-factor-authentication/multi-factor-authentication-whats-next.md)中設定可信任的 IP位址範圍。 「條件式存取」原則會在每個驗證要求和權杖發行上使用已設定的範圍來評估規則。 
2. 設定使用公司網路內部的宣告，您可以使用 AD FS，將此選項與同盟目錄搭配使用。 若要深入了解公司網路內部宣告，請參閱[信任的 IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)。


## <a name="rules-based-on-application-sensitivity"></a>根據應用程式敏感性的規則
規則是依據個別應用程式來設定，以允許對高價值服務進行保護，而不影響對其他服務的存取。 條件式存取規則可以在應用程式的 [設定]  索引標籤上設定。 

目前提供的規則︰

* **需要多重要素驗證**
  
  * 所有套用了這個原則的使用者都必須透過 Multi-Factor Authentication 至少驗證一次。
* **不在工作時需要多重要素驗證**
  
  * 如果套用了這個原則，所有使用者如果是從非工作遠端位置存取服務，都必須至少執行一次 Multi-Factor Authentication。 如果他們從工作位置移到遠端位置，則在存取服務時，就必須執行 Multi-Factor Authentication。
* **不工作時封鎖存取** 
  
  * 當使用者從工作位置移至遠端位置時，如果套用了 [不工作時封鎖存取] 原則，他們就會被封鎖。  當他們回到工作位置時，就會再次允許他們存取。

## <a name="related-topics"></a>相關主題
* [保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取](active-directory-conditional-access.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)


