---
title: "將使用者新增至 Azure Active Directory | Microsoft Docs"
description: "說明如何在 Azure Active Directory 中新增使用者。"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 13a7d2d3b991206c45e66872b590bc27a224eead
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>快速入門：將使用者新增至 Azure Active Directory
本文說明如何在 Azure Active Directory (Azure AD) 中新增您組織中的使用者，您可以使用 Azure 入口網站一次新增一位，或藉由同步處理您內部部署 Windows Server AD 使用者帳戶資料來新增。 

## <a name="add-cloud-based-users"></a>新增雲端式使用者
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 選取 [Azure Active Directory]**Azure Active Directory**，然後選取 [使用者和群組]。
3. 在 [使用者和群組] 刀鋒視窗上，選取 [所有使用者]，然後選取 [新增使用者]。
   ![選取 [新增] 命令](./media/add-users-azure-active-directory/add-user.png)
4. 輸入使用者的詳細資料，例如「名稱」和「使用者名稱」。 使用者名稱的網域名稱部分必須是初始預設網域名稱 "[網域名稱].onmicrosoft.com"，或是已驗證的非同盟[自訂網域名稱](add-custom-domain.md)，例如"contoso.com"。
5. 複製或記下產生的使用者密碼，以便在此程序完成後，將它提供給使用者。
6. (選擇性) 您可以開啟使用者的 [設定檔] 刀鋒視窗、[群組] 刀鋒視窗或 [目錄角色] 刀鋒視窗，然後在其中填入資訊。 如需有關使用者和系統管理員角色的詳細資訊，請參閱 [在 Azure AD 中指派系統管理員角色](active-directory-assign-admin-roles.md)。
7. 在 [使用者] 刀鋒視窗上，選取 [建立]。
8. 將產生的密碼安全地散發給新使用者，以便讓使用者可以登入。

> [!TIP]
> 您也可以同步處理內部部署 Windows Server AD 中的使用者帳戶資料。 Microsoft 的身分識別解決方案可跨越內部部署和雲端架構功能，建立單一使用者身分識別以用於所有資源的驗證和授權，不論位於何處。 我們稱之為混合式身分識別。 您可以針對混合式身分識別案例，使用 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 來整合您的內部部署目錄與 Azure Active Directory。 這可讓您為與 Azure AD 整合之 Office 365、Azure 和 SaaS 應用程式的使用者提供通用身分識別。 

## <a name="delete-users-from-azure-ad"></a>從 Azure AD 刪除使用者
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組]。
3. 在 [使用者和群組] 刀鋒視窗上，從清單中選取要刪除的使用者。 
4. 在所選使用者的刀鋒視窗上選取 [概觀]，然後在命令列中選取 [刪除]。
   ![選取 [新增] 命令](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>詳細資訊 
* [新增外部使用者](active-directory-users-create-external-azure-portal.md)

* [在 Azure AD 中將使用者指派給角色](active-directory-users-assign-role-azure-portal.md)

## <a name="next-steps"></a>後續步驟
在此快速入門中，您學到如何將使用者新增至 Azure AD Premium。 

您可以使用下列連結，從 Azure 入口網站在 Azure AD 中建立新的使用者。

> [!div class="nextstepaction"]
> [在 Azure AD 中新增使用者](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 
