---
title: "Azure 訂用帳戶如何與 Azure Active Directory 產生關聯 | Microsoft Docs"
description: "登入 Microsoft Azure 及相關問題，例如 Azure 訂用帳戶與 Azure Active Directory 之間的關係。"
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
ms.date: 08/22/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: b2f8cc774b6cc245a4000e5c6924a8bb817707bc
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Azure 訂用帳戶如何與 Azure Active Directory 產生關聯
本文涵蓋有關 Azure 訂用帳戶與 Azure Active Directory (Azure AD) 之間關係的資訊。

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>您的 Azure 訂用帳戶與 Azure AD 的關係
Azure 訂用帳戶具有 Azure AD 目錄的信任關係，這表示它信任此目錄來驗證使用者、服務和裝置。 多個訂用帳戶可以信任相同的目錄，但是每個訂用帳戶只能信任一個目錄。 

訂用帳戶與目錄之間存在的信任關係不同於訂用帳戶與 Azure 中其他資源 (網站、資料庫等) 之間的關係。 如果訂用帳戶已過期，則也會停止存取與訂用帳戶相關聯的其他資源。 但目錄會保留在 Azure 中，而且您可以將不同訂用帳戶與該目錄產生關聯，並繼續管理目錄使用者。

![訂用帳戶如何與圖表產生關聯](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

Azure AD 的運作方式與 Azure 訂用帳戶中其他服務的運作方式不同。 其他 Azure 服務附屬於 Azure 訂用帳戶。 但是，您在 Azure AD 中看到的內容不會隨著訂用帳戶改變。 它可讓您根據登入的使用者存取目錄。

所有使用者都有會對他們進行驗證的單一主目錄，但是他它們也可以是其他目錄中的來賓。 在 Azure AD 中，您只可以看到您的使用者帳戶為其成員的目錄。 目錄也可與內部部署 Active Directory 同步。

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Azure AD 和雲端服務訂用帳戶
Azure AD 提供大部分 Microsoft 雲端服務的核心目錄和身分識別管理功能，包括：

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

當您註冊上述任何 Microsoft 雲端服務時，會免費取得 Azure AD 服務。 如果您需要將額外的 Azure 訂用帳戶新增至 Azure AD 目錄，唯有使用 Microsoft 帳戶進行登入才能這麼做。 例如，如果您使用 Microsoft 帳戶註冊 Azure，而且也使用工作或學校帳戶註冊不同的 Microsoft 雲端服務，您有兩個 Azure AD 執行個體：
1. Azure 訂用帳戶的預設目錄。 如果您使用 Microsoft 帳戶進行登入，您可以將其他 Azure 訂用帳戶新增至此目錄或您建立的其他目錄，因為您可由 Azure 進行驗證。
2. 您的工作或學校帳戶的主目錄。 如果您使用工作或學校帳戶來登入 Azure，則無法將 Azure 訂用帳戶新增至現有的目錄，因為 Azure 無法直接驗證您的工作或學校帳戶。 
 
若要深入了解如何變更 Azure 訂用帳戶的系統管理員，請參閱[將 Azure 訂用帳戶的擁有權轉移至另一個帳戶](../billing/billing-subscription-transfer.md)

## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>管理訂用帳戶和目錄的建議
Azure 訂用帳戶的系統管理角色會管理繫結至 Azure 訂用帳戶的資源。 本節說明 Azure 訂用帳戶管理員與 Azure AD 目錄管理員之間的差異。 系統管理角色和使用這些角色來管理訂用帳戶的其他建議，均涵蓋於[在 Azure Active Directory 中指派系統管理員角色](active-directory-assign-admin-roles.md)。

根據預設，當您註冊時，您會被指派服務管理員角色。 如果其他人必須使用相同的訂用帳戶來登入和存取服務，您可以將他們新增為共同管理員。 

Azure AD 有一組不同的系統管理角色，可用來管理目錄和識別相關功能。 例如，目錄的全域管理員可以將使用者和群組加入至目錄，或要求使用者的多因素驗證。 建立目錄的使用者會被指派全域管理員角色，而且他們可以將系統管理角色指派給其他使用者。 其他服務 (例如 Office 365 和 Microsoft Intune) 也會使用 Azure AD 系統管理角色。 

Azure 訂用帳戶管理員和 Azure AD 目錄管理員是兩個不同的角色。 
* Azure 訂用帳戶管理員可以管理 Azure 中的資源，而且可以在 Azure 入口網站中使用 Azure AD (因為 Azure 入口網站本身是 Azure 資源)。 
* 目錄管理員止可以管理 Azure AD 目錄中的屬性。

使用者可以同時擔任這兩個角色，但並非必要。 使用者可以被指派目錄全域管理員角色，但不可被指派為 Azure 訂用帳戶的服務管理員或共同管理員。 使用者不需是訂用帳戶的管理員，即可登入 Azure 入口網站，但無法在入口網站中管理該訂用帳戶的目錄。 此使用者可以使用其他工具 (例如 Azure AD PowerShell 或 Office 365 系統管理中心) 來管理目錄。

## <a name="next-steps"></a>後續步驟
* 若要深入了解如何變更 Azure 訂用帳戶的系統管理員，請參閱[將 Azure 訂用帳戶的擁有權轉移至另一個帳戶](../billing/billing-subscription-transfer.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](active-directory-understanding-resource-access.md)
* 如需有關如何在 Azure AD 中指派角色的詳細資訊，請參閱 [在 Azure Active Directory 中指派系統管理員角色](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

