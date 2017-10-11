---
title: "自訂 Azure AD 屬性對應 | Microsoft Docs"
description: "了解 Azure Active Directory 中 SaaS 應用程式有哪些屬性對應，以及如何修改屬性對應來應付業務需求。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ca2fdc9c68ea0030d938eeaebd57aafa0e2790f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應
Microsoft Azure AD 支援使用者佈建到例如 Salesforce、Google Apps 等等的協力廠商 SaaS 應用程式。 如果您啟用了協力廠商 SaaS 應用程式的使用者佈建，Azure 管理入口網站會以一種稱為「屬性對應」的組態方式控制其屬性值。

在 Azure AD 使用者物件和每個 SaaS app 的使用者物件之間，有一組預先設定的屬性對應。 有些 app 則管理其他類型的物件，例如群組或連絡人。 <br> 
 您可以根據您的業務需求自訂預設的屬性對應。 這表示您可以變更或刪除現有的屬性對應，或建立新的屬性對應。

在 Azure AD 入口網站中，您可以存取這項功能，方法是在 [企業應用程式] 的 [管理] 區段中，按一下 [佈建] 底下的 [對應] 設定。


![Salesforce][5] 

按一下 [對應]設定，可開啟相關的 [屬性對應] 刀鋒視窗。  
SaaS 應用程式需要有幾個屬性對應才能正確運作。 若為必要的屬性，[刪除] 功能就無法使用。


![Salesforce][6]  

在上述範例中，您可以看到 Salesforce 中受管理的物件的 **Username** 屬性填入了連結 Azure Active Directory 物件的 **userPrincipalName** 值。

您可以按一下對應來自訂現有的 [屬性對應]。 這會開啟 [編輯屬性] 刀鋒視窗。

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>了解屬性對應類型
有了屬性對應，您就可以控制屬性在協力廠商 SaaS 應用程式中填入的方式。 支援四種不同的對應類型：

* **直接** - 目標屬性會填入 Azure AD 中連結物件的屬性值。
* **常數** - 目標屬性會填入您所指定的特定字串。
* **運算式** - 目標屬性會根據類似指令碼的運算式結果填入。 
  如需詳細資訊，請參閱[在 Azure Active Directory 中撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)。
* **無** - 目標屬性保留未修改。 不過，如果目標屬性是空的，就會填入您所指定的預設值。

除了這四個基本屬性對應類型，自訂屬性對應還支援選擇性的**預設**值指派的概念。 預設值指派可確保當 Azure AD 中和目標物件都沒有值時，目標屬性會填入某個值。 最常見的設定是將其保留空白。


## <a name="understanding-attribute-mapping-properties"></a>了解屬性對應屬性

在前一個區段中，您已經引進了屬性對應類型屬性。
除了這個屬性之外，屬性對應也會支援下列屬性：

- **來源屬性** - 來源系統的使用者屬性 (例如：Azure Active Directory)。
- **目標屬性** – 目標系統中的使用者屬性 (例如：ServiceNow)。
- **使用此屬性比對物件** – 是否應該將此對應用於唯一識別來源與目標系統之間的使用者。 這通常會在 Azure AD 中的 userPrincipalName 或 mail 屬性上加以設定，通常會對應至目標應用程式中的使用者名稱欄位。
- **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。
- **套用此對應**
    - **一律** – 將此對應套用於使用者建立和更新動作
    - **僅限建立期間** - 僅將此對應套用於使用者建立動作


## <a name="what-you-should-know"></a>您應該知道的事情

Microsoft Azure AD 提供有效率的同步處理程序實作。 在初始化環境中，同步處理期間只會處理需要更新的物件。 更新屬性對應會影響同步處理期間的效能。 更新屬性對應組態需要重新評估所有受管理的物件。 建議您最好不要經常變更屬性對應。

## <a name="next-steps"></a>後續步驟

* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [自動化 SaaS 應用程式使用者佈建/解除佈建](active-directory-saas-app-provisioning.md)
* [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [適用於使用者佈建的範圍篩選器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](active-directory-scim-provisioning.md)
* [帳戶佈建通知](active-directory-saas-account-provisioning-notifications.md)
* [如何整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

