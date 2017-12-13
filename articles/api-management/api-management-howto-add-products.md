---
title: "如何在 Azure API 管理中建立及發行產品"
description: "了解如何在 Azure API 管理中建立及發行產品。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 89e1115291fbb2ba3499801981b70e10eb23eb94
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="create-and-publish-a-product"></a>建立和發行產品  

在 Azure API 管理中，產品包含一或多個 API，以及使用量配額與使用規定。 發行產品之後，開發人員便可訂閱產品，並開始使用產品的 API。  

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立和發行產品
> * 將 API 新增至產品

![新增的產品](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>必要條件

+ 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 以及完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-and-publish-a-product"></a>建立和發行產品

1. 按一下左邊功能表中的 [產品]，以顯示 [產品] 頁面。
2. 按一下 [+ 產品]。

    ![新增的產品](media/api-management-howto-add-products/add-product.png)

    當您新增產品時，您需要提供下列資訊： 

    |名稱|說明|
    |---|---|
    |顯示名稱|您想要其顯示在**開發人員入口網站**中的名稱。|
    |名稱|產品的描述性名稱。|
    |說明|[說明] 欄位可讓您提供產品的詳細資訊，例如用途、產品可供其存取的 API 及其他有用的資訊。|
    |State|如果您想要發行產品，請按 [發行]。 產品必須發行，才能呼叫產品中的 API。 依預設不會發行新產品，且只有 [Administrators] 群組才能看見。|
    |需要核准|如果您希望管理員檢閱並接受或拒絕對此產品的訂閱嘗試，請核取 [Require subscription approval]  。 如果未核取方塊，將會自動核准訂閱嘗試。 |
    |訂閱計數限制|若要限制多個訂閱同時進行的計數，請輸入訂閱限制。 |
    |法律條款|您可以包含訂閱者必須接受才可使用產品的產品使用規定。|
    |API|產品是一或多個 API 的關聯。 您可以包括數個 API，並透過開發人員入口網站將它們提供給開發人員。 <br/> 在產品建立期間，您可以新增現有的 API。 您可以在之後將 API 新增至產品中，可以從產品的 [設定] 頁面或建立 API 時進行新增。|<br/>開發人員必須先訂閱產品，才能取得 API 的存取權。 當他們訂閱時，就能取得適用於該產品中任何 API 的中訂用帳戶金鑰。<br/> 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此根據預設，您已訂閱每一項產品。|

3. 按一下 [儲存] 來建立新產品。

### <a name="add-more-configurations"></a>新增更多設定

將其儲存之後，您可以選擇 [設定] 索引標籤，繼續設定產品。 

從 [訂用帳戶] 索引標籤中，檢視/新增產品訂閱者。

從 [存取控制] 索引標籤中，設定開發人員或來賓適用的產品可見性。

## <a name="add-apis"> </a>將 API 加入至產品

產品是一或多個 API 的關聯。 您可以包括數個 API，並透過開發人員入口網站將它們提供給開發人員。 在產品建立期間，您可以新增現有的 API。 您可以在之後將 API 新增至產品中，可以從產品的 [設定] 頁面或建立 API 時進行新增。

開發人員必須先訂閱產品，才能取得 API 的存取權。 當他們訂閱時，就能取得適用於該產品中任何 API 的中訂用帳戶金鑰。 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此根據預設，您已訂閱每一項產品。

### <a name="add-an-api-to-an-existing-product"></a>將 API 新增至現有的產品

1. 選取產品。
2. 選取 [API] 索引標籤。
3. 按一下 [+API]。
4. 選擇 API，然後按一下 [建立]。

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立和發行產品
> * 將 API 新增至產品

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [建立空白 API 和模擬 API 回應](mock-api-responses.md)
