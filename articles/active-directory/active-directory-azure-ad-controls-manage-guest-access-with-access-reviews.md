---
title: "使用 Azure AD 存取權檢閱來管理來賓存取權 | Microsoft Docs"
description: "使用 Azure Active Directory 存取權檢閱來管理來賓使用者，該使用者作為群組的成員或指派給應用程式"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b2a76945482e0fe51dc7cc46740cbb8f3f2ff643
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>使用 Azure AD 存取權檢閱來管理來賓存取權


您可以搭配使用 Azure Active Directory 與 [Azure AD B2B 功能](active-directory-b2b-what-is-azure-ad-b2b.md)，輕鬆地跨組織界限啟用共同作業。 [系統管理員](active-directory-b2b-admin-add-users.md)或[使用者](active-directory-b2b-how-it-works.md)可邀請來自其他租用戶的來賓使用者。  這也適用於社交身分識別，例如 Microsoft 帳戶。

您可以輕易確保來賓 使用者有適當的存取權。  您可藉由要求來賓本身或決策者參與存取權檢閱並重新證實 (或「證明」) 來賓的存取權，以執行此工作。 檢閱者可以根據 Azure AD 的建議，對每位使用者的持續存取需求給予其意見。 存取權檢閱完成時，您可接著進行變更並移除不再需要存取之來賓的存取權。

> [!NOTE]
> 本文件著重於檢閱來賓使用者的存取權。 如果您想要檢閱所有使用者存取權，而不只是來賓，請改為閱讀[使用存取權檢閱管理使用者存取權](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)指南。  而且，若您想要檢閱使用者的系統管理角色 (例如全域系統管理員) 成員資格，請參閱[如何在 Azure AD PIM 中開始存取權檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)。 
>
>

## <a name="prerequisites"></a>必要條件 

存取權檢閱可用於 Azure Active Directory 的 Premium P2 版本 (位於 EMS E5 中)。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。  透過這項功能建立評論、檢閱存取權或套用評論的每位使用者，都需要授權。  

如果將要求來賓使用者檢閱其本身的存取權，請閱讀 [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)的來賓使用者授權詳細資訊。

## <a name="creating-and-performing-an-access-review-for-guests"></a>建立和執行來賓的存取權檢閱

首先，讓存取權檢閱出現在檢閱者的存取面板上。  作為全域管理員，移至[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。 

Azure AD 可檢閱來賓使用者的幾個案例。  

選取下列其中一個：
 - Azure Active Directory 中的一個群組，具有一個或多個作為成員的來賓 
 - 或連線到 Azure Active Directory 的一個應用程式，其被指派一或多個來賓使用者，並決定是否要每個來賓檢閱他們自己的存取權，或讓一或多個使用者檢閱每個來賓的存取權。

 下列其中一節會說明每個案例。

* [要求來賓檢閱其在群組中的成員資格](#asking-guests-to-review-their-own-membership-in-a-group)
* [要求贊助者檢閱來賓在群組中的成員資格](#asking-sponsors-to-review-guests-membership-in-a-group)
* [要求來賓檢閱其自己的應用程式存取權](#asking-guests-to-review-their-own-access-to-an-application)
* [要求贊助者檢閱來賓的應用程式存取權](#asking-sponsors-to-review-guests-access-to-an-application) 
* [要求來賓檢閱其一般的存取權需求](#asking-guests-to-review-their-need-for-access-in-general)


### <a name="asking-guests-to-review-their-own-membership-in-a-group"></a>要求來賓檢閱其在群組中的成員資格

存取權檢閱可用來確定已受邀並新增至群組的使用者，持續需要存取權。  要求來賓檢閱其在該群組中的成員資格是一個簡單方法。

1. 啟動群組的存取權檢閱，選取只加入要檢閱的來賓使用者成員，並讓該成員自我檢閱。 深入了解[如何建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。
2. 要求每個來賓檢閱他們自己的成員資格。  根據預設，已接受邀請的每個來賓會收到 Azure AD 的電子郵件，包含存取權檢閱的連結。  Azure AD 有來賓所適用的[如何檢閱其存取權](active-directory-azure-ad-controls-perform-access-review.md)指示。
3. 一旦檢閱者提供意見，便停止存取權檢閱並套用變更。 深入了解[如何完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。 
4. 除了拒絕持續存取需求的使用者外，您可能也想要移除未回應的使用者。  這是因為未回應的使用者可能不會再接收電子郵件。
5. 如果群組不是使用於存取權管理，您也可能希望移除未選擇參與檢閱的使用者，因為他們並未接受其邀請。  這可能表示已拼錯受邀使用者的電子郵件地址。  不過，若正在將群組作為通訊群組清單，則某些來賓使用者可能尚未選擇參與，因為他們是連絡人物件。

### <a name="asking-sponsors-to-review-guests-membership-in-a-group"></a>要求贊助者檢閱來賓在群組中的成員資格

您可以要求贊助者 (例如群組的擁有者) 檢閱來賓對持續保有群組成員資格的需求。

1. 啟動群組的存取權檢閱，選取只加入要檢閱的來賓使用者成員，並指定一或多個檢閱者。 深入了解[如何建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。
2. 要求檢閱者提供意見。 根據預設，他們會各自收到來自 Azure AD 的一封電子郵件，內含存取面板的連結，他們將會在其中[執行其存取權檢閱](active-directory-azure-ad-controls-perform-access-review.md)。
3. 一旦檢閱者提供意見，便停止存取權檢閱並套用變更。 深入了解[如何完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。

### <a name="asking-guests-to-review-their-own-access-to-an-application"></a>要求來賓檢閱其自己的應用程式存取權

存取權檢閱可用來確定已針對特定應用程式邀請的使用者持續需要存取權。  要求來賓檢閱其自己的存取權需求是一個簡單的方法。

1. 啟動應用程式的存取權檢閱，選取僅加入要檢閱的來賓，並讓該使用者檢閱其自己的存取權。 深入了解[如何建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。
2. 要求每個來賓檢閱其自己的應用程式存取權。  根據預設，已接受邀請的每個來賓會在您組織的存取面板中收到 Azure AD 的電子郵件，包含存取權檢閱的連結。  Azure AD 有來賓所適用的[如何檢閱其存取權](active-directory-azure-ad-controls-perform-access-review.md)指示。
3. 一旦檢閱者提供意見，便停止存取權檢閱並套用變更。 深入了解[如何完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。
4. 除了拒絕持續存取需求的使用者外，您可能也想要移除未回應的來賓使用者，因為未回應的使用者可能不會再接收電子郵件。  您也可能想要移除未選擇參與的來賓使用者，尤其是最近未受邀的來賓，因為這些使用者尚未接受其邀請，因此沒有應用程式的存取權。 

### <a name="asking-sponsors-to-review-guests-access-to-an-application"></a>要求贊助者檢閱來賓的應用程式存取權


您可以要求贊助者 (例如應用程式的擁有者) 檢閱來賓對持續存取應用程式的需求。

1. 啟動應用程式的存取權檢閱，選取只加入要檢閱的來賓，並將一或多個使用者指定為檢閱者。 深入了解[如何建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。
2. 要求檢閱者提供意見。 根據預設，他們會各自收到來自 Azure AD 的一封電子郵件，內含存取面板的連結，他們將會在其中[執行其存取權檢閱](active-directory-azure-ad-controls-perform-access-review.md)。
3. 一旦檢閱者提供意見，便停止存取權檢閱並套用變更。 深入了解[如何完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。

### <a name="asking-guests-to-review-their-need-for-access-in-general"></a>要求來賓檢閱其一般的存取權需求

在某些組織中，來賓可能無法得知其群組成員資格。

> [!NOTE]
> 較早版本的 Azure 入口網站不允許擁有 UserType 為 Guest 的使用者取得系統管理存取權，並在某些情況下，您目錄中的系統管理員可能已將來賓的 UserType 值變更為 Member (使用 PowerShell)。  如果您的目錄發生過這種情況，則上述查詢可能不含過去擁有系統管理存取權限的所有來賓使用者，因此您必須變更該來賓的 UserType，或在群組成員資格中手動加入它們。

1. 如果不存在適當的群組，請在 Azure AD 中將來賓作為成員以建立安全性群組。  比方說，您可能想要手動建立群組並保留來賓的成員資格。  或者，您可能想要針對 Contoso 租用戶中 UserType 屬性值為 Guest 的使用者，建立具有「Contoso 來賓」之類名稱的動態群組。
2. 為該群組啟動存取權檢閱，選取作為成員本身的檢閱者。 深入了解[如何建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。
3. 要求每個來賓檢閱他們自己的成員資格。  根據預設，已接受邀請的每個來賓會在您組織的存取面板中收到 Azure AD 的電子郵件，包含存取權檢閱的連結。  Azure AD 有來賓所適用的[如何檢閱其存取權](active-directory-azure-ad-controls-perform-access-review.md)指示。
4. 一旦檢閱者提供意見，便停止存取權檢閱。 深入了解[如何完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。
5. 移除被拒絕、未完成檢閱，或未曾接受其邀請之來賓的來賓存取權。   如果某些來賓是選取要參與檢閱的連絡人 (因為他們先前並未接受邀請)，則您可能希望使用 Azure 入口網站或 PowerShell 來停用其帳戶，使其無法登入。  如果來賓不再需要存取權且不是連絡人，則可使用 Azure 入口網站或 PowerShell，從您的目錄移除其使用者物件。

## <a name="next-steps"></a>後續步驟

- [建立群組成員或應用程式存取的存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)







