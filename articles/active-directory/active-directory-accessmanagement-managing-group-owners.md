---
title: "在 Azure Active Directory 中管理群組擁有者 | Microsoft Docs"
description: "管理群組擁有者，以及如何使用這些群組管理資源的存取權。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: curtand
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 70be82fdd673c4f245e306b1e1cfdcd94d4dac53
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="managing-owners-for-a-group"></a>管理群組的擁有者
資源擁有者將資源的存取權指派給 Azure AD 群組後，群組擁有者就能管理群組的成員資格。 資源擁有者實際上是委派權限給群組的擁有者，以指派使用者存取資源。

## <a name="add-an-owner-to-a-group"></a>將擁有者新增至群組

1. 在 [Azure AD 系統管理中心](https://aad.portal.azure.com)內，選取 [使用者和群組]。
2. 選取 [所有群組] ，然後開啟您想要新增擁有者的目標群組。
3. 依序選取 [擁有者] 和 [新增擁有者]。
4. 在 [新增擁有者] 頁面上，選取您想要新增為此群組擁有者的使用者，然後按一下或點選 [選取]。 

## <a name="remove-an-owner-from-a-group"></a>移除群組中的擁有者

1. 在 [Azure AD 系統管理中心](https://aad.portal.azure.com)內，選取 [使用者和群組]。
2. 選取 [所有群組]，然後開啟要移除擁有者的群組。
3. 選取 [擁有者]，選取您要從這個群組中移除的擁有者，然後按一下或點選 [選取]。
4. 在選定擁有者的開啟窗格中，選取 [移除]。

## <a name="additional-information"></a>其他資訊
這些文章提供有關 Azure Active Directory 群組的其他資訊。

* [查看現有的群組](active-directory-groups-view-azure-portal.md)
* [建立新群組並新增成員](active-directory-groups-create-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員資格](active-directory-groups-membership-azure-portal.md)
* [管理群組中使用者的動態規則](active-directory-groups-dynamic-membership-azure-portal.md)

