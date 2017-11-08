---
title: "使用 Azure AD，為可存取應用程式的群組成員或使用者建立存取權檢閱 | Microsoft Docs"
description: "了解如何為可存取應用程式的群組成員或使用者建立存取權檢閱。"
services: active-directory
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
ms.openlocfilehash: ae926e8d3a856c9b488d46b481a1c7b5b581d532
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>使用 Azure AD 建立群組成員或應用程式存取的存取權檢閱

當使用者有不再需要的存取權時，存取指派變成「過時」。 為了降低過時存取權指派的相關風險，系統管理員可以使用 Azure Active Directory (Azure AD) 為群組成員或指派給應用程式的使用者建立存取權檢閱。 如需有關這些案例的詳細資訊，請參閱[管理使用者存取權](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)和[管理來賓存取權](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)。 

## <a name="create-an-access-review"></a>建立存取權檢閱

1. 身為全域系統管理員，請移至[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，並選取 [程式集]。

2. 選取包含您想建立之存取權檢閱控制項的程式。 一律會出現一個「預設程式」程式，您也可以建立不同的程式。 比方說，您可以針對每個合規性方案或業務目標建立一個程式。

3. 在程式中，選取 [控制項]，然後選取 [新增] 加入控制項。

4. 每個存取權檢閱的名稱。 選擇性地提供每個檢閱的描述。 檢閱者就會看到名稱。

5. 設定開始與結束日期。 根據預設，存取權檢閱會在它建立的當日啟動，並在一個月後結束。 您可以將存取權檢閱的開始和結束日期變更為未來，並持續任何天數。

6. 存取權檢閱可針對群組成員或指派給應用程式的使用者執行。 您可以進一步將存取權檢閱限縮成僅檢閱身為成員 (或指派給應用程式) 的來賓使用者，而非檢閱身為成員或可存取應用程式的所有使用者。

7. 選取一或多個人員來檢閱範圍內的所有使用者。 或者，您可以選擇讓成員檢閱自己的存取權。 如果資源是群組，您可以要求群組擁有者檢閱。 您也可以要求檢閱者在核准存取權時提供原因。

8. 最後，選取 [開始]。


## <a name="manage-the-access-review"></a>管理存取權檢閱

依預設，Azure AD 會在檢閱開始不久後傳送電子郵件給檢閱者。 如果您選擇不讓 Azure AD 傳送電子郵件，請務必通知檢閱者有存取權檢閱等待他們完成。 您可以提供他們如何[檢閱存取權](active-directory-azure-ad-controls-perform-access-review.md)的指示。 如果您允許來賓檢閱他們自己的存取權，請提供他們如何[檢閱自己的存取權](active-directory-azure-ad-controls-perform-access-review.md)的指示。

若有部分檢閱者是來賓，則只會透過電子郵件通知已接受邀請的來賓。


當檢閱者完成其檢閱時，您可以在 [存取權檢閱] 區段的 Azure AD 儀表板中追蹤進度。 在[完成檢閱](active-directory-azure-ad-controls-complete-access-review.md)之前，不會變更目錄中的任何存取權限。

## <a name="next-steps"></a>後續步驟

開始存取權檢閱後，Azure AD 會自動傳送電子郵件給檢閱者，提示其檢閱存取權。 如果使用者未收到電子郵件，您可以傳送如何[檢閱存取權](active-directory-azure-ad-controls-perform-access-review.md)的指示給他們。 

存取權檢閱期間結束或系統管理員停止存取權檢閱之後，請依照[完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)的步驟來查看並套用結果。


