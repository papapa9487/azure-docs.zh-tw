---
title: "使用 Azure AD 存取權檢閱來管理使用者存取權 | Microsoft Docs"
description: "了解如何透過 Azure Active Directory 存取權檢閱，以群組成員資格或指派給應用程式的方式管理使用者存取權"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: c4ea5d797613b214be569c6405a0fc672cae83a0
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>使用 Azure AD 存取權檢閱來管理使用者存取權

透過 Azure Active Directory (Azure AD)，您可以輕易地確認使用者是否有適當的存取權。 您可藉由要求使用者本身或決策者參與存取權檢閱，並重新證實 (或「證明」) 使用者的存取權。 檢閱者可以根據 Azure AD 的建議，對每位使用者的持續存取需求給予其意見。 存取權檢閱完成時，您可接著進行變更並為使用者移除不再需要的存取權。

> [!NOTE]
> 如果您只想檢閱來賓使用者的存取權，而不要檢閱各類使用者的存取權，請參閱[透過存取權檢閱管理來賓使用者存取權](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)。 若您想要檢閱使用者的系統管理角色 (例如全域系統管理員) 成員資格，請參閱[在 Azure AD Privileged Identity Management 中開始存取權檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)。 
>
>

## <a name="prerequisites"></a>必要條件 

存取權檢閱是 Microsoft Enterprise Mobility + Security, E5 中所含之 Azure AD Premium P2 版本的一項功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。 透過這項功能建立檢閱、存取檢閱或套用檢閱結果的每位使用者都需要授權。


## <a name="create-and-perform-an-access-review"></a>建立和執行存取權檢閱

您可以讓一或多個使用者作為存取權檢閱中的檢閱者。  

1. 在 Azure AD 中選取具有一個或多個成員的群組。 或選取連線到 Azure AD 的應用程式，該應用程式上有一或多個指派至此的使用者。 

2. 決定是否要讓每個使用者檢閱自己的存取權，或讓一個或多個使用者檢閱每個人的存取權。

3. 讓存取權檢閱出現在檢閱者的存取面板上。 以全域管理員身分，移至[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

4. 開始存取權檢閱。 如需詳細資訊，請參閱[建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)。

5. 要求檢閱者提供意見。 根據預設，他們會各自收到來自 Azure AD 的一封電子郵件，內含存取面板的連結，他們將會在其中[執行其存取權檢閱](active-directory-azure-ad-controls-perform-access-review.md)。

6. 如果檢閱者有沒有指定的輸入，則您可以要求 Azure AD 將提醒傳送給他們。 依預設，Azure AD 會在結束日期過半時自動將提醒傳送給尚未回應的檢閱者。

7. 檢閱者提供輸入後，便停止存取權檢閱並套用變更。 如需詳細資訊，請參閱[完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)。


## <a name="next-steps"></a>後續步驟

[建立群組成員或應用程式存取的存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)




