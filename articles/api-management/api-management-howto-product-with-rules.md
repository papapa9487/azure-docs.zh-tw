---
title: "使用 Azure API 管理保護 API| Microsoft Docs"
description: "了解如何使用配額和節流 (頻率限制) 原則保護您的 API。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 450dc368-d005-401d-ae64-3e1a2229b12f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 5553bcb8f9fd38630f694151dc644a684266387c
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>使用 Azure API 管理以頻率限制保護 API
本指南說明藉由使用 Azure API 管理設定頻率限制和配額原則，來為後端 API 增加保護是多麼簡單容易的工作。

在本教學課程中，您會建立「免費試用」的 API 產品，讓開發人員可使用[限制每個訂用帳戶的呼叫頻率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate)和[設定每個訂用帳戶的使用量配額](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)原則，每分鐘最多呼叫您的 API 10 次，每週最多呼叫 200 次。 您接著會發佈此 API，並測試頻率限制原則。

有關使用 [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) 和 [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) 原則進行進階要求節流的案例，請參閱[以 Azure API 管理進行進階要求節流](api-management-sample-flexible-throttling.md)。

## <a name="create-product"> </a>建立產品
在本步驟中，您將建立不需核准訂用帳戶的免費試用產品。

> [!NOTE]
> 如果您已經有設定好的產品，且想要在本教學課程中使用該產品，可以直接跳到[設定呼叫頻率限制和配額原則][Configure call rate limit and quota policies]，並從該處使用您的產品 (而非「免費試用」產品) 來進行本教學課程。
> 
> 

若要開始，請在 API 管理服務的 Azure 入口網站中按一下 [發佈者入口網站]。

![發行者入口網站][api-management-management-console]

> 如果您尚未建立 API 管理服務執行個體，請參閱[在 Azure API 管理中管理您的第一個 API][Manage your first API in Azure API Management] 教學課程中的[建立 API 管理服務執行個體][Create an API Management service instance]。
> 
> 

從左側的 [API 管理] 功能表按一下 [產品]，以顯示 [產品] 頁面。

![Add product][api-management-add-product]

按一下 [加入產品] 以顯示 [加入新的產品] 對話方塊。

![Add new product][api-management-new-product-window]

在 [標題] 文字方塊中輸入**免費試用**。

在 [描述] 文字方塊中輸入下列文字：**存取遭到拒絕後，訂戶每分鐘可以執行 10 次呼叫，每週最多 200 次呼叫**。

API 管理中的產品可以是受保護或開放的。 受保護的產品必須先訂閱才能使用。 開放產品不需要訂用帳戶即可使用。 若要建立需要訂用帳戶的受保護產品，請務必選取 [需要訂用帳戶]  。 這是預設設定。

如果您希望系統管理員檢閱並接受或拒絕對此產品的訂用帳戶嘗試，請選取 [需要訂用帳戶核准] 。 如果未選取此核取方塊，將會自動核准訂用帳戶嘗試。 在此範例中會自動核准訂用帳戶，因此請勿選取此方塊。

若要允許開發人員帳戶多次訂閱新產品，請選取 [允許多項同時訂閱]  核取方塊。 本教學課程不會使用多項同時訂閱，所以維持未核取即可。

輸入所有值之後，按一下 [儲存]  來建立產品。

![Product added][api-management-product-added]

依預設， **Administrator** 群組中的使用者可看見新產品。 我們即將加入 **Developers** 群組。 按一下 [免費試用]，然後選取 [可見度] 索引標籤。

> 在 API 管理中，群組的作用是管理產品對於開發人員的可見度。 產品會將可見度授與群組，而開發人員可檢視並訂閱其所屬群組可見的產品。 如需詳細資訊，請參閱[如何在 Azure API 管理中建立和使用群組][How to create and use groups in Azure API Management]。
> 
> 

![Add developers group][api-management-add-developers-group]

選取 [開發人員] 核取方塊，然後按一下 [儲存]。

## <a name="add-api"> </a>將 API 加入至產品
在教學課程的這個步驟中，我們會將 Echo API 加入至新的「免費試用」產品。

> 每個 API 管理服務執行個體隨附預先設定的範例 Echo API，可供您試驗與了解 API 管理。 如需詳細資訊，請參閱[在 Azure API 管理中管理您的第一個 API][Manage your first API in Azure API Management]。
> 
> 

從左側的 [API 管理] 功能表按一下 [產品]，然後按一下 [免費試用] 來設定產品。

![Configure product][api-management-configure-product]

按一下 [加入 API 至產品] 。

![Add API to product][api-management-add-api]

請選取 [Echo API]，然後按一下 [儲存]。

![Add Echo API][api-management-add-echo-api]

## <a name="policies"> </a>設定呼叫頻率限制和配額原則
頻率限制和配額是在原則編輯器中設定。 我們將在本教學課程中加入的兩個原則為[限制每個訂用帳戶的呼叫頻率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate)和[設定每個訂用帳戶的使用量配額](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)原則。 這些原則必須套用於產品範圍。

在左邊的 [API 管理] 功能表下，按一下 [原則]。 在 [產品] 清單中，按一下 [免費試用]。

![Product policy][api-management-product-policy]

按一下 [加入原則]  來匯入原則範本，並開始建立頻率限制和配額原則。

![Add policy][api-management-add-policy]

頻率限制和配額原則為輸入原則，因此將游標放置在輸入元素中。

![Policy editor][api-management-policy-editor-inbound]

捲動原則清單並找出 [限制每個訂用帳戶的呼叫率] 原則項目。

![Policy statements][api-management-limit-policies]

游標放置在 **inbound** 原則元素中之後，按一下 [限制每個訂用帳戶的呼叫頻率] 旁的箭頭來插入其原則範本。

```xml
<rate-limit calls="number" renewal-period="seconds">
<api name="name" calls="number">
<operation name="name" calls="number" />
</api>
</rate-limit>
```

如您在程式碼片段中所見，此原則允許設定產品的 API 和作業限制。 我們不會在本教學課程中使用該功能，因此請將 **rate-limit** 元素中的 **api** 和 **operation** 元素刪除，只保留外部 **rate-limit** 元素，如以下範例所示。

```xml
<rate-limit calls="number" renewal-period="seconds">
</rate-limit>
```

在「免費試用」產品中，允許的呼叫頻率為每分鐘 10 次呼叫，因此請輸入 **10** 做為 **calls** 屬性的值，輸入 **60** 做為 **renewal-period** 屬性的值。

```xml
<rate-limit calls="10" renewal-period="60">
</rate-limit>
```

若要設定 [設定每個訂用帳戶的使用量配額] 原則，請將游標放置在 **inbound** 元素內新加入的 **rate-limit** 元素正下方，然後找出並按一下 [設定每個訂用帳戶的使用量配額] 左側的箭頭。

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
<api name="name" calls="number" bandwidth="kilobytes">
<operation name="name" calls="number" bandwidth="kilobytes" />
</api>
</quota>
```

與 [設定每個訂用帳戶的使用量配額] 原則類似，[設定每個訂用帳戶的使用量配額] 原則允許設定產品的 API 和作業上限。 我們不會在本教學課程中使用該功能，因此請將 **quota** 元素中的 **api** 和 **operation** 元素刪除，如以下範例所示。

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
</quota>
```

配額可以基於每一間隔的呼叫數、頻寬或兩者。 在本教學課程中，我們不會基於頻寬進行節流，因此請刪除 **bandwidth** 屬性。

```xml
<quota calls="number" renewal-period="seconds">
</quota>
```

在免費試用產品中，配額為每週 200 個呼叫。 指定 **200** 做為 **calls** 屬性的值，然後指定 **604800** 做為 **renewal-period** 屬性的值。

```xml
<quota calls="200" renewal-period="604800">
</quota>
```

> 原則間隔是依秒來指定。 若要計算一週的間隔，您可以將天數 (7) x 一天的小時數 (24) x 每小時的分鐘數 (60) x 每分鐘的秒數 (60)：7 * 24 * 60 * 60 = 604800。
> 
> 

完成設定原則之後，應該符合以下範例。

```xml
<policies>
    <inbound>
        <rate-limit calls="10" renewal-period="60">
        </rate-limit>
        <quota calls="200" renewal-period="604800">
        </quota>
        <base />

</inbound>
<outbound>

    <base />

    </outbound>
</policies>
```

設定需要的原則之後，按一下 [儲存] 。

![Save policy][api-management-policy-save]

## <a name="publish-product"> </a> 發佈產品
現在已加入 API，也已設定原則，產品必須發佈才能供開發人員使用。 從左側的 [API 管理] 功能表按一下 [產品]，然後按一下 [免費試用] 來設定產品。

![Configure product][api-management-configure-product]

按一下 [發佈]，然後按 [是，發佈] 確認。

![Publish product][api-management-publish-product]

## <a name="subscribe-account"> </a>為開發人員帳戶訂閱產品
現在已發佈產品，產品即可供開發人員訂閱和使用。

> API 管理的管理員執行個體會自動訂閱每個產品。 在這個教學課程步驟中，我們會將其中一個非系統管理員開發人員帳戶訂閱「免費試用」產品。 如果您的開發人員帳戶隸屬於系統管理員角色，即使您已訂閱，也可以遵循此步驟。
> 
> 

在左側的 [API 管理] 功能表上按一下 [使用者]，然後按一下您的開發人員帳戶的名稱。 在此範例中，我們要使用 **Clayton Gragg** 開發人員帳戶。

![Configure developer][api-management-configure-developer]

按一下 [ **加入訂閱**]。

![加入訂閱][api-management-add-subscription-menu]

選取 [免費試用]，然後按一下 [訂閱]。

![加入訂閱][api-management-add-subscription]

> [!NOTE]
> 在本教學課程中，沒有針對「免費試用」產品啟用多項同時訂閱。 如果有啟用，則系統會提示您為訂用帳戶命名，如以下範例所示。
> 
> 

![加入訂閱][api-management-add-subscription-multiple]

按一下 [訂閱] 後，產品會出現在使用者的 [訂用帳戶] 清單中。

![已新增訂用帳戶][api-management-subscription-added]

## <a name="test-rate-limit"> </a>呼叫作業並測試頻率限制
現在免費試用產品已設定和發佈，我們可以呼叫一些作業並測試頻率限制原則。
在右上角的功能表中按一下 [開發人員入口網站]  ，切換到開發人員入口網站。

![開發人員入口網站][api-management-developer-portal-menu]

在上方功能表中按一下 [API]，然後按一下 [Echo API]。

![開發人員入口網站][api-management-developer-portal-api-menu]

按一下 [GET 資源]，然後按一下 [嘗試]。

![Open console][api-management-open-console]

保留預設的參數值，然後選取「免費試用」產品的訂用帳戶金鑰。

![訂用帳戶金鑰][api-management-select-key]

> [!NOTE]
> 如果您有多個訂用帳戶，請務必選取**免費試用**的金鑰，否則在先前步驟中設定的原則將不會生效。
> 
> 

按一下 [傳送]，然後檢視回應。 記下 [200 OK] 的 [回應狀態]。

![Operation results][api-management-http-get-results]

在頻率超過每分鐘 10 個呼叫的頻率限制原則時，按一下 [傳送]  。 超出頻費限制原則之後，會傳回 [429 太多要求]  回應狀態。

![Operation results][api-management-http-get-429]

**回應內容** 指出重試成功的剩餘間隔時間。

每分鐘 10 次呼叫的頻率限制原則生效時，在超出頻率限制之前，從最初成功呼叫產品 10 次後需要經過 60 秒，否則後續的呼叫會失敗。 在本範例中，剩餘間隔為 54 秒。

## <a name="next-steps"> </a>後續步驟
* 請觀看以下影片中設定頻率限制和配額的示範。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Manage your first API in Azure API Management]: api-management-get-started.md
[How to create and use groups in Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configure call rate limit and quota policies]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota

