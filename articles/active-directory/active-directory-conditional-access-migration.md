---
title: "Azure 入口網站中的 Migrate 傳統原則 | Microsoft Docs"
description: "Azure 入口網站中的 Migrate 傳統原則。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Azure 入口網站中的 Migrate 傳統原則 


[條件式存取](active-directory-conditional-access-azure-portal.md)是 Azure Active Directory (Azure AD) 的功能，可讓您控制授權使用者如何存取您的雲端應用程式。 雖然目的仍相同，但新版的 Azure 入口網站也導入了條件式存取如何運作的重大改善。 您在 Azure 入口網站外設定的條件式存取原則，可與您正在 Azure 入口網站中建立的新原則並存。 只要您不停用或移除它們，它們仍然會套用至您的環境。 不過，我們建議您將傳統原則遷移到新的 Azure AD 條件式存取原則，因為：

- 新的原則可讓您處理傳統原則無法處理的案例。

- 您可以合併它們以減少必須管理的原則數目。   

本主題可協助您將現有的傳統原則遷移至新的 Azure AD 條件式存取原則。


## <a name="classic-policies"></a>傳統原則

您未在 Azure 入口網站中建立的 Azure AD 和 Intune 條件式存取原則，也稱為**傳統原則**。 若要遷移傳統原則，您不需要存取 Azure 傳統入口網站。 Azure 入口網站提供 [[傳統原則 (預覽)] 檢視](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)，讓您檢閱您的傳統原則。

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>開啟傳統原則

**開啟傳統原則：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左側導覽列上，按一下 [Azure Active Directory]。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. 在 [Azure Active Directory] 頁面的 [管理] 區段中，按一下 [條件式存取]。

    ![條件式存取](./media/active-directory-conditional-access-migration/02.png)
 
2. 在 [條件式存取 - 原則] 頁面的 [管理] 區段中，按一下 [傳統原則 (預覽)]。

3. 從傳統原則清單中，選取您關心的原則。   

    ![條件式存取](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Azure AD 條件式存取原則

本主題提供您詳細的步驟，讓您不熟悉 Azure AD 條件式存取原則也能遷移傳統原則。 不過，熟悉 Azure AD 條件式存取的基本概念與術語，有利於改善您的遷移體驗。

請參閱：

- [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)以了解基本概念與術語

- [開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)以熟悉 Azure 入口網站的使用者介面


 





## <a name="multi-factor-authentication-policy"></a>Multi-Factor Authentication 原則 

這個範例示範如何遷移需要雲端應用程式 Multi-Factor Authentication** 的傳統規則。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**遷移傳統原則：**

1. [開啟傳統原則](#open-a-classic-policy)以取得組態設定。
2. 建立新的 Azure AD 條件式存取原則，以取代您的傳統原則。 


### <a name="create-a-new-conditional-access-policy"></a>建立新的條件式存取原則


1. 在 [Azure 入口網站](https://portal.azure.com)的左側導覽列上，按一下 [Azure Active Directory]。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. 在 [Azure Active Directory] 頁面的 [管理] 區段中，按一下 [條件式存取]。

    ![條件式存取](./media/active-directory-conditional-access-migration/02.png)



3. 若要在 [條件式存取] 頁面上開啟 [新增] 頁面，請在頂端的工具列中按一下 [新增]。

    ![條件式存取](./media/active-directory-conditional-access-migration/03.png)

4. 在 [新增] 頁面的 [名稱] 文字方塊中，鍵入您的原則名稱。

    ![條件式存取](./media/active-directory-conditional-access-migration/29.png)

5. 在 [指派] 區段中，按一下 [使用者和群組]。

    ![條件式存取](./media/active-directory-conditional-access-migration/05.png)

    a. 如已選取傳統原則中的所有使用者，請按一下 [所有使用者]。 

    ![條件式存取](./media/active-directory-conditional-access-migration/35.png)

    b. 如已選取傳統原則中的群組，請按一下 [選取使用者和群組]，然後選取所需的使用者和群組。

    ![條件式存取](./media/active-directory-conditional-access-migration/36.png)

    c. 如已排除群組，請按一下 [排除] 索引標籤，然後選取所需的使用者和群組。 

    ![條件式存取](./media/active-directory-conditional-access-migration/37.png)

6. 在 [新增] 頁面上，若要開啟 [雲端應用程式] 頁面，請在 [指派] 區段中，按一下 [雲端應用程式]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. 在 [雲端應用程式] 頁面上，執行下列步驟︰

    ![條件式存取](./media/active-directory-conditional-access-migration/08.png)

    a. 按一下 [選取應用程式]。

    b.這是另一個 C# 主控台應用程式。 按一下 [選取] 。

    c. 在 [選取] 頁面上，選取您的雲端應用程式，然後按一下 [選取]。

    d. 在 [雲端應用程式] 頁面上，按一下 [完成]。



9. 如已選取 [需要多重要素驗證]：

    ![條件式存取](./media/active-directory-conditional-access-migration/26.png)

    a. 在 [存取控制] 區段中，按一下 [授與]。

    ![條件式存取](./media/active-directory-conditional-access-migration/27.png)

    b. 在 [授與] 頁面上，按一下 [授與存取權]，然後按一下 [需要多重要素驗證]。

    c. 按一下 [選取] 。


10. 按一下 [開啟] 以啟用您的原則。

    ![條件式存取](./media/active-directory-conditional-access-migration/30.png)

11. 停用傳統原則。 

    ![條件式存取](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。 
