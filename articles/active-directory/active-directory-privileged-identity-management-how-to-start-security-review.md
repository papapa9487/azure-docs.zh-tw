---
title: "如何開始存取權檢閱 | Microsoft Docs"
description: "了解如何使用 Azure Privileged Identity Management 應用程式為特殊權限身分識別建立存取權檢閱。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 3e52b731-55f4-4c8a-ba87-9fd34033f52f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2b516e2f05aa883c5e37f5864e5ee8a2b37d3a46
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中開始存取權檢閱
當使用者擁有不再需要的特殊存取權時，角色指派就會變成「過時」。 為了降低與這些過時的角色指派相關聯的風險，特殊權限角色管理員應該定期檢閱使用者獲授與的角色。 本文件涵蓋在 Azure AD Privileged Identity Management (PIM) 中開始存取權檢閱的步驟。

## <a name="start-an-access-review"></a>開始存取權檢閱
> [!NOTE]
> 如果您尚未在 Azure 入口網站的儀表板中新增 PIM 應用程式，請參閱[開始使用 Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) 中的步驟。
> 
> 

在 PIM 應用程式的主頁面，有三種方式可以開始存取權檢閱︰

* [存取權檢閱] > [新增]
* [角色] > [檢閱] 按鈕
* 從角色清單中選取要檢閱的特定角色 > [檢閱] 按鈕

按一下 [檢閱] 按鈕時，隨即會顯示 [開始存取檢閱] 刀鋒視窗。 您將在此刀鋒視窗上為檢閱設定名稱和時間限制、選擇要檢閱的角色，以及決定將由誰執行檢閱。

![開始存取權檢閱 - 螢幕擷取畫面][1]

### <a name="configure-the-review"></a>設定檢閱
若要建立存取權檢閱，您需要為它命名並設定開始和結束日期。

![設定檢閱 - 螢幕擷取畫面][2]

為檢閱設定足夠的時間長度，以便讓使用者能夠完成檢閱。 如果您在結束日期之前即完成檢閱，您一律可以提早停止檢閱。

### <a name="choose-a-role-to-review"></a>選取要檢閱的角色
每個檢閱只針對一個角色。 除非您是從特定的角色刀鋒視窗開始存取權檢閱，否則您現在將必須選擇一個角色。

1. 瀏覽至 [檢閱角色成員資格] 
   
    ![檢閱角色成員資格 - 螢幕擷取畫面][3]
2. 從清單中選擇一個角色。

### <a name="decide-who-will-perform-the-review"></a>決定將由誰執行檢閱
執行檢閱的選項有三個。 您可以將檢閱指派給其他人來完成、可以自行進行檢閱，或者可以讓每個使用者檢閱自己的存取權。

1. 瀏覽至 [選取檢閱者] 
   
    ![選取檢閱者 - 螢幕擷取畫面][4]
2. 選擇其中一個選項：
   
   * **選取檢閱者**︰如果您不知道誰需要存取權，請使用此選項。 使用此選項，您可以指派資源擁有者或群組管理員完成檢閱。
   * **我**︰如果您想要預覽存取權檢閱如何運作，或是想要代表無法檢閱的人員執行檢閱，此選項會相當有用。
   * **成員自我檢閱**：若要讓使用者檢閱自己的角色指派，請使用此選項。

### <a name="start-the-review"></a>開始檢閱
最後，您可以選擇是否要要求使用者在核准其存取權時提供原因。 請依喜好新增檢閱描述，然後選取 [開始] 。

請確定讓使用者知道有等待他們執行的存取權檢閱，並示範 [如何執行存取權檢閱](active-directory-privileged-identity-management-how-to-perform-security-review.md)。

## <a name="manage-the-access-review"></a>管理存取權檢閱
當檢閱者在完成其檢閱時，您可以在 Azure AD PIM 儀表板的 [存取權檢閱] 區段中追蹤其進度。 在 [完成檢閱](active-directory-privileged-identity-management-how-to-complete-review.md)之前，不會變更目錄中的任何存取權限。

在檢閱期間結束之前，您都可以提醒使用者完成其檢閱，或是從 [存取權檢閱] 區段提前停止檢閱。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>PIM 目錄
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png
