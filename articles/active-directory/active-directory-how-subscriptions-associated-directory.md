---
title: "如何將現有的 Azure 訂用帳戶新增至您的 Azure AD 目錄 | Microsoft Docs"
description: "如何將現有的訂用帳戶新增至您的 Azure AD 目錄"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: abf207a3ceec708a828170936f7dc7948ccf34a9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>如何將 Azure 訂用帳戶新增至 Azure Active Directory
本文涵蓋有關 Azure 訂用帳戶與 Azure Active Directory (Azure AD) 之間關係，以及如何將現有訂用帳戶新增至 Azure AD 目錄的資訊。 Azure 訂用帳戶具有 Azure AD 目錄的信任關係，這表示它信任此目錄來驗證使用者、服務和裝置。 多個訂用帳戶可以信任相同的目錄，但是每個訂用帳戶只能信任一個目錄。 

訂用帳戶與目錄之間存在的信任關係不同於訂用帳戶與 Azure 中其他資源 (網站、資料庫等) 之間的關係。 如果訂用帳戶已過期，也會停止存取與訂用帳戶相關聯的其他資源。 但 Azure AD 目錄會保留在 Azure 中，而且您可以將不同的訂用帳戶與該目錄產生關聯，以及使用新的訂用帳戶管理此目錄。

所有使用者都有會對他們進行驗證的單一主目錄，但是他它們也可以是其他目錄中的來賓。 在 Azure AD 中，您可以看到您的使用者帳戶為其成員或來賓的目錄。

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>將現有的訂用帳戶新增至您的 Azure AD 目錄
您用於登入的帳戶必須同時存在於與訂用帳戶相關聯的目前目錄中和您想要將訂用帳戶新增至的目錄中。 

1. 以您想要移轉擁有權之訂用帳戶的帳戶管理員之帳戶登入 [Azure 帳戶中心](https://account.azure.com/Subscriptions)。
2. 確定您希望成為訂用帳戶擁有者的使用者位於目標目錄中。
3. 按一下 [移轉訂用帳戶]。
4. 指定接受者。 接受者會自動收到含有接受連結的電子郵件。
5. 接受者按一下連結並遵循指示進行，包括輸入他們的付款資訊。 當接受者成功時，訂用帳戶就轉移完成。 
6. 訂用帳戶的預設目錄會變更為使用者所在的目錄。

如需詳細資訊，請參閱[將 Azure 訂用帳戶擁有權轉移給另一個帳戶](../billing/billing-subscription-transfer.md)。

## <a name="next-steps"></a>後續步驟
* 若要深入了解如何變更 Azure 訂用帳戶的系統管理員，請參閱[將 Azure 訂用帳戶的擁有權轉移至另一個帳戶](../billing/billing-subscription-transfer.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](active-directory-understanding-resource-access.md)
* 如需有關如何在 Azure AD 中指派角色的詳細資訊，請參閱 [在 Azure Active Directory 中指派系統管理員角色](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
