---
title: "MES 的工作預設 (媒體編碼器標準) | Microsoft Docs"
description: "本主題提供 MES 的工作預設 (媒體編碼器標準) 概觀。"
author: Juliako
manager: SyntaxC4
editor: 
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: e9018ffa74c2895560adbd4c0d2b83ee67e9eb1c
ms.contentlocale: zh-tw
ms.lasthandoff: 03/03/2017

---

# <a name="task-presets-for-mes-media-encoder-standard"></a>MES 的工作預設 (媒體編碼器標準)

**媒體編碼器標準**會定義一組編碼預設，供您在建立編碼作業時使用。 如果您想要將視訊編碼以使用媒體服務進行串流處理，建議使用「彈性資料流」預設。 當您指定這個預設時，媒體編碼器標準將[自動產生位元速率階梯](media-services-autogen-bitrate-ladder-with-mes.md)。 

不過，如果您需要自訂編碼預設，則應採用本節中定義的其中一個編碼預設做為您自訂組態的範本。 如需這些預設中每個元素的意義說明及每個元素的有效值，請參閱[媒體編碼器標準結構描述](media-services-mes-schema.md)主題。  
  
> [!NOTE]
>  使用 4k 編碼的預設值時，您應該取得 `S3` 保留單元類型。 如需詳細資訊，請參閱 [如何調整編碼](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units)。  
  
使用媒體編碼器標準時，預設會啟用旋轉。 如果您的視訊是在智慧型手機或其他行動裝置上以直向模式錄製，根據預設，這些預設會將它們旋轉成橫向模式後再編譯 (有別於使用 Azure 媒體編碼器時，視訊旋轉為手動操作，如[此部落格](http://azure.microsoft.com/blog/2014/08/21/advanced-encoding-features-in-azure-media-encoder/)中的＜視訊旋轉＞所述)。  
  
可用的預設：  
  
 [H264 多重位元速率 1080p 音訊 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) 會產生一組 8 個對齊 GOP 的 MP4 檔案 (範圍從 6000 kbps 到 400 kbps) 和 AAC 5.1 音訊。  
  
 [H264 多重位元速率 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) 會產生一組 8 個對齊 GOP 的 MP4 檔案 (範圍從 6000 kbps 到 400 kbps) 和立體聲 AAC 音訊。  
  
 [H264 多重位元速率 16x9 (適用於 iOS)](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) 會產生一組 8 個對齊 GOP 的 MP4 檔案 (範圍從 8500 kbps 到 200 kbps) 和立體聲 AAC 音訊。  
  
 [H264 多重位元速率 16x9 SD 音訊 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) 會產生一組 5 個對齊 GOP 的 MP4 檔案 (範圍從 1900 kbps 到 400 kbps) 和 AAC 5.1 音訊。  
  
 [H264 多重位元速率 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) 會產生一組 5 個對齊 GOP 的 MP4 檔案 (範圍從 1900 kbps 到 400 kbps) 和立體聲 AAC 音訊。  
  
 [H264 多重位元速率 4K 音訊 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) 會產生一組 12 個對齊 GOP 的 MP4 檔案 (範圍從 20000 kbps 到 1000 kbps) 和 AAC 5.1 音訊。  
  
 [H264 多重位元速率 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) 會產生一組 12 個對齊 GOP 的 MP4 檔案 (範圍從 20000 kbps 到 1000 kbps) 和立體聲 AAC 音訊。  
  
 [H264 多重位元速率 4x3 (適用於 iOS)](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) 會產生一組 8 個對齊 GOP 的 MP4 檔案 (範圍從 8500 kbps 到 200 kbps) 和立體聲 AAC 音訊。  
  
 [H264 多重位元速率 4x3 SD 音訊 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) 會產生一組 5 個對齊 GOP 的 MP4 檔案 (範圍從 1600 kbps 到 400 kbps) 和 AAC 5.1 音訊。  
  
 [H264 多重位元速率 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) 會產生一組 5 個對齊 GOP 的 MP4 檔案 (範圍從 1600 kbps 到 400 kbps) 和立體聲 AAC 音訊。  
  
 [H264 多重位元速率 720p 音訊 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) 會產生一組 6 個對齊 GOP 的 MP4 檔案 (範圍從 3400 kbps 到 400 kbps) 和 AAC 5.1 音訊。  
  
 [H264 多重位元速率 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) 會產生一組 6 個對齊 GOP 的 MP4 檔案 (範圍從 3400 kbps 到 400 kbps) 和立體聲 AAC 音訊。  
  
 [H264 單一位元速率 1080p 音訊 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) 會產生位元速率為 6750 kbps 的單一 MP4 檔案，而且是 AAC 5.1 音訊。  
  
 [H264 單一位元速率 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) 會產生位元速率為 6750 kbps 的單一 MP4 檔案，而且是立體聲 AAC 音訊。  
  
 [H264 單一位元速率 4K 音訊 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) 會產生位元速率為 18000 kbps 的單一 MP4 檔案，而且是 AAC 5.1 音訊。  
  
 [H264 單一位元速率 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) 會產生位元速率為 18000 kbps 的單一 MP4 檔案，而且是立體聲 AAC 音訊。  
  
 [H264 單一位元速率 4x3 SD 音訊 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) 會產生位元速率為 1800 kbps 的單一 MP4 檔案，而且是 AAC 5.1 音訊。  
  
 [H264 單一位元速率 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) 會產生位元速率為 1800 kbps 的單一 MP4 檔案，而且是立體聲 AAC 音訊。  
  
 [H264 單一位元速率 16x9 SD 音訊 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) 會產生位元速率為 2200 kbps 的單一 MP4 檔案，而且是 AAC 5.1 音訊。  
  
 [H264 單一位元速率 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) 會產生位元速率為 2200 kbps 的單一 MP4 檔案，而且是立體聲 AAC 音訊。  
  
 [H264 單一位元速率 720p 音訊 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) 會產生位元速率為 4500 kbps 的單一 MP4 檔案，而且是 AAC 5.1 音訊。  
  
 [H264 單一位元速率 720p (適用於 Android)](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) 預設會產生位元速率為 2000 kbps 的單一 MP4 檔案，而且是立體聲 AAC。  
  
 [H264 單一位元速率 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) 會產生位元速率為 4500 kbps 的單一 MP4 檔案，而且是立體聲 AAC 音訊。  
  
 [H264 單一位元速率高品質 SD (適用於 Android)](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) 會產生位元速率為 500 kbps 的單一 MP4 檔案，而且是立體聲 AAC 音訊。  
  
 [H264 單一位元速率低品質 SD (適用於 Android)](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) 會產生位元速率為 56 kbps 的單一 MP4 檔案，而且是立體聲 AAC 音訊。  
  
 如需與媒體服務編碼器相關的詳細資訊，請參閱[使用 Azure 媒體服務隨選編碼](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/)。

