---
title: "使用範圍篩選器佈建應用程式 | Microsoft Docs"
description: "了解如果某個物件無法滿足您的業務需求，如何使用範圍篩選器來防止實際佈建 App 中支援使用者佈建自動化的物件。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: e40ba3d3035d5c04017a8ed558981b01fae40a13
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>含範圍篩選器的屬性型應用程式佈建
本節的目標在於說明如何使用範圍篩選器定義會決定哪些使用者會佈建到應用程式的屬性型規則。

## <a name="scoping-filter-use-cases"></a>範圍篩選器使用案例

範圍篩選器可讓 Azure AD 佈建服務包含或排除擁有符合特定值之屬性的任何使用者。 例如，將使用者從 Azure AD 佈建至銷售團隊所使用的 SaaS 應用程式時，您可以指定只有 "Sales" 之 "Department" 屬性的使用者應該位於佈建範圍中。

範圍篩選器的使用的方式端視佈建連接器的類型而定：

* **從 Azure AD 至 SaaS 應用程式的輸出佈建**：當 Azure AD 為來源系統時，[使用者和群組指派](active-directory-coreapps-assign-user-azure-portal.md)是判斷哪些使用者處於佈建範圍的最常見方法。 這些指派也用於啟用單一登入，並提供單一方法來管理存取與佈建。 根據屬性值，除了指派或取代之外，範圍篩選器可以選擇性地用於篩選使用者。

>[!TIP]
> 您可以根據企業應用程式的指派，將 [範圍]**[](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)** 功能表底下的佈建設定設定為 [同步處理所有使用者和群組]，藉此停用佈建。 使用此選項再加上屬性型範圍篩選器時，可提供比使用群組型指派更快的效能。  

* **從 HCM 應用程式至 Azure AD 和 Active Directory 的輸入佈建**：當 [HCM 應用程式 (例如 Workday)](active-directory-saas-workday-tutorial.md) 為來源系統時，範圍篩選器是判斷應該將哪些使用者從 HCM 應用程式佈建至 Active Directory 或 Azure Active Directory 的主要方法。

根據預設，Azure AD 佈建連接器尚未設定任何屬性型範圍篩選器。 

## <a name="scoping-filter-construction"></a>範圍篩選器建構

範圍篩選器包含一個或多個**子句**。 子句會透過評估每個使用者的屬性，決定哪些使用者可以通過範圍篩選器。 例如，您可能有一個子句要求使用者的 "State" 屬性等於 "New York"，因此只會將 New York 使用者佈建到應用程式。 

單一子句可定義單一屬性值的單一條件。 如果在單一範圍篩選器中建立多個子句，則系統會使用 "AND" 邏輯一起評估這些子句。 也就是說，所有子句都必須評估為 "true"，才能佈建使用者。

最後，您可以針對單一應用程式建立多個範圍篩選器。 如果有多個範圍篩選器，則系統可以使用 "OR" 邏輯一起評估這些篩選器。 也就是說，如果任何已設定之範圍篩選器中的所有子句都評估為 "true"，則會佈建使用者。

Azure AD 佈建服務所處理的每個使用者或群組一律會針對每個範圍篩選器進行個別評估。

例如，請考慮以下的範圍篩選器：

![範圍篩選器](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

根據此範圍篩選器，使用者必須滿足下列條件，才能加以佈建：

1. 他們必須位於 New York
2. 他們必須在工程部門工作
3. 其公司員工識別碼必須介於 1000000 到 2000000 之間
4. 其職稱不得為 Null 或空白

## <a name="creating-scoping-filters"></a>建立範圍篩選器
系統會針對每個 Azure AD 使用者佈建連接器，將範圍篩選器設定為屬性對應的一部分。 下列程序假設您已經針對[其中一個支援的應用程式](active-directory-saas-tutorial-list.md)設定自動佈建，並在該應用程式中新增範圍篩選器。

### <a name="to-create-a-scoping-filter"></a>若要建立範圍篩選器：
1. 在 [Azure 入口網站](https://portal.azure.com)中，移至 [Azure Active Directory] > [企業應用程式] > [所有應用程式] 區段。
2. 選取您已設定自動佈建的應用程式。 範例："ServiceNow"
3. 選取 [佈建] 索引標籤。
4. 在 [對應] 區段中，選取您想要為其設定範圍篩選器的對應。 範例：「同步處理 Azure Active Directory 使用者至 ServiceNow」
5. 選取 [來源物件範圍] 功能表。
6. 選取 [新增範圍篩選器]。
7. 依序選取要比對的來源 [屬性名稱]、[運算子] 和 [屬性值]，來定義子句。 以下是支援的運算子：
   * **EQUALS**：如果評估的屬性完全符合輸入字串值 (區分大小寫)，子句會傳回 "true"。
   * **NOT EQUALS**：如果評估的屬性不符合輸入字串值 (區分大小寫)，子句會傳回 "true"。
   * **IS TRUE**：如果評估的屬性包含布林值 true，子句會傳回 "true"。
   * **IS FALSE**：如果評估的屬性包含布林值 false，子句會傳回 "true"。
   * **IS NULL**：如果評估的屬性是空的，子句會傳回 "true"。
   * **IS NOT NULL**：如果評估的屬性不是空的，子句會傳回 "true"。
   * **REGEX MATCH**：如果評估的屬性符合規則運算式模式，子句會傳回 "true"。 範例：([1-9][0-9]) 符合介於 10 到 99 之間的任何數字
   * **NOT REGEX MATCH**：如果評估的屬性不符合規則運算式模式，子句會傳回 "true"。
8. 選取 [新增新範圍子句]。
9. (選擇性) 重複步驟 7-8，新增其他範圍子句。
10. 在 [範圍篩選器標題] 中，新增您範圍篩選器的名稱。
11. 選取 [確定] 。
12. 在 [範圍篩選器] 畫面上再次選取 [確定] \(或重複步驟 6-11，新增另一個範圍篩選器\)。
13. 在 [屬性對應] 畫面上，選取 [儲存]。 

>[!IMPORTANT] 
> 儲存新的範圍篩選器會為應用程式觸發新的完整同步處理，其中來源系統中的所有使用者都會根據新的範圍篩選器，再次進行估。 如果應用程式中的使用者先前在佈建範圍內，但超出範圍，則其帳戶在應用程式中將會遭到停用或解除佈建。


## <a name="related-articles"></a>相關文章
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [SaaS 應用程式的自動化使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)
* [自訂使用者佈建的屬性對應](active-directory-saas-customizing-attribute-mappings.md)
* [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [帳戶佈建通知](active-directory-saas-account-provisioning-notifications.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](active-directory-scim-provisioning.md)
* [如何整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)


