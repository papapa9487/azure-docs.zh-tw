---
title: "使用 Azure AD，為可存取應用程式的群組成員或使用者完成存取權檢閱 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中，完成可存取應用程式之群組成員或使用者的存取權檢閱。"
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
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: e83bc42d658c4d6304f98b99d0e0942595bd65d2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---

# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>在 Azure AD 中，為可存取應用程式的群組成員或使用者完成存取權檢閱

系統管理員可以[建立存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)，要求檢閱指派給應用程式的群組成員或使用者。 Azure AD 會自動將電子郵件傳給檢閱者，提示其檢閱存取權。 如果使用者未收到電子郵件，您可以在[如何檢閱存取權](active-directory-azure-ad-controls-perform-access-review.md)中傳送指示。  存取權檢閱期間結束後，或系統管理員已停止存取權檢閱之後，請依照本文的步驟來查看並套用結果。

## <a name="viewing-an-access-review-in-the-azure-portal"></a>在 Azure 入口網站中檢視存取權檢閱

1. 移至[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，變更為 [程式集] 索引標籤，並選取包含存取權檢閱控制項的程式。
2. 按一下管理並選取存取權檢閱控制項。  如果程式中有許多控制項，您可以篩選為特定類型的控制項、按其狀態排序，或按存取權檢閱控制項的名稱或建立它之擁有者的顯示名稱進行搜尋。 

## <a name="stopping-a-review-that-has-not-yet-completed"></a>停止尚未完成的檢閱

如果檢閱尚未到達排程的結束日期，系統管理員可按一下 [停止] 按鈕以提早完成，藉此停止檢閱。  如果此時有任何使用者尚未受到檢閱，在您停止檢閱之後，他們將無法受到檢閱。 在停止檢閱之後，即無法重新開始該檢閱。

## <a name="applying-the-changes"></a>套用變更 

完成存取權檢閱後，因已達到結束日期或系統管理員已手動停止它，[套用] 按鈕就會藉由更新群組或應用程式來實作該檢閱的結果。 若在檢閱中拒絕使用者的存取權，則當系統管理員按一下這個按鈕時，Azure AD 將會移除其成員資格或應用程式指派。 

按一下 [套用] 按鈕不會影響源自內部部署目錄中的群組，或動態群組。  如果您想要變更源自於內部部署的群組，則下載結果，並將那些變更套用至該目錄中群組的表示法。

## <a name="downloading-the-results-of-the-review"></a>下載檢閱的結果

若要擷取檢閱的結果，請變更 [核准] 索引標籤，然後按一下 [下載] 按鈕。  可在 Excel 中，或開啟 CSV 檔案的其他程式中檢視產生的 CSV 檔案。

## <a name="optional-deleting-a-review"></a>選擇性：刪除檢閱
如果對檢閱不再有任何興趣，請刪除它。 [刪除] 按鈕會從 Azure AD 移除檢閱。

> [!IMPORTANT]
> 刪除執行前不會收到警告，因此請務必確定您想要刪除該檢閱。
> 
> 

## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 存取權檢閱來管理使用者存取權](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [使用 Azure AD 存取權檢閱來管理來賓存取權](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [管理 Azure AD 存取權檢閱的程式和控制項](active-directory-azure-ad-controls-manage-programs-controls.md)
- [建立群組成員或應用程式存取的存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)
- [在 Azure AD 管理角色中建立使用者的存取權檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)

