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
ms.openlocfilehash: c2c5aee3db11255be5a6fe405424c2cbb92c5b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>使用 Azure AD 建立群組成員或應用程式存取的存取權檢閱

當使用者有不再需要的存取權時，存取指派變成「過時」。  為了降低狀態存取權指派的相關風險，系統管理員可以建立存取權檢閱，要求檢閱分派至應用程式的群組成員或使用者。 [管理使用者存取權](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)和[管理來賓存取](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)指南提供這些案例的詳細資訊。  

## <a name="how-to-create-an-access-review"></a>如何建立存取權檢閱


1. 身為全域系統管理員，請移至[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)並變更為 [程式集] 索引標籤。
2. 按一下會保留您想要建立存取權檢閱控制項的程式。  一律會出現一個「預設程式」程式，您也可以建立不同的程式。  比方說，您可以針對每個合規性方案或業務目標建立一個程式。
3. 在程式中按一下 控制項，然後按一下新增 按鈕來新增控制項。
4. 每個存取權檢閱必須有一個名稱，而且可選擇加上描述。  檢閱者就會看到名稱。  
5. 根據預設，存取權檢閱會在它建立的當日啟動，並排定在一個月後結束。  您可以將存取權檢閱的開始和結束日期變更為未來，並持續任何天數。
6. 存取權檢閱可屬於群組的成員，或已指派給應用程式的使用者。  您可以進一步將存取權檢閱限縮成僅檢閱身為成員 (或指派給應用程式) 的來賓使用者，而非檢閱身為成員或可存取應用程式的所有使用者。
7. 您可以選取一或多人以檢閱範圍內的所有使用者，或選擇讓成員檢閱其自己的存取權。  如果資源是群組，您可以要求群組擁有者檢閱。  您也可以要求檢閱者在核准存取權時提供原因。
8. 最後按一下 [儲存]。


## <a name="managing-the-access-review"></a>管理存取權檢閱

依預設，Azure AD 會在檢閱啟動不久後將電子郵件傳給檢閱者。  如果您選擇禁止 Azure AD 傳送電子郵件，請務必讓檢閱者知道有存取權檢閱等待他們完成。  您可以向其顯示[如何檢閱存取權](active-directory-azure-ad-controls-perform-access-review.md)指示，或如果您允許來賓檢閱他們自己的存取權，請顯示[如何檢閱您自己的存取權](active-directory-azure-ad-controls-perform-access-review.md)指示。

請注意，若有部分檢閱者是來賓，則只會透過電子郵件通知已接受邀請的來賓。


當檢閱者在完成其檢閱時，您可以在 Azure AD 儀表板的 [存取權檢閱] 區段中追蹤其進度。 在 [完成檢閱](active-directory-azure-ad-controls-complete-access-review.md)之前，不會變更目錄中的任何存取權限。

## <a name="next-steps"></a>後續步驟

啟動存取權檢閱後，Azure AD 會自動將電子郵件傳給檢閱者，提示其檢閱存取權。 如果使用者未收到電子郵件，您可以在[如何檢閱存取權](active-directory-azure-ad-controls-perform-access-review.md)中傳送指示。  

存取權檢閱期間結束後，或系統管理員已停止存取權檢閱之後，請依照[完成存取權檢閱](active-directory-azure-ad-controls-complete-access-review.md)的步驟來查看並套用結果。


