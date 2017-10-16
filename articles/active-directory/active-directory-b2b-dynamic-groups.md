---
title: "動態群組與 Azure Active Directory B2B 共同作業 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業可搭配 Azure AD 動態群組使用"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.reviewer: sasubram
ms.openlocfilehash: 5818c41610c8c5df89abcb0dcd058bcbe9579ce7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>動態群組與 Azure Active Directory B2B 共同作業

## <a name="what-are-dynamic-groups"></a>什麼是動態群組？
[Azure 入口網站](https://portal.azure.com)允許您動態設定 Azure Active Directory (Azure AD) 的安全性群組成員資格。 系統管理員可以設定規則以根據使用者屬性 (例如 userType、部門或國家/地區) 填入在 Azure Active Directory 中建立的群組。 在安全性群組中，可以根據成員的屬性而自動新增或移除成員。 這些群組可以提供對應用程式或雲端資源 (SharePoint 網站和文件) 的存取，以及將授權指派給成員。 若要深入了解動態群組，請參閱 [Azure Active Directory 中的專用群組](active-directory-accessmanagement-dedicated-groups.md)。

需要有適當的 [Azure AD Premium P1 或 P2 授權](https://azure.microsoft.com/pricing/details/active-directory/)，才能建立和使用動態群組。 請參閱[在 Azure Active Directory 中針對動態群組成員資格建立以屬性為基礎的規則](active-directory-groups-dynamic-membership-azure-portal.md)一文，以深入了解。

## <a name="what-are-the-built-in-dynamic-groups"></a>什麼是內建動態群組？
「所有使用者」動態群組讓租用戶系統管理員按一下滑鼠即可建立包含租用戶中所有使用者的群組。 根據預設值，「所有使用者」群組包括目錄中的所有使用者 (包括「成員」與「來賓」)。
在新的 Azure Active Directory 系統管理員入口網站中，您可以選擇在 [群組設定] 檢視中啟用「所有使用者」群組。

![內建群組](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

## <a name="hardening-the-all-users-dynamic-group"></a>強化「所有使用者」動態群組
根據預設值，**所有使用者**群組也包含您的 B2B 共同作業 (來賓) 使用者。 您可以使用規則移除來賓使用者，以進一步保護**所有使用者**群組。 下圖顯示已修改為排除來賓的**所有使用者**群組。

![啟用所有使用者群組](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

您可能也會發現，建立一個只包含來賓使用者的新動態群組很有用，這樣可讓您對他們套用原則 (例如 Azure AD 條件式存取原則)。
這種群組可能如下所示：

![排除來賓使用者](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)
* [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
* [委派 B2B 共同作業邀請](active-directory-b2b-delegate-invitations.md)
* [B2B 共同作業程式碼與 PowerShell 範例](active-directory-b2b-code-samples.md)
* [設定適用於 B2B 共同作業的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
* [B2B 共同作業使用者權杖](active-directory-b2b-user-token.md)
* [B2B 共同作業使用者宣告對應](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共用](active-directory-b2b-o365-external-user.md)
* [B2B 共同作業目前的限制](active-directory-b2b-current-limitations.md)
