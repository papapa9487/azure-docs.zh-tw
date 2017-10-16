---
title: "在 Azure Active Directory 中將使用者或群組指派給企業應用程式 | Microsoft Docs"
description: "如何在 Azure Active Directory 中選取企業應用程式以將使用者或群組指派給它"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.openlocfilehash: 8e61044f261033a473241e2de152026bf49c4c70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中將使用者或群組指派給企業應用程式
在 Azure Active Directory (Azure AD) 中，您可以輕鬆將使用者或群組指派給您的企業應用程式。 您必須具備適當的權限，才能管理企業應用程式，而且必須是目錄的全域管理員。

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>如何將企業應用程式的存取權指派給使用者？
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [更多服務]，在文字方塊中輸入 Azure Active Directory，然後選取 **Enter**。
3. 在 [Azure Active Directory - directoryname] 刀鋒視窗 (也就是您所管理目錄的 Azure AD 刀鋒視窗) 上，選取 [企業應用程式]。

    ![開啟企業應用程式](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. 在 [企業應用程式] 刀鋒視窗上，選取 [所有應用程式]。 您將會看到一份您可以管理的應用程式清單。
5. 在 [企業應用程式 - 所有應用程式]  刀鋒視窗上，選取一個應用程式。
6. 在 [***appname***] 刀鋒視窗 (亦即標題中含有所選應用程式名稱的刀鋒視窗) 上，選取 [使用者和群組]。

    ![選取所有應用程式命令](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. 在 [***appname*** - 使用者和群組指派] 刀鋒視窗上，選取 [新增] 命令。
8. 在 [新增指派] 刀鋒視窗上，選取 [使用者和群組]。

    ![將使用者或群組指派給應用程式](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. 在 [使用者和群組] 刀鋒視窗上，從清單中選取一或多個使用者或群組，然後選取刀鋒視窗底部的 [選取] 按鈕。
10. 在 [新增指派] 刀鋒視窗上，選取 [角色]。 然後，在 [選取角色] 刀鋒視窗上，選取要套用到所選使用者或群組的角色，然後選取刀鋒視窗底部的 [確定] 按鈕。
11. 在 [新增指派] 刀鋒視窗上，選取刀鋒視窗底部的 [指派] 按鈕。 受指派的使用者或群組將會具備此企業應用程式的所選角色所定義的權限。

## <a name="next-steps"></a>後續步驟
* [查看我的所有群組](active-directory-groups-view-azure-portal.md)
* [從企業應用程式中移除使用者或群組指派](active-directory-coreapps-remove-assignment-azure-portal.md)
* [停用企業應用程式的使用者登入](active-directory-coreapps-disable-app-azure-portal.md)
* [變更企業應用程式的名稱或標誌](active-directory-coreapps-change-app-logo-user-azure-portal.md)
