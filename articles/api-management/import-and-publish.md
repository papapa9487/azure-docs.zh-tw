---
title: "在 Azure API 管理中匯入和發佈您的第一個 API | Microsoft Docs"
description: "了解如何使用 API 管理來匯入和發佈您的第一個 API。"
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
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: cd6ceaf5f8cdcfbde5d0d2bebb4b89488d0122e9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="import-and-publish-your-first-api"></a>匯入和發佈您的第一個 API 

本教學課程示範如何匯入「OpenAPI 規格」後端 API (位於 http://conferenceapi.azurewebsites.net?format=json)。 這個後端 API 是由 Microsoft 所提供且裝載於 Azure 上。 

一旦將此後端 API 匯入至 API 管理 (APIM)，APIM API 就會變成適用於此後端 API 的外觀。 當您匯入此後端 API 時，來源 API 和 APIM API 會完全相同。 APIM 可讓您根據需求自訂外觀，而不需碰觸到後端 API。 如需詳細資訊，請參閱[轉換及保護您的 API](transform-api.md)。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 匯入第一個 API
> * 在 Azure 入口網站中測試 API
> * 在開發人員入口網站中測試 API

![新增 API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>先決條件

完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>匯入和發佈後端 API

本節示範如何匯入和發佈 OpenAPI 規格後端 API。
 
1. 選取 [API 管理] 下方的 [API]。
2. 從清單中選取 [OpenAPI 規格]。

    ![建立 API](./media/api-management-get-started/create-api.png)

    您可以在建立期間或稍後前往 [設定] 索引標籤來設定 API 值。  

    |設定|值|描述|
    |---|---|---|
    |**OpenAPI 規格**|http://conferenceapi.azurewebsites.net?format=json|參考實作 API 的服務。 API 管理則將要求轉送至此位址。|
    |**顯示名稱**|*Demo Conference API*|如果您在輸入服務 URL 之後按下 Tab，APIM 將根據 json 中的內容填寫此欄位。 <br/>此名稱會顯示於開發人員入口網站中。|
    |**名稱**|*demo-conference-api*|為 API 提供唯一的名稱。 <br/>如果您在輸入服務 URL 之後按下 Tab，APIM 將根據 json 中的內容填寫此欄位。|
    |**描述**|提供 API 的選擇性描述。|如果您在輸入服務 URL 之後按下 Tab，APIM 將根據 json 中的內容填寫此欄位。|
    |**API URL 尾碼**|*conference*|這個尾碼會附加到 API 管理服務的基底 URL。 API 管理依尾碼來區分 API，因此，特定發行者的每一個 API 必須有唯一的尾碼。|
    |**URL 配置**|*HTTPS*|決定可使用哪些通訊協定來存取 API。 |
    |**產品**|*無限制*| 透過將 API 關聯至某個產品來發佈 API。 若要選擇性地將這個新的 API 新增至產品中，請輸入產品名稱。 可以重複此步驟多次來將 API 加入多個產品。<br/>產品是一或多個 API 的關聯。 您可以包括數個 API，並透過開發人員入口網站將它們提供給開發人員。 開發人員必須先訂閱產品，才能取得 API 的存取權。 當他們訂閱時，就能取得適用於該產品中任何 API 的訂用帳戶金鑰。 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此根據預設，您已訂閱每一個產品。<br/> 根據預設，每個「API 管理」執行個體都隨附兩個範例產品：**入門**與**無限制**。 |
3. 選取 [建立]。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>在 Azure 入口網站中測試新的 APIM API

您可以從 Azure 入口網站直接呼叫作業，以便檢視和測試 API 的作業。  
1. 選取您在上一個步驟中建立的 API。
2. 按 [測試] 索引標籤。

    ![測試 API](./media/api-management-get-started/test-api.png)
3. 按一下 [GetSpeakers]。

    此頁面會顯示查詢參數的欄位，但在此案例中，我們沒有任何適用欄位。 此頁面也會顯示標頭的欄位。 其中一個標頭是 "Ocp-Apim-Subscription-Key"，它適用於與此 API 相關聯之產品的訂用帳戶金鑰。 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此會自動填入此金鑰。 
4. 按 [傳送]。

    後端會回應 **200 確定**與部分資料。

## <a name="call-operation"> </a>從開發人員入口網站呼叫作業

您也可以在**開發人員入口網站**呼叫作業來測試 API。 

1. 選取您在「匯入和發佈後端 API」步驟中建立的 API。
2. 按 [開發人員入口網站]。

    ![在開發人員入口網站中進行測試](./media/api-management-get-started/developer-portal.png)

    「開發人員入口網站」隨即開啟。
3. 選取 [API]。
4. 選取 [Demo Conference API]。
5. 按一下 [GetSpeakers]。
    
    此頁面會顯示查詢參數的欄位，但在此案例中，我們沒有任何適用欄位。 此頁面也會顯示標頭的欄位。 其中一個標頭是 "Ocp-Apim-Subscription-Key"，它適用於與此 API 相關聯之產品的訂用帳戶金鑰。 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此會自動填入此金鑰。
6. 按 [試試看]。
7. 按 [傳送]。
    
    叫用作業之後，開發人員入口網站會顯示 [回應狀態]、[回應標頭]，以及任何的 [回應內容]。

## <a name="next-steps"> </a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 匯入第一個 API
> * 在 Azure 入口網站中測試 API
> * 在開發人員入口網站中測試 API

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [建立和發佈產品](api-management-howto-add-products.md)