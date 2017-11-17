---
title: "新增或變更 Azure 系統管理員訂用帳戶角色| Microsoft Docs"
description: "說明如何新增或變更 Azure 共同管理員、服務管理員和帳戶管理員"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2017
ms.author: genli
ms.openlocfilehash: 091fe208783a2fe5d5c91abe4ec498bf760a3eb3
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>新增或變更管理訂用帳戶或服務的 Azure 系統管理員角色

您可以變更管理您的 Azure 訂用帳戶或訂用帳戶中所使用 Azure 服務的 Azure 系統管理員。 若要檢視 Azure 帳單資訊及管理訂用帳戶，您必須以帳戶管理員的身分登入帳戶中心。 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>在 Azure 入口網站中新增訂用帳戶的 RBAC 擁有者管理員 

若要在 Azure 入口網站中將某人新增為訂用帳戶的管理員，我們建議您授與他 [RBAC](../active-directory/role-based-access-control-configure.md) 擁有者角色。 擁有者角色可以管理您指派之訂用帳戶中的資源，但沒有其他訂用帳戶的存取權限。 您透過 [Azure 入口網站](https://portal.azure.com)新增的擁有者不能管理 [Azure 傳統入口網站](https://manage.windowsazure.com)中的資源。

1. 登入 [Azure 入口網站中的訂用帳戶檢視](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取您想讓管理員存取的訂用帳戶。
1. 在功能表中選取 [存取控制 (IAM)]。
1. 選取 [新增] > [角色] > [擁有者]。 鍵入您想要新增為擁有者之使用者的電子郵件地址，選取該使用者，然後選取 [儲存]。

    ![顯示已選取 [擁有者] 角色的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/add-role.png)

## <a name="add-or-change-co-administrator"></a>新增或變更共同管理員

您只能將擁有者新增為共同管理員， 不能將角色為參與者和讀者等的其他使用者新增為共同管理員。

1. 如果您尚未新增，請遵循上述指示將某人新增為擁有者。
2. **以滑鼠右鍵按一下**您剛才新增的「擁有者」使用者，然後選取 [新增為共同管理員]。 如果您沒有看到 [新增為共同管理員] 選項，請重新整理頁面或嘗試使用其他網際網路瀏覽器。 

     ![新增共同管理員的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >如果使用者需要在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中管理 Azure 服務，請將「擁有者」帳戶新增為共同管理員。

    若要移除共同管理員權限，請**以滑鼠右鍵按一下**「共同管理員」使用者，然後選取 [移除共同管理員]。

    ![移除共同管理員的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>變更 Azure 訂用帳戶的服務管理員

只有帳戶管理員可以變更訂用帳戶的服務管理員。 根據預設，註冊時系統會將服務管理員與帳戶管理員設為同一人。

1. 請參閱[變更服務管理員的限制](#limits)，確定您的情況是否受支援。
1. 以帳戶管理員身分登入[帳戶中心](https://account.windowsazure.com/subscriptions)。
1. 選取一個訂用帳戶。
1. 選取右側的 [編輯訂用帳戶詳細資料]。

    ![顯示帳戶中心裡 [編輯訂用帳戶] 按鈕的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. 在 [服務管理員  ] 方塊中，輸入新的服務管理員的電子郵件地址。

    ![顯示應變更服務管理員電子郵件之方塊的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>變更服務管理員的限制

* 每個訂用帳戶都與 Azure AD 目錄相關聯。 若要尋找與訂用帳戶相關聯的目錄，請前往 [Azure 傳統入口網站](https://manage.windowsazure.com/)，然後選取 [設定] > [訂用帳戶]。 請查看訂用帳戶識別碼來尋找目錄。
* 如果您以公司或學校帳戶登入，可以將組織中的其他帳戶新增為服務管理員。 例如，abby@contoso.com 可以將 bob@contoso.com 新增為服務管理員，但若想新增 john@notcontoso.com 為服務管理員，則 contoso.com 目錄中必須要有 john@notcontoso.com 才能這麼做。 以公司或學校帳戶登入的使用者可以繼續將 Microsoft 帳戶使用者新增為服務管理員。

  | 登入方法 | 要將 Microsoft 帳戶使用者新增為安全性關聯 (SA) 嗎？ | 要將同一組織中的公司或學校帳戶新增為安全性關聯 (SA) 嗎？ | 要將其他組織中的公司或學校帳戶新增為安全性關聯 (SA) 嗎？ |
  | --- | --- | --- | --- |
  |  Microsoft 帳戶 |是 |否 |否 |
  |  公司或學校帳戶 |是 |是 |否 |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>變更 Azure 訂用帳戶的帳戶管理員

若要變更訂用帳戶的帳戶管理員，請參閱[將 Azure 訂用帳戶的擁有權轉移給另一個帳戶](billing-subscription-transfer.md)。

<a name="check-the-account-administrator-of-the-subscription"></a>

**不確定帳戶管理員是誰嗎？** 請遵循下列步驟：

1. 登入 [Azure 入口網站中的訂用帳戶檢視](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取您想要檢查的訂用帳戶，然後查看 [設定]。
1. 選取 [屬性] 。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。  

## <a name="types-of-azure-admin-accounts"></a>Azure 管理帳戶的類型

 帳戶管理員、服務管理員和共同管理員是 Microsoft Azure 中的三種系統管理員角色。 下表說明這三個系統管理角色之間的差異。

| 管理角色 | 限制 | 說明 |
| --- | --- | --- |
| 帳戶管理員 (AA) |每個 Azure 帳戶 1 名 |這就是註冊或購買 Azure 訂用帳戶，且經過授權可存取 [帳戶中心](https://account.azure.com/Subscriptions) 及執行各種管理工作的那個人。 包括能夠建立訂用帳戶、取消訂用帳戶、變更訂用帳戶的計費方式以及變更服務管理員。 |
| 服務管理員 (SA) |每個 Azure 訂用帳戶 1 名 |此角色經過授權，可管理 [Azure 入口網站](https://portal.azure.com)上的服務。 根據預設，新訂用帳戶的帳戶管理員也是服務管理員。 |
| [Azure 傳統入口網站](https://manage.windowsazure.com) |每個訂用帳戶 200 名 |此角色的存取權限與服務管理員相同，但無法變更訂用帳戶與 Azure 目錄的關聯。 |

Azure Active Directory 角色型存取控制 (RBAC) 能讓使用者擁有多個角色。 如需詳細資訊，請參閱 [Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md)。


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>深入了解資源存取控制和 Active Directory

* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱[了解 Azure 中的資源存取](../active-directory/active-directory-understanding-resource-access.md)。
* 如需有關 Azure Active Directory 的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](../active-directory/active-directory-how-subscriptions-associated-directory.md)和[指派 Azure Active Directory 中的管理員角色](../active-directory/active-directory-assign-admin-roles-azure-portal.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
