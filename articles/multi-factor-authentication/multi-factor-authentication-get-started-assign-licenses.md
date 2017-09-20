---
title: "指派 Azure MFA 的授權 | Microsoft Docs"
description: "了解如何為 Microsoft Azure Multi-Factor Authentication 指派授權。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 514ef423-8ee6-4987-8a4e-80d5dc394cf9
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: 4572913c2bc732c31f38021f9d3ccc34417de875
ms.openlocfilehash: 536968a803e14019c42e7beed4c948ab760cf020
ms.contentlocale: zh-tw
ms.lasthandoff: 02/15/2017

---
# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>將 Azure MFA、Azure AD Premium 或 Enterprise Mobility 授權指派給使用者
如果您已購買 Azure MFA、Azure AD Premium 或 Enterprise Mobility Suite 授權，便不需要建立 Multi-Factor Auth Provider。 將授權指派給使用者後，您就可以開始為使用者啟用 MFA。

## <a name="to-assign-a-license"></a>指派授權
1. 以系統管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 選取左邊的 [Active Directory] 。
3. 在 [Active Directory] 頁面上，在有您要啟用的使用者之目錄上按兩下。
4. 在目錄頁面頂端，選取 [授權] 。
   ![指派授權](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. 在 [授權] 頁面上，選取 [Multi-Factor Authentication]、[Active Directory Premium] 或 [Enterprise Mobility Suite]。  如果您只有一個，則它應該會自動選取。
6. 在頁面底部，按一下 [指派] 。
   ![指派授權](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
7. 在出現的方塊中，於您想要對其指派授權的使用者或群組旁邊按一下。  您應該會看到綠色核取記號出現。
8. 按一下核取記號圖示以儲存變更。
   ![指派授權](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
9. 您應該會看到訊息，指出已指派多少授權以及多少授權已失敗。  按一下 [確定] 。
   ![指派授權](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱[什麼是 Microsoft Azure Active Directory 授權？](../active-directory/active-directory-licensing-what-is.md)

