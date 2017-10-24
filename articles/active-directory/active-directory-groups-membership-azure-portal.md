---
title: "在 Azure Active Directory 中管理您的群組所屬的群組 | Microsoft Docs"
description: "在 Azure Active Directory 中，群組可以包含其他群組。 以下說明如何管理這些成員資格。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e785c2d0-7724-47d4-a56e-c58280c08a14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 44911ec2c278f7af7b8ec4bd971bc97b342a8bf6
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>管理 Azure Active Directory 租用戶中群組所屬的群組
在 Azure Active Directory 中，群組可以包含其他群組。 以下說明如何管理這些成員資格。

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>如何找出我群組所屬的群組？
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組]。

   ![開啟使用者和群組影像](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. 選取 [所有群組]。

   ![選取群組影像](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. 選取群組。
2. 選取 [群組成員資格]。

   ![開啟群組成員資格影像](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. 若要新增您的群組作為另一個群組的成員，請在 [群組 - 群組成員資格] 刀鋒視窗上，選取 [新增] 命令。
2. 從 [選取群組] 刀鋒視窗中選取一個群組，然後選取刀鋒視窗底部的 [選取] 按鈕。 您一次只能將您的群組新增到一個群組中。 [使用者]  方塊會根據將您的輸入內容與使用者或裝置名稱的任何部分進行比對來篩選顯示。 該方塊中不接受任何萬用字元。

   ![新增群組成員資格](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. 若要移除您群組在另一個群組的成員身分，請在 [群組 - 群組成員資格]  刀鋒視窗上，選取一個群組。
9. 選取 [移除] 命令，並在出現提示時確認您的選擇。

   ![移除成員資格命令](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. 完成變更您群組的群組成員資格時，選取 [儲存] 。

## <a name="additional-information"></a>其他資訊
這些文章提供有關 Azure Active Directory 的其他資訊。

* [查看現有的群組](active-directory-groups-view-azure-portal.md)
* [建立新群組並新增成員](active-directory-groups-create-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員](active-directory-groups-members-azure-portal.md)
* [管理群組中使用者的動態規則](active-directory-groups-dynamic-membership-azure-portal.md)
