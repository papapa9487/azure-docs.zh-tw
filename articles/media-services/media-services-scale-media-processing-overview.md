---
title: "調整媒體處理概觀 | Microsoft Docs"
description: "本主題為透過 Azure 媒體服務調整媒體處理的概觀。"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 165e4394d21a0202a320a8697360b715517bc7a2
ms.contentlocale: zh-tw
ms.lasthandoff: 03/15/2017

---
# <a name="scaling-media-processing-overview"></a>調整媒體處理概觀
此頁面提供調整媒體處理的方式及原因的概觀。 

## <a name="overview"></a>概觀
媒體服務帳戶是與保留單元類型相關聯，後者決定媒體處理工作的速度。 您可以選擇下列的保留單元類型：**S1**、**S2** 或 **S3**。 例如，在執行相同編碼作業的前提下，使用 **S2** 保留單元類型的速度會比 **S1** 類型快。 如需詳細資訊，請參閱 [保留單元類型](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)。

除了指定保留單元類型之外，您還可以指定使用保留單元來佈建帳戶。 佈建的保留單元數目可決定指定帳戶中可同時處理的媒體工作數目。 例如，如果帳戶有五個保留單元，則只要有工作需要處理，就會同時執行五個媒體工作。 剩餘的工作會在佇列中等待，當執行中的工作完成時，就循序地挑選來處理。 如果帳戶未佈建任何保留單元，則會循序地挑選工作。 在此情況下，一件工作完成與下一件工作開始之間的等待時間，視系統中的資源可用性而定。

## <a name="choosing-between-different-reserved-unit-types"></a>選擇不同的保留單元類型
下表可協助您在不同的編碼速度之間做出決定。 也提供幾個基準測試案例，以及 SAS URL 供您下載您可自行執行測試的影片︰

| 案例 | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| 預定的使用案例 |單一位元速率編碼。 <br/>在 SD或如下解決方法的檔案、對時間不敏感、低成本。 |單一位元速率和多重位元速率編碼。<br/>SD 和 HD 編碼的一般使用方式。 |單一位元速率和多重位元速率編碼。<br/>Full HD 和 4K 解析度影片。 對時間敏感、周轉時間更快的編碼。 |
| 基準測試 |[輸入檔案︰長度 5 分鐘，每秒 29.97 格畫面的解析度為 640x360p](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)。<br/><br/>編碼為相同解析度的單一位元速率 MP4 檔案，大約需要 11 分鐘。 |[輸入檔案︰長度 5 分鐘，每秒 29.97 格畫面的解析度為 1280x720p](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>具有「H264 單一位元速率 720p」預設值的編碼會花大約 5 分鐘。<br/><br/>具有「H264 多重位元速率 720p」預設值的編碼會花大約 11.5 分鐘。 |[輸入檔案︰長度 5 分鐘，每秒 29.97 格畫面的解析度為 1920x1080p](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)。 <br/><br/>具有「H264 單一位元速率 1080p」預設值的編碼會花大約 2.7 分鐘。<br/><br/>具有「H264 多重位元速率 1080p」預設值的編碼會花大約 5.7 分鐘。 |

## <a name="considerations"></a>考量
> [!IMPORTANT]
> 請檢閱本章節所述的考量。  
> 
> 

* 保留單元用於平行化所有媒體處理，包括使用 Azure 媒體索引器的索引工作。  不過，與編碼不同，索引工作的處理速度不會因為使用較快的保留單元而變快。
* 如果使用共用的集區，也就是沒有任何保留單元，則編碼工作會和 S1 RU 有相同的效能。 不過，您的工作在已排入佇列的狀態下可以花費的時間沒有上限，而且在任何時候，最多只會執行一個工作。
* 下列資料中心不提供 **S2** 保留單元類型：巴西南部和印度西部。
* 下列資料中心不提供 **S3** 保留單元類型：印度西部。

## <a name="billing"></a>計費

您的費用取決於使用媒體保留單元的實際分鐘數。 如需詳細說明，請參閱[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/)頁面的＜常見問題集＞一節。   

## <a name="quotas-and-limitations"></a>配額和限制
如需配額和限制以及如何開啟支援票證的相關資訊，請參閱 [配額和限制](media-services-quotas-and-limitations.md)。

## <a name="next-step"></a>後續步驟
使用這些技術的其中之一達成調整媒體處理工作︰ 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [入口網站](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


