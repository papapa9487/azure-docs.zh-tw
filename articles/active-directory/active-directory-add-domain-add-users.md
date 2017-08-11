---
title: "將使用者指派給 Azure Active Directory 中的自訂網域 | Microsoft Docs"
description: "如何在 Azure Active Directory 中的自訂網域填入使用者帳戶。"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 39cb54a6637088c35c6aef864a804c24803f48ba
ms.contentlocale: zh-tw
ms.lasthandoff: 07/26/2017

---
# <a name="assign-users-to-a-custom-domain"></a>將使用者指派至自訂網域
在您將您的自訂網域新增至 Azure Active Directory 之後，您必須加入此網域的使用者帳戶，以便開始驗證它們。

> [!IMPORTANT]
> Microsoft 建議您使用 Azure 入口網站中的 [Azure AD 系統管理中心](https://aad.portal.azure.com)來管理 Azure AD，而不要使用本文所提及的 Azure 傳統入口網站。 如需如何在 Azure AD 系統管理中心管理網域名稱的相關資訊，請參閱[在 Azure Active Directory 中管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)。

## <a name="users-synced-from-a-on-premises-directory"></a>從內部部署目錄同步處理的使用者
如果您已經設定內部部署 Active Directory 與 Azure Active Directory 之間的連線，同步處理可以填入帳戶。 如需有關如何同步處理 Azure Active Directory 與內部部署 Active Directory 的詳細資訊，請參閱 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

## <a name="users-added-and-managed-in-the-cloud"></a>新增和管理雲端中的使用者
若要變更現有使用者帳戶的網域：

1. 使用全域管理員或使用者管理員帳戶開啟 Azure 傳統入口網站。
2. 開啟您的目錄。
3. 選取 [使用者]  索引標籤。
4. 從清單中選取使用者。
5. 變更使用者的網域，然後選取 [儲存] 。

使用 [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 或 [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) 也可以完成此操作。

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>在建立新的使用者時選取自訂網域
1. 使用全域管理員或使用者管理員帳戶開啟 Azure 傳統入口網站。
2. 開啟您的目錄。
3. 選取 [使用者]  索引標籤。
4. 在命令列上選取 [新增] 。
5. 當您新增使用者名稱時，請從網域清單中選擇自訂網域。
6. 選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟
* [使用自訂網域名稱，以簡化您的使用者的登入體驗](active-directory-add-domain.md)
* [管理自訂網域名稱](active-directory-add-manage-domain-names.md)
* [了解 Azure AD 中的網域管理概念](active-directory-add-domain-concepts.md)


