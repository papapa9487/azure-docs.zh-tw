---
title: "使用 Azure AD 存取權檢閱來管理來賓存取權 | Microsoft Docs"
description: "使用 Azure Active Directory 存取權檢閱，以作為群組成員或指派給應用程式的方式管理來賓使用者"
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
ms.openlocfilehash: d67b12eaac17e278724ddf8670e65afba235f099
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>使用 Azure AD 存取權檢閱來管理來賓存取權


您可以搭配使用 Azure Active Directory (Azure AD) 與 [Azure AD B2B 功能](active-directory-b2b-what-is-azure-ad-b2b.md)，輕鬆地跨組織界限啟用共同作業。 [系統管理員](active-directory-b2b-admin-add-users.md)或[其他使用者](active-directory-b2b-how-it-works.md)可邀請來自其他租用戶的來賓使用者。 這也適用於社交識別，例如 Microsoft 帳戶。

您可以輕易確保來賓使用者有適當的存取權。 您可藉由要求來賓本身或決策者參與存取權檢閱，並重新證實 (或「證明」) 來賓的存取權。 檢閱者可以根據 Azure AD 的建議，對每位使用者的持續存取需求給予其意見。 存取權檢閱完成時，您可接著進行變更並移除不再需要存取之來賓的存取權。

> [!NOTE]
> 本文件著重於檢閱來賓使用者的存取權。 如果您想檢閱所有使用者的存取權 (不只來賓)，請參閱[使用存取權檢閱管理使用者存取權](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)。 若您想要檢閱使用者的系統管理角色 (例如全域系統管理員) 成員資格，請參閱[在 Azure AD Privileged Identity Management 中開始存取權檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)。 
>
>

## <a name="prerequisites"></a>必要條件 

存取權檢閱是 Microsoft Enterprise Mobility + Security, E5 中所含之 Azure AD Premium P2 版本的一項功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。 透過這項功能建立檢閱、檢閱存取權或套用檢閱結果的每位使用者都需要授權。

若您計劃讓來賓使用者檢閱自己的存取權，請閱讀來賓使用者授權相關資訊。 如需詳細資訊，請參閱 [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)。

## <a name="create-and-perform-an-access-review-for-guests"></a>建立和執行來賓的存取權檢閱

首先，讓存取權檢閱出現在檢閱者的存取面板上。 以全域管理員身分，移至[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。 

Azure AD 可檢閱來賓使用者的幾個案例。

選取下列其中一個：

 - Azure AD 中的一個群組，成員包含一或多個來賓。
 - 連接到 Azure AD 的應用程式，有一或多個指派給它的來賓使用者。 

然後，您可以決定要求每個來賓檢閱自己的存取權，或要求一或多個使用者檢閱每個來賓的存取權。

 下列各節說明這些案例。

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>要求來賓檢閱其在群組中的成員資格

使用存取權檢閱，可確定已受邀並新增至群組的使用者持續需要存取權。 您可以輕鬆地要求來賓檢閱其在該群組中的成員資格。

1. 若要開始群組的存取權檢閱，請選取檢閱只包含來賓使用者成員，並讓成員自行檢閱。 如需詳細資訊，請參閱[建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。

2. 要求每個來賓檢閱他們自己的成員資格。 根據預設，已接受邀請的每個來賓會收到 Azure AD 的電子郵件，其中包含存取權檢閱的連結。 Azure AD 有來賓如何[檢閱其存取權](active-directory-azure-ad-controls-perform-access-review.md)的指示。

3. 在檢閱者提供輸入後，停止存取權檢閱並套用變更。 如需詳細資訊，請參閱[完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。

4. 除了拒絕持續存取權需求的使用者外，您可能也想要移除未回應的使用者。 未回應的使用者可能不會再收到電子郵件。

5. 如果群組不是使用於存取權管理，您也可移除因未接受邀請而未獲選參與檢閱的使用者。 未接受可能是因為受邀使用者的電子郵件地址拼錯。 如果某群組是作為通訊群組清單，可能某些來賓使用者因為是連絡人物件而未獲選參與。

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>要求贊助者檢閱來賓在群組中的成員資格

您可以要求贊助者 (例如群組的擁有者) 檢閱來賓是否需要持續保有群組成員資格。

1. 若要開始群組的存取權檢閱，請選取檢閱只包含來賓使用者成員。 然後指定一或多個檢閱者。 如需詳細資訊，請參閱[建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。

2. 要求檢閱者提供意見。 根據預設，他們會各自收到來自 Azure AD 的電子郵件，其中的連結可連至存取面板以[執行其存取權檢閱](active-directory-azure-ad-controls-perform-access-review.md)。

3. 在檢閱者提供輸入後，停止存取權檢閱並套用變更。 如需詳細資訊，請參閱[完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>要求來賓檢閱其自己的應用程式存取權

使用存取權檢閱，可確保因特定應用程式而受邀的使用者持續需要存取權。 您可以輕鬆地要求來賓檢閱自己的存取權需求。

1. 若要開始應用程式的存取權檢閱，請選取檢閱只包含來賓，並讓使用者自行檢閱存取權。 如需詳細資訊，請參閱[建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。

2. 要求每個來賓檢閱其自己的應用程式存取權。 根據預設，已接受邀請的每個來賓會收到 Azure AD 的電子郵件，其中的連結可連至您組織的存取面板以執行存取權檢閱。 Azure AD 有來賓如何[檢閱其存取權](active-directory-azure-ad-controls-perform-access-review.md)的指示。

3. 在檢閱者提供輸入後，停止存取權檢閱並套用變更。 如需詳細資訊，請參閱[完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。

4. 除了拒絕持續存取權需求的使用者外，您可能也想要移除未回應的來賓使用者。 未回應的使用者可能不會再收到電子郵件。 您也可以移除未獲選參與的來賓使用者，尤其如果他們最近未獲邀請。 這些使用者未接受邀請，因此無法存取應用程式。 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>要求贊助者檢閱來賓的應用程式存取權

您可以要求贊助者 (例如應用程式的擁有者) 檢閱來賓是否需要應用程式的持續存取權。

1. 若要開始應用程式的存取權檢閱，請選取檢閱只包含來賓。 然後指定一或多個使用者作為檢閱者。 如需詳細資訊，請參閱[建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。

2. 要求檢閱者提供意見。 根據預設，他們會各自收到來自 Azure AD 的電子郵件，其中的連結可連至存取面板以[執行其存取權檢閱](active-directory-azure-ad-controls-perform-access-review.md)。

3. 在檢閱者提供輸入後，停止存取權檢閱並套用變更。 如需詳細資訊，請參閱[完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>要求來賓檢閱其一般的存取權需求

在某些組織中，來賓可能無法得知其群組成員資格。

> [!NOTE]
> 舊版的 Azure 入口網站不允許 UserType 是「來賓」的使用者具備系統管理存取權。 在某些情況下，您目錄中的系統管理員可能已使用 PowerShell 將來賓的 UserType 值變更為「成員」。 如果您的目錄之前有過這項變更，先前的查詢可能不會包括過去具有系統管理存取權限的所有使用者。 在此情況下，您需要變更來賓的 UserType 或在群組成員資格中手動加入來賓。

1. 如果不存在適當的群組，請在 Azure AD 中建立成員是來賓的安全性群組。 比方說，您可以建立手動保留來賓成員資格的群組。 或者，您可針對 Contoso 租用戶中 UserType 屬性值為「來賓」的使用者，建立具有「Contoso 來賓」之類名稱的動態群組。

2. 若要開始該群組的存取權檢閱，請選取檢閱者是成員本身。 如需詳細資訊，請參閱[建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。

3. 要求每個來賓檢閱他們自己的成員資格。 根據預設，已接受邀請的每個來賓會收到 Azure AD 的電子郵件，其中的連結可連至您組織的存取面板以執行存取權檢閱。 Azure AD 有來賓如何[檢閱其存取權](active-directory-azure-ad-controls-perform-access-review.md)的指示。

4. 在檢閱者提供輸入後，停止存取權檢閱。 如需詳細資訊，請參閱[完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。

5. 移除被拒絕、未完成檢閱，或先前未接受邀請之來賓的來賓存取權。 如果某些來賓是連絡人，因為他們未接受邀請而未獲選參與檢閱，您可以使用 Azure 入口網站或 PowerShell 來停用其帳戶。 如果來賓不再需要存取權，也不是連絡人，您可以使用 Azure 入口網站或 PowerShell 從您的目錄中移除其使用者物件。

## <a name="next-steps"></a>後續步驟

[建立群組成員或應用程式存取的存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)







