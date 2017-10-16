---
title: "如何在 Azure API 管理中建立及發行產品"
description: "了解如何在 Azure API 管理中建立及發行產品。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 73bf4451ba1b71807e22440beecc73a7e8045c5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>如何在 Azure API 管理中建立及發行產品
在 Azure API 管理中，產品包含一或多個 API，以及使用量配額與使用規定。 發行產品之後，開發人員便可訂閱產品，並開始使用產品的 API。 本主題提供建立產品、加入 API 和發行供開發人員使用的指引。

## <a name="create-product"> </a>建立產品
請在發行者入口網站新增和設定 API 的作業。 若要存取發行者入口網站，請在您「API 管理」服務的「Azure 入口網站」中按一下 [發行者入口網站]。

![發行者入口網站][api-management-management-console]

> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][Get started with Azure API Management]教學課程中的[建立 API 管理服務執行個體][Create an API Management service instance]。
> 
> 

按一下左邊功能表中的 [產品]，以顯示 [產品] 頁面，然後按一下 [新增產品]。

![產品][api-management-products]

![New product][api-management-add-new-product]

在 [名稱] 欄位中輸入產品的描述性名稱，在 [說明] 欄位中輸入產品說明。

API 管理中的產品可以是 [開放] 或 [受保護]。 受保護產品必須先擁有訂用帳戶才能使用，開放產品則可以使用而不需訂用帳戶。 若要建立需要訂用帳戶的受保護產品，請核取 [ **需要訂閱** ]。 這是預設設定。

如果您希望管理員檢閱並接受或拒絕對此產品的訂閱嘗試，請核取 [Require subscription approval]  。 如果未核取方塊，將會自動核准訂閱嘗試。 如需訂用帳戶的詳細資訊，請參閱[檢視產品的訂閱者][View subscribers to a product]。

若要允許開發人員帳戶訂閱產品多次，請核取 [ **允許多項訂閱** ] 核取方塊。 如果未核取此方塊，則每個開發人員帳戶只能訂閱產品一次。

![無限制的多項訂閱][api-management-unlimited-multiple-subscriptions]

若要限制多項同時訂閱的數目，請核取 [ **將同時訂閱數目限制為** ] 核取方塊，然後輸入訂閱限制。 在以下範例中，同時訂閱數已限制為每個開發人員帳戶四個。

![四個多項訂閱][api-management-four-multiple-subscriptions]

設定好所有新產品的選項後，請按一下 [ **儲存** ] 來建立新產品。

![產品][api-management-products-page]

> 依預設不會發行新產品，且只有 [Administrators] 群組才能看見。
> 
> 

若要設定產品，請在 [產品]  索引標籤中按一下產品名稱。

## <a name="add-apis"> </a>將 API 加入至產品
[產品] 頁面包含組態的四個連結：**摘要**、**設定**、**可見度**和**訂閱者**。 [ **摘要** ] 索引標籤可讓您加入 API，以及發行或取消發行產品。

![摘要][api-management-new-product-summary]

發行產品之前，您必須加入一或多個 API。 若要這樣做，請安一下 [加入 API 至產品] 。

![Add APIs][api-management-add-apis-to-product]

選取想要的 API，按一下 [儲存] 。

## <a name="add-description"> </a>將描述性資訊加入至產品
[ **設定** ] 索引標籤可讓您提供產品的詳細資訊，例如用途、它提供存取的 API 及其他有用的資訊。 內容以將會呼叫 API 的開發人員為對象，可使用純文字或 HTML 標記來撰寫。

![Product settings][api-management-product-settings]

若要建立需要使用訂用帳戶的受保護產品，請核取 [ **需要訂閱** ]，或是清除核取方塊，以建立不需訂用帳戶即可呼叫的開放產品。

如果您要手動核准所有產品訂閱要求，請選取 [ **需要訂閱** ]。 依預設會自動同意所有產品訂閱。

若要允許開發人員帳戶訂閱產品多次，請核取 [ **允許多項訂閱** ] 核取方塊，並選擇是否指定限制。 如果未核取此方塊，則每個開發人員帳戶只能訂閱產品一次。

選擇性填寫 [ **使用規定** ] 欄位，描述訂閱者必須接受才可使用產品的產品使用規定。

## <a name="publish-product"> </a>發行產品
產品必須發行，才能呼叫產品中的 API。 在產品的 摘要 索引標籤上，按一下 發佈，然後按一下是，發佈 表示確認。 若要將先前發行的產品設為私人，請按一下 [取消發行] 。

![Publish product][api-management-publish-product]

## <a name="make-visible"> </a>讓開發人員看見產品
[ **可見度** ] 索引標籤可讓選擇哪些角色可在開發人員入口網站上看見產品及訂閱產品。

![Product visibility][api-management-product-visiblity]

若要允許或不允許群組中的開發人員看見產品，請核取或取消核取群組旁邊的核取方塊，然後按一下儲存 。

> 如需詳細資訊，請參閱 [如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶][How to create and use groups to manage developer accounts in Azure API Management]。
> 
> 

## <a name="view-subscribers"> </a>檢視產品的訂閱者
[ **訂閱者** ] 索引標籤會列出已訂閱產品的開發人員。 按一下開發人員的名稱，即可檢視開發人員的詳細資料和設定。 在此範例中，還沒有開發人員訂閱產品。

![開發人員][api-management-developer-list]

## <a name="next-steps"> </a>後續步驟
加入想要的 API 並發行產品之後，開發人員就可以訂閱產品並開始呼叫 API。 如需有關這些項目和進階產品組態的示範教學課程，請參閱 [如何在 Azure API 管理中建立和設定進階產品設定][How create and configure advanced product settings in Azure API Management]。

如需關於使用產品的詳細資訊，請觀看以下影片。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 
