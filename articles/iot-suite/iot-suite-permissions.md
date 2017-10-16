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
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: f67c7bfa3f0ea7b720c8684cc0c501be3e464373
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
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
* 全域管理員可以佈建預先設定的方案，並獲得其 AAD 租用戶的應用程式內部的 [管理員] 角色。
* 如果相同的 AAD 租用戶中有其他使用者建立應用程式，則授與全域系統管理員的預設角色會是「唯讀」。
* 全域系統管理員可以使用 [Azure 入口網站][lnk-portal]來將使用者指派給應用程式的角色。

### <a name="domain-user"></a>網域使用者

每一個 AAD 租用戶可以有很多網域使用者：

* 網域使用者可以透過 [azureiotsuite.com][lnk-azureiotsuite] 網站佈建預先設定的解決方案。 根據預設，網域使用者在佈建應用程式中會被授與 [系統管理員] 角色。
* 網域使用者可以使用 [azure-iot-remote-monitoring][lnk-rm-github-repo]、[azure-iot-predictive-maintenance][lnk-pm-github-repo] 或 [azure-iot-connected-factory][lnk-cf-github-repo] 存放庫中的 build.cmd 指令碼建立應用程式。 不過，授與網域使用者的預設角色是「唯讀」，因為網域使用者沒有指派角色的權限。
* 如果 AAD 租用戶中有其他使用者建立應用程式，網域使用者預設會獲指派該應用程式的「唯讀」角色。
* 網域使用者無法指派給角色給應用程式。因此，即使應用程式使用者佈建應用程式，網域使用者也無法針對應用程式的使用者新增使用者或角色。

### <a name="guest-user"></a>來賓使用者

每一個 AAD 租用戶可以有很多來賓使用者。 來賓使用者在 AAD 租用戶中有一組受限的權限。 因此，來賓使用者無法在 AAD 租用戶中佈建預先設定的方案。

如需有關 AAD 中使用者和角色的詳細資訊，請參閱下列資源：

* [在 Azure AD 中建立使用者][lnk-create-edit-users]
* [將使用者指派給應用程式][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure 訂用帳戶管理員角色

Azure 管理員角色可控制將 Azure 訂用帳戶對應至 AD 租用戶的能力。

在[如何新增或變更 Azure 共同管理員、服務管理員及帳戶管理員][lnk-admin-roles]一文中，進一步了解 Azure 系統管理員角色。

## <a name="application-roles"></a>應用程式角色

應用程式角色可控制您預先設定的方案中的裝置存取權。

在已佈建的應用程式中有兩個定義的角色和一個隱含角色：

* **管理員︰**擁有完整控制權可新增、管理、移除裝置和修改設定。
* **唯讀：**可以檢視裝置、規則、動作、作業和遙測。

您可以在 [RolePermissions.cs][lnk-resource-cs] 來源檔案中尋找指派給每個角色的權限。

### <a name="changing-application-roles-for-a-user"></a>變更使用者的應用程式角色

您可以使用下列程序，讓您的 Active Directory 中的使用者成為預先設定解決方案的系統管理員。

您必須是 AAD 全域管理員才能變更使用者的角色：

1. 移至 [Azure 入口網站][lnk-portal]。
2. 選取 **Azure Active Directory**。
3. 確定您使用的目錄是您在佈建解決方案時在 azureiotsuite.com 上所選擇的目錄。 如果您有多個與訂用帳戶相關聯的目錄，只要按一下入口網站右上方的帳戶名稱，就可以切換目錄。
4. 依序按一下 [企業應用程式] 和 [所有應用程式]。
4. 顯示具有**任何**狀態的**所有應用程式**。 然後搜尋具有預先設定解決方案名稱的應用程式。
5. 按一下符合預先設定之方案名稱的應用程式名稱。
6. 按一下 [使用者和群組]。
7. 選取您想要轉換角色的使用者。
8. 按一下 [指派]，選取您想要指派給使用者的角色 (例如 [管理員])，然後按一下核取記號。

## <a name="faq"></a>常見問題集

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>我是服務管理員，我想要變更我的訂用帳戶與特定 AAD 租用戶之間的目錄對應。 如何完成這項工作？

1. 前往 [Azure 傳統入口網站][lnk-classic-portal]，然後按一下左側服務清單中的 [設定]。
2. 選取您要變更目錄對應的訂用帳戶。
3. 按一下 [編輯目錄] 。
4. 選取下拉式清單中您要使用的 [目錄]  。 按一下向前箭號。
5. 確認目錄對應和受影響的共同管理員。 如果您要從另一個目錄移動，則會移除原始目錄中的所有共同管理員。

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>我是 AAD 租用戶的網域使用者/成員，我已建立預先設定的方案。 如何獲得我的應用程式的角色？

要求全域系統管理員讓您成為 AAD 租用戶的全域系統管理員，然後自行為使用者指派角色。 或者，要求全域系統管理員直接為您指派角色。 如果您想要變更預先設定的方案已部署至的 AAD 租用戶，請參閱下一個問題。

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>如何切換已指派給我的遠端監視預先設定之方案和應用程式的 AAD 租用戶？

您可以從 <https://github.com/Azure/azure-iot-remote-monitoring> 執行雲端部署，並利用新建立的 AAD 租用戶重新部署。 因為建立 AAD 租用戶時，您預設為全域系統管理員，所以有新增使用者及指派角色給這些使用者的權限。

1. 在 [Azure 入口網站][lnk-portal]中建立 AAD 目錄。
2. 前往 <https://github.com/Azure/azure-iot-remote-monitoring>。
3. 執行 `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (例如，`build.cmd cloud debug myRMSolution`)
4. 出現提示時，將 **tenantid** 設定為您新建立的租用戶，而不是您先前的租用戶。

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
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
