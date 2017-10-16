---
title: "如何完成存取權檢閱 | Microsoft Docs"
description: "在您於 Azure AD Privileged Identity Management 中開始存取權檢閱之後，了解如何完成它並檢視結果"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: ca2a1c7c287e4cf6b1b50cfb0068861b6f525596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中完成存取權檢閱
在 [開始安全性檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)之後，特殊權限角色管理員就可以檢閱特殊權限存取權。 Azure AD Privileged Identity Management (PIM) 會自動傳送電子郵件，提示使用者檢閱其存取權。 如果使用者未收到電子郵件，您可以將 [Azure AD Privileged Identity Management：如何執行安全性檢閱](active-directory-privileged-identity-management-how-to-perform-security-review.md)中的指示傳送給他們。

安全性檢閱時間結束後，或所有使用者都已完成其自我檢閱後，請遵循本文的步驟管理檢閱並查看結果。

## <a name="manage-security-reviews"></a>管理安全性檢閱
1. 移至 [Azure 入口網站](https://portal.azure.com/)，然後在儀表板上選取 [Azure AD Privileged Identity Management] 應用程式。
2. 選取儀表板的 [存取權檢閱]  區段。
3. 選取您想要管理的存取權檢閱。

在存取權檢閱的詳細資料刀鋒視窗上，有一些可管理該檢閱的選項。

![PIM 存取權檢閱按鈕 - 螢幕擷取畫面][1]

### <a name="remind"></a>提醒
如果將存取權檢閱設定成讓使用者自我檢閱，[提醒]  按鈕就會傳送通知。 

### <a name="stop"></a>停止
所有的存取權檢閱都有結束日期，但是您可以使用 [停止]  按鈕來提早結束檢閱。 如果此時有任何使用者尚未受到檢閱，在您停止檢閱之後，他們將無法受到檢閱。 在停止檢閱之後，即無法重新開始該檢閱。

### <a name="apply"></a>套用
在存取權檢閱因您已達到結束日期或手動停止它而完成之後，[套用]  按鈕就會實作該檢閱的結果。 如果使用者的存取權在檢閱中被拒絕，則在這個步驟就會將其角色指派移除。  

### <a name="export"></a>匯出
如果想要手動套用安全性檢閱的結果，您可以匯出檢閱。 [匯出]  按鈕會開始下載 CSV 檔案。 您可以在 Excel 或開啟 CSV 檔案的其他程式中管理結果。

### <a name="delete"></a>刪除
如果對檢閱不再有任何興趣，請刪除它。 [刪除]  按鈕會將檢閱從 PIM 應用程式中移除。

> [!IMPORTANT]
> 刪除執行前，您不會收到警告，因此請務必確定您想要刪除該檢閱。 

## <a name="next-steps"></a>後續步驟
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
