---
title: "將驗證權杖傳遞到 Azure 媒體服務 | Microsoft Docs"
description: "了解如何將驗證權杖從用戶端傳送到 Azure 媒體服務金鑰傳遞服務"
services: media-services
keywords: "content protection, DRM, token authentication, 內容保護, 權杖驗證"
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>用戶端如何將權杖傳遞到 Azure 媒體服務金鑰傳遞服務
我們不斷地接收到關於播放程式如何將權杖傳遞到我們金鑰傳遞服務的問題，其將得到驗證，而播放程式會獲得金鑰。 我們支援簡單 Web 權杖 (SWT) 和 JSON Web 權杖 (JWT) 這兩種權杖格式。 權杖驗證可以套用到任何類型的金鑰，而不論您在系統中執行的是一般加密或 AES 信封加密。

您可以使用下列方式，根據設定為目標的播放程式和平台，利用播放程式來傳遞權杖：
- 透過 HTTP 授權標頭。
> [!NOTE]
> 請注意，預期每個 OAuth 2.0 規格都有 "Bearer" 前置詞。 有一個範例播放程式，其權杖設定已裝載於 Azure 媒體播放器[示範頁面](http://ampdemo.azureedge.net/) \(英文\) 上。 請選擇 AES (JWT 權杖) 或 AES (SWT 權杖) 來設定影片來源。 權杖會透過授權標頭來傳遞。

- 透過新增含有 “token=tokenvalue” 的 Url 查詢參數。  
> [!NOTE]
> 請注意，預期不會有 "Bearer" 前置詞。 因為權杖已透過 URL 傳送，您將需要為權杖字串提供防禦。 以下是如何執行此動作的 C# 範例程式碼：

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- 透過 CustomData 欄位。
只適用於 PlayReady 授權取得，透過 PlayReady 授權取得查問的 CustomData 欄位。 在此案例中，權杖必須位於 xml 文件中，如下所述。

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
將您的驗證權杖放置於 <Token> 元素中。

- 透過替代 HLS 播放清單。 如果您需要針對 iOS/Safari 上的 AES + HLS 播放設定權杖驗證，則沒有任何方式可讓您在權杖中直接傳送。 請參閱這篇[部落格文章](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) \(英文\)，以了解如何替代播放清單來實現此案例。

## <a name="next-steps"></a>後續步驟
檢閱媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]