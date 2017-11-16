---
title: "Azure IoT Suite 和 Azure Active Directory | Microsoft Docs"
description: "描述 Azure IoT Suite 如何使用 Azure Active Directory 來管理權限。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 3eb23ccde4522fdf6dee81c6404dfc5fdf0d0acf
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>azureiotsuite.com 網站的權限

## <a name="what-happens-when-you-sign-in"></a>當您登入時會發生什麼事？

您第一次在 [azureiotsuite.com][lnk-azureiotsuite] 登入時，此網站會根據目前選取的 Azure Active Directory (AAD) 租用戶和 Azure 訂用帳戶，決定您擁有的權限等級。

1. 首先，若要填入您的使用者名稱旁邊顯示的租用戶清單，此網站會先從 Azure 找出您所屬的 AAD 租用戶。 目前，網站一次只能取得一個租用戶的使用者權杖。 因此，當您使用右上角下拉式清單切換租用戶時，此網站會讓您重新登入該租用戶，以取得該租用戶的權杖。

2. 接下來，網站會從 Azure 找出與所選租用戶有關聯的訂用帳戶。 當您建立預先設定的新方案時，您會看到可用的訂用帳戶。

3. 最後，網站會擷取訂用帳戶中的所有資源以及標記為預先設定之方案的資源群組，並在首頁上填入動態磚。

下列各節說明的角色可控制預先設定之方案的存取。

## <a name="aad-roles"></a>AAD 角色

AAD 角色控制在預先設定的方案中佈建預先設定的方案以及管理使用者的能力。

您可以在[在 Azure AD 中指派系統管理員角色][lnk-aad-admin]中的 AAD 找到有關使用者和系統管理員角色的詳細資訊。 目前的文章內容著重於預先設定的解決方案所使用的**全域管理員**和**使用者**目錄角色。

### <a name="global-administrator"></a>全域管理員

每個 AAD 租用戶可以有很多全域系統管理員：

* 當您建立 AAD 租用戶時，您會預設為該租用戶的全域管理員。
* 全域管理員可以佈建基本和標準的預先設定解決方案。

### <a name="domain-user"></a>網域使用者

每一個 AAD 租用戶可以有很多網域使用者：

* 網域使用者可以透過 [azureiotsuite.com][lnk-azureiotsuite] 網站佈建標準的預先設定解決方案。
* 網域使用者可以使用 CLI 建立基本的預先設定解決方案。

### <a name="guest-user"></a>來賓使用者

每一個 AAD 租用戶可以有很多來賓使用者。 來賓使用者在 AAD 租用戶中有一組受限的權限。 因此，來賓使用者無法在 AAD 租用戶中佈建預先設定的方案。

如需有關 AAD 中使用者和角色的詳細資訊，請參閱下列資源：

* [在 Azure AD 中建立使用者][lnk-create-edit-users]
* [將使用者指派給應用程式][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure 訂用帳戶管理員角色

Azure 管理員角色可控制將 Azure 訂用帳戶對應至 AD 租用戶的能力。

在[如何新增或變更 Azure 共同管理員、服務管理員及帳戶管理員][lnk-admin-roles]一文中，進一步了解 Azure 系統管理員角色。

## <a name="faq"></a>常見問題集

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>我是服務管理員，我想要變更我的訂用帳戶與特定 AAD 租用戶之間的目錄對應。 如何完成這項工作？

請參閱[將現有的訂用帳戶新增至您的 Azure AD 目錄](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-add-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>我想要在使用組織帳戶登入時變更服務管理員或共同管理員

請參閱支援文章[在使用組織帳戶登入時變更服務管理員和共同管理員][lnk-service-admins]。

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>為什麼出現以下錯誤？ 「您的帳戶沒有適當的權限可建立方案。 請洽詢您的帳戶管理員或嘗試使用不同的帳戶。」

看看下圖中的指導方針︰

![][img-flowchart]

> [!NOTE]
> 在驗證您為 AAD 租用戶的全域管理員和訂用帳戶的共同管理員後，如果您還是持續看到錯誤，請帳戶管理員依以下順序移除使用者並重新指派必要的權限。 首先，將使用者新增為全域管理員，然後將使用者新增為 Azure 訂用帳戶的共同管理員。 如果問題持續發生，請連絡[說明與支援][lnk-help-support]。

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>當我有 Azure 訂用帳戶時為何會出現此錯誤？ 「需要有 Azure 訂用帳戶，才能建立預先設定的方案。 只需要幾分鐘的時間，您就可以建立免費試用帳戶。」

如果您確定有 Azure 訂用帳戶，請驗證您的訂用帳戶的租用戶對應，並確保已在下拉式清單中選取正確的租用戶。 如果您已驗證所需的租用戶是否正確，請遵循先前圖表並驗證您的訂用帳戶與此 AAD 租用戶的對應。

## <a name="next-steps"></a>後續步驟
若要繼續了解 IoT 套件，請參閱如何[自訂預先設定的解決方案][lnk-customize]。

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles-azure-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
