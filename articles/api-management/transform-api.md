---
title: "使用 Azure API 管理轉換及保護您的 API| Microsoft Docs"
description: "了解如何使用配額和節流 (速率限制) 原則保護您的 API。"
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
ms.openlocfilehash: 772f3828d85c54e7b8bb44c857e555175b7444cc
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="transform-and-protect-your-api"></a>轉換及保護您的 API 

本教學課程示範如何轉換您的 API，使它不會揭露私人的後端資訊。 例如，您可以隱藏在後端執行之技術堆疊的相關資訊。 您也可以隱藏 API HTTP 回應主體中出現的原始 URL，改為將它們重新導向至 APIM 閘道。

本教學課程示範透過使用「Azure API 管理」設定速率限制，來為後端 API 新增保護是多麼簡單容易的工作。 例如，您可以限制呼叫 API 的呼叫次數，使得開發人員不會過度使用它。 如需詳細資訊，請參閱 [API 管理原則](api-management-policies.md)。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 轉換 API 以刪除回應標頭
> * 使用 APIM 閘道 URL 取代 API 回應主體中的原始 URL
> * 新增速率限制原則 (節流) 來保護 API
> * 測試轉換

![原則](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>先決條件

+ 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 同時也請完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。
 
[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>轉換 API 以刪除回應標頭

本節示範如何隱藏您不想向使用者顯示的 HTTP 標頭。 在此範例中，會在 HTTP 回應中刪除下列標頭：

* **X-Powered-By**
* **X-AspNet-Version**

### <a name="test-the-original-response"></a>測試原始回應

查看原始回應：

1. 選取 [API] 索引標籤。
2. 按一下 API 清單中的 [Demo Conference API]。
3. 選取 [GetSpeakers] 作業。
4. 按一下畫面頂端的 [測試] 索引標籤。
5. 按畫面底部的 [傳送] 按鈕。 

    此時，您會看見如下的原始回應：

    ![原則](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>設定轉換原則

1. 瀏覽至您的 APIM 執行個體。
2. 選取 [API] 索引標籤。
3. 按一下 API 清單中的 [Demo Conference API]。
4. 選取 [所有作業]。
5. 選取畫面頂端的 [設計] 索引標籤。
6. 在 [輸出處理] 視窗中，按一下三角形 (鉛筆旁邊)。
7. 選取 [程式碼編輯器]。
    
     ![編輯原則](./media/set-edit-policies/set-edit-policies01.png)
9. 將游標放在 **<outbound>** 元素內部。
10. 在右側視窗的 [轉換原則] 下方，按兩次 [+ 設定 HTTP 標頭] (以插入兩個原則程式碼片段)。

    ![原則](./media/transform-api/transform-api.png)
11. 修改您的 **<outbound>** 程式碼，使它看起來如下：

        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
                
## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>使用 APIM 閘道 URL 取代 API 回應主體中的原始 URL

本節示範如何隱藏 API HTTP 回應主體中出現的原始 URL，改為將它們重新導向至 APIM 閘道。

### <a name="test-the-original-response"></a>測試原始回應

查看原始回應：

1. 選取 [API] 索引標籤。
2. 按一下 API 清單中的 [Demo Conference API]。
3. 選取 [GetSpeakers] 作業。
4. 按一下畫面頂端的 [測試] 索引標籤。
5. 按畫面底部的 [傳送] 按鈕。 

    此時，您會看見如下的原始回應：

    ![原則](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>設定轉換原則

1. 瀏覽至您的 APIM 執行個體。
2. 選取 [API] 索引標籤。
3. 按一下 API 清單中的 [Demo Conference API]。
4. 選取 [所有作業]。
5. 選取畫面頂端的 [設計] 索引標籤。
6. 在 [輸出處理] 視窗中，按一下三角形 (鉛筆旁邊)。
7. 選取 [程式碼編輯器]。
8. 將游標放在 **<outbound>** 元素內部。
9. 在右側視窗的 [轉換原則] 下方，按一下 [+ 尋找並取代主體中的字串]。
10. 修改您的 **<find-and-replace** 程式碼 (位於 **<outbound>** 元素中)，取代 URL 以符合您的 APIM 閘道。 例如：

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>新增速率限制原則 (節流) 來保護 API

本節示範如何透過設定速率限制來為後端 API 新增保護。 例如，您可以限制呼叫 API 的呼叫次數，使得開發人員不會過度使用它。 在此範例中，會針對每個訂用帳戶 ID，將限制設定為每 15 秒呼叫 3 次。15 秒之後，開發人員就可重試呼叫 API。

1. 瀏覽至您的 APIM 執行個體。
2. 選取 [API] 索引標籤。
3. 按一下 API 清單中的 [Demo Conference API]。
4. 選取 [所有作業]。
5. 選取畫面頂端的 [設計] 索引標籤。
6. 在 [輸入處理] 視窗中，按一下三角形 (鉛筆旁邊)。
7. 選取 [程式碼編輯器]。
8. 將游標放在 **<inbound>** 元素內部。
9. 在右側視窗的 [存取限制原則] 下方，按一下 [+ 限制每個金鑰的呼叫速率]。
10. 將您的 **<rate-limit-by-key** 程式碼 (位於 **<inbound>** 元素中) 修改為下列程式碼：

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>測試轉換
        
此時，您的原則程式碼看起來如下：

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

本節的其餘部分將測試您在此文章中設定的原則轉換。

### <a name="test-the-stripped-response-headers"></a>測試已刪除的回應標頭

1. 瀏覽至您的 APIM 執行個體。
2. 選取 [API] 索引標籤。
3. 按一下 API 清單中的 [Demo Conference API]。
4. 按一下 [GetSpeakers] 作業。
5. 選取 [測試] 索引標籤。
6. 按 [傳送]。

    此時，您可以看到已刪除標頭：

    ![原則](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>測試已取代的 URL

1. 瀏覽至您的 APIM 執行個體。
2. 選取 [API] 索引標籤。
3. 按一下 API 清單中的 [Demo Conference API]。
4. 按一下 [GetSpeakers] 作業。
5. 選取 [測試] 索引標籤。
6. 按 [傳送]。

    此時，您可以看到已取代 URL。

    ![原則](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>測試速率限制 (節流)

1. 瀏覽至您的 APIM 執行個體。
2. 選取 [API] 索引標籤。
3. 按一下 API 清單中的 [Demo Conference API]。
4. 按一下 [GetSpeakers] 作業。
5. 選取 [測試] 索引標籤。
6. 連續按三次 [傳送]。

    傳送要求 3 次之後，您會收到 **429 太多要求**的回應。
7. 大約等候 15 秒之後，再按一次 [傳送]。 此時，您應該得到 **200 確定**的回應。

    ![節流](./media/transform-api/test-throttling.png)

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 轉換 API 以刪除回應標頭
> * 使用 APIM 閘道 URL 取代 API 回應主體中的原始 URL
> * 新增速率限制原則 (節流) 來保護 API
> * 測試轉換

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [監視您的 API](api-management-howto-use-azure-monitor.md)
