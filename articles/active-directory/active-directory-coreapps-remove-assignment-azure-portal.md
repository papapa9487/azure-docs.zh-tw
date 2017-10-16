---
title: "在 Azure Active Directory 中從企業應用程式移除使用者或群組指派 | Microsoft Docs"
description: "如何在 Azure Active Directory 中從企業應用程式移除使用者或群組的存取權指派"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 99cbc54b4daa988dbf741275ce92d1c863af6720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中從企業應用程式移除使用者或群組指派
在 Azure Active Directory (Azure AD) 中，針對已獲指派其中一個企業應用程式存取權的使用者或群組，您可以輕鬆移除已指派的存取權。 您必須具備適當的權限，才能管理企業應用程式，而且必須是目錄的全域管理員。

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>如何移除使用者或群組指派？
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [更多服務]，在文字方塊中輸入 **Azure Active Directory**，然後選取 **Enter**。
3. 在 [Azure Active Directory - directoryname] 刀鋒視窗 (也就是您所管理目錄的 Azure AD 刀鋒視窗) 上，選取 [企業應用程式]。

    ![開啟企業應用程式](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. 在 [企業應用程式] 刀鋒視窗上，選取 [所有應用程式]。 您將會看到一份您可以管理的應用程式清單。
5. 在 [企業應用程式 - 所有應用程式]  刀鋒視窗上，選取一個應用程式。
6. 在 [***appname***] 刀鋒視窗 (亦即標題中含有所選應用程式名稱的刀鋒視窗) 上，選取 [使用者和群組]。

    ![選取使用者或群組](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. 在 [***appname*** - 使用者和群組指派] 刀鋒視窗上，選取一或多個使用者或群組，然後選取 [移除] 命令。 出現提示時，請確認您的決定。

    ![選取 [移除] 命令](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>後續步驟
* [查看我的所有群組](active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)
* [停用企業應用程式的使用者登入](active-directory-coreapps-disable-app-azure-portal.md)
* [變更企業應用程式的名稱或標誌](active-directory-coreapps-change-app-logo-user-azure-portal.md)
