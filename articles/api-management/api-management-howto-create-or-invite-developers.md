---
title: "如何在 Azure API 管理中管理使用者帳戶 | Microsoft Docs"
description: "了解如何在 Azure API 管理中建立或邀請使用者"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 6f2fd5e4c1a51fe9d1652c9970bcd8d76b25ab60
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>如何在 Azure API 管理中管理使用者帳戶
在 API 管理中，開發人員是指您使用 API 管理所公開之 API 的使用者。 本指南示範如何建立並邀請開發人員使用您以 API 管理執行個體提供給他們的 API 和產品。 如需以程式設計方式管理使用者帳戶的相關資訊，請參閱 [API 管理 REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) 參考中的[使用者實體](https://msdn.microsoft.com/library/azure/dn776330.aspx)文件。

## <a name="create-developer"> </a>建立新的開發人員
若要建立新的開發人員，請在您「API 管理」服務的「Azure 入口網站」中按一下 [發行者入口網站]。 這會帶您前往 API 管理發行者入口網站。 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][Create an API Management service instance]。

![發行者入口網站][api-management-management-console]

從左邊的 [API 管理] 功能表中按一下 [使用者]，然後按一下 [使用者]。

![Create developer][api-management-create-developer]

輸入新開發人員的 [電子郵件]、[密碼] 和 [名稱]，然後按一下 [儲存]。

![Create developer][api-management-add-new-user]

依預設，新建立的開發人員帳戶為 [作用中]，且與 [開發人員] 群組相關聯。

![New developer][api-management-new-developer]

處於「作用中」  狀態的開發人員帳戶可用來存取擁有訂用帳戶的所有 API。 若要將新建立的開發人員與其他群組建立關聯，請參閱 [如何將群組與開發人員建立關聯][How to associate groups with developers]。

## <a name="invite-developer"> </a>邀請開發人員
若要邀請開發人員，請從左邊的 [API 管理] 功能表中按一下 [使用者]，然後按一下 [邀請使用者]。

![Invite developer][api-management-invite-developer]

輸入開發人員的名稱和電子郵件地址，然後按一下 [邀請] 。

![Invite developer][api-management-invite-developer-window]

這時會顯示確認訊息，但剛獲邀的開發人員在尚未接受邀請之前，都不會出現在清單中。 

![Invite confirmation][api-management-invite-developer-confirmation]

邀請開發人員時，有一封電子郵件會傳送給開發人員。 此電子郵件是以範本產生，可自訂。 如需詳細資訊，請參閱 [設定電子郵件範本][Configure email templates]。

接受邀請時，帳戶就會變成作用中。

## <a name="block-developer"> </a> 停用或重新啟用開發人員帳戶
依預設，新建立或邀請的開發人員帳戶為「作用中」 。 若要停用開發人員帳戶，請按一下 [封鎖] 。 若要重新啟用已封鎖的開發人員帳戶，請按一下 [啟用] 。 已封鎖的開發人員帳戶無法存取開發人員入口網站或呼叫任何 API。 若要刪除使用者帳戶，請按一下 [刪除] 。

![Block developer][api-management-new-developer]

## <a name="reset-a-user-password"></a>重設使用者密碼
若要重設使用者帳戶的密碼，請按一下帳戶的名稱。

![重設密碼][api-management-view-developer]

按一下 [重設密碼]  連結可傳送連結給使用者，以便重設其密碼。

![重設密碼][api-management-reset-password]

若要以程式設計方式使用使用者帳戶，請參閱 [API 管理 REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) 參考中的[使用者實體](https://msdn.microsoft.com/library/azure/dn776330.aspx)文件。 若要將使用者帳戶密碼重設為特定值，您可以使用 [更新使用者](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) 作業並指定所要的密碼。

## <a name="pending-verification"></a>擱置驗證
![擱置驗證][api-management-pending-verification]

## <a name="next-steps"> </a>後續步驟
建立開發人員帳戶之後，您可以將它與角色建立關聯，並讓它訂閱產品和 API。 如需詳細資訊，請參閱[如何建立和使用群組][How to create and use groups]。

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
