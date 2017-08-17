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
ms.topic: article
ms.date: 07/20/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: da5995535d42ed52772cb09e0f4da51bbf878748
ms.contentlocale: zh-tw
ms.lasthandoff: 07/20/2017

---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>新增或變更管理訂用帳戶或服務的 Azure 系統管理員角色
您可以變更管理您的 Azure 訂用帳戶或訂用帳戶中所使用 Azure 服務的 Azure 系統管理員。 若要檢視 Azure 帳單資訊及管理訂用帳戶，您必須以帳戶管理員的身分登入[帳戶中心](https://account.windowsazure.com/Home/Index)。 

## <a name="add-an-admin-for-a-subscription"></a>新增訂用帳戶的管理員
您可以在 Azure 入口網站或 Azure 傳統入口網站新增 Azure 系統管理員。

**Azure 入口網站**

若要在 Azure 入口網站中將某人新增為訂用帳戶的系統管理員，請賦予他擁有者角色。 擁有者角色只能管理您指派之訂用帳戶中的資源。 此系統管理員沒有其他訂用帳戶的存取權限。 您透過 [Azure 入口網站](https://portal.azure.com)新增的擁有者無法管理 [Azure 傳統入口網站](https://manage.windowsazure.com)中的資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [訂用帳戶] > *您想要讓管理員存取的訂用帳戶*。

    ![顯示已選取訂用帳戶選項的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. 在 [訂用帳戶] 刀鋒視窗中，選取 [存取控制 (IAM)]。
4. 選取 [新增] > [角色] > [擁有者]。 輸入您想要新增為擁有者的使用者的電子郵件地址，選取使用者，再選取 [儲存]。

    ![顯示已選取 [擁有者] 角色的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/add-role.png)

5. 如果想要新增擁有者帳戶作為共同管理員，請在 [存取控制 (IAM)] 頁面上，以滑鼠右鍵按一下使用者，然後選取 [新增為共同管理員]。 [Azure 預覽入口網站](https://preview.portal.azure.com/)現提供此功能。 

     ![新增共同管理員的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >如果使用者需要在 [Azure 傳統入口網站](https://manage.windowsazure.com/)上管理 Azure 服務，您需要將「擁有者」使用者新增為共同管理員。

    若要移除共同管理員權限，請以滑鼠右鍵按一下「共同管理員」使用者，然後選取 [移除共同管理員]。

    ![移除共同管理員的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)


**Azure 傳統入口網站**

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 在導覽窗格中，選取 [設定]> [管理員]> [新增]。 </br>

    ![顯示如何存取 [新增] 按鈕的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. 輸入您想新增為共同管理員之人員的電子郵件地址，然後選取您想讓共同管理員存取的訂用帳戶。</br>

    ![顯示已選取訂用帳戶選項的螢幕擷取畫面 ](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

下列電子郵件地址可以新增為共同管理員：

* **Microsoft 帳戶** (先前的 Windows Live ID) </br>
  您可以使用 Microsoft 帳戶登入所有消費者導向的 Microsoft 產品和雲端服務，例如 Outlook (Hotmail)、Skype (MSN)、OneDrive、Windows Phone 和 Xbox LIVE。
* **Organizational account**</br>
  組織帳戶是建立在 Azure Active Directory 之下的帳戶。 組織帳戶地址的格式如下︰

    使用者@&lt;您的網域&gt;.onmicrosoft.com

## <a name="change-service-administrator-for-a-subscription"></a>變更訂用帳戶的服務管理員
只有帳戶管理員可以變更訂用帳戶的服務管理員。

1. 使用帳戶管理員登入 [Azure 帳戶中心](https://account.windowsazure.com/subscriptions)。
2. 選取您想變更的訂用帳戶。
3. 選取右側的 [編輯訂用帳戶詳細資料]。 </br>

    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. 在 [服務管理員  ] 方塊中，輸入新的服務管理員的電子郵件地址。 </br>

    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="change-the-account-administrator"></a>如何變更帳戶管理員
若要將 Azure 帳戶的擁有權轉移到另一個帳戶，請參閱 [轉移 Azure 訂用帳戶的擁有權](billing-subscription-transfer.md)。

強烈建議您不要將帳戶管理員的電子郵件地址重新命名或刪除。 否則 Azure 帳戶可能會有未預期的行為或出現問題。 您可能會無法使用該帳戶登入 Azure、變更帳戶，或使用該帳戶來管理資源。 

## <a name="check-the-account-administrator-of-the-subscription"></a>檢查訂用帳戶的帳戶管理員
如果您不確定誰是訂用帳戶的帳戶管理員，請使用下列步驟來找出帳戶管理員。

  1. 登入 [Azure 入口網站](https://portal.azure.com)。
  2. 在 [中樞] 功能表中，選取 [訂用帳戶] 。
  3. 選取您想要檢查的訂用帳戶，然後查看 [設定]。
  4. 選取 [屬性] 。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。  

## <a name="types-of-azure-admin-accounts"></a>Azure 管理帳戶的類型
 帳戶管理員、服務管理員和共同管理員是 Microsoft Azure 中的三種系統管理員角色。 下表說明這三個系統管理角色之間的差異。

| 管理角色 | 限制 | 說明 |
| --- | --- | --- |
| 帳戶管理員 (AA) |每個 Azure 帳戶 1 名 |這就是註冊或購買 Azure 訂用帳戶，且經過授權可存取 [帳戶中心](https://account.windowsazure.com/Home/Index) 及執行各種管理工作的那個人。 包括能夠建立訂用帳戶、取消訂用帳戶、變更訂用帳戶的計費方式以及變更服務管理員。 |
| 服務管理員 (SA) |每個 Azure 訂用帳戶 1 名 |此角色經過授權，可管理 [Azure 入口網站](https://portal.azure.com)上的服務。 根據預設，新訂用帳戶的帳戶管理員也是服務管理員。 |
| [Azure 傳統入口網站](https://manage.windowsazure.com) |每個訂用帳戶 200 名 |此角色的存取權限與服務管理員相同，但無法變更訂用帳戶與 Azure 目錄的關聯。 |

Azure Active Directory 角色型存取控制 (RBAC) 能讓使用者擁有多個角色。 如需詳細資訊，請參閱 [Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

## <a name="limitations-and-restrictions-for-admin-accounts"></a>系統管理員帳戶的限制和約束
* 每個訂用帳戶都與一個 Azure AD 目錄 (也就是預設目錄) 相關聯。 若要尋找與訂用帳戶相關聯的預設目錄，請前往 [Azure 傳統入口網站](https://manage.windowsazure.com/)，然後選取 [設定] > [訂用帳戶]。 請查看訂用帳戶識別碼來尋找預設目錄。
* 如果您以 Microsoft 帳戶登入，就只能將其他 Microsoft 帳戶或預設目錄中的使用者新增為共同管理員。
* 如果您以組織帳戶登入，就可以將組織中的其他組織帳戶新增為共同管理員。 舉例來說，abby@contoso.com 可以將 bob@contoso.com 新增為服務管理員或共同管理員，但無法新增 john@notcontoso.com，除非 john@notcontoso.com 在預設目錄中。 以組織帳戶登入的使用者，可以繼續將 Microsoft 帳戶使用者新增為服務管理員或共同管理員。
* 現在可以使用組織帳戶登入至 Azure，以下是服務管理員和共同管理員帳戶需求的變更：

  | 登入方法 | 將 Microsoft 帳戶或預設目錄中的使用者新增為 CA 或 SA？ | 將相同組織中的組織帳戶新增為 CA 或 SA？ | 將不同組織中的組織帳戶新增為 CA 或 SA？ |
  | --- | --- | --- | --- |
  |  Microsoft 帳戶 |是 |否 |否 |
  |  組織帳戶 |是 |是 |否 |

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>深入了解資源存取控制和 Active Directory
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱[了解 Azure 中的資源存取](../active-directory/active-directory-understanding-resource-access.md)。
* 如需有關 Azure Active Directory 的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](../active-directory/active-directory-how-subscriptions-associated-directory.md)和[指派 Azure Active Directory 中的管理員角色](../active-directory/active-directory-assign-admin-roles.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

