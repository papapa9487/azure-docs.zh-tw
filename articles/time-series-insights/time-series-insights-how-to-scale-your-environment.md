---
title: "如何調整您的 Azure Time Series Insights 環境規模 | Microsoft Docs"
description: "本文說明如何調整您的 Azure Time Series Insights 環境規模。 使用 Azure 入口網站在價格 SKU 內增加或減少容量。"
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: a6f10e14e3f9e5761734738caddc247d9e4a90cd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>如何調整您的 Time Series Insights 環境規模

本文說明如何使用 Azure 入口網站變更時間序列深入解析環境的容量。 容量是套用至輸入速率、儲存體容量以及與選取之 SKU 相關聯成本的乘數。 

您可以使用 Azure 入口網站在給定的價格 SKU 內增加或減少容量。 

不過，不允許變更定價層 SKU。 例如，擁有 S1 價格 SKU 的環境不能轉換成 S2，反之亦然。 


## <a name="s1-sku-ingress-rates-and-capacities"></a>S1 SKU 輸入速率和容量

| S1 SKU 容量 | 輸入速率 | 儲存體容量上限
| --- | --- | --- |
| 1 | 1 GB (1 百萬個事件) | 一個月 30 GB (3 千萬個事件) |
| 10 | 10 GB (1 千萬個事件) | 一個月 300 GB (3 億個事件) |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU 輸入速率和容量

| S2 SKU 容量 | 輸入速率 | 儲存體容量上限
| --- | --- | --- |
| 1 | 10 GB (1 千萬個事件) | 一個月 300 GB (3 億個事件) |
| 10 | 100 GB (1 億個事件) | 一個月 3 TB (30 億個事件) |

容量是以線性方式擴充或減少，因此容量 2 的 S1 SKU 支援的輸入速率為一天 2 GB (2 百萬) 的事件，和 一個月 60 GB (6 千萬個事件)。

## <a name="change-the-capacity-of-your-environment"></a>變更您的環境容量
1. 在 Azure 入口網站中，找到並選取您的時間序列深入解析環境。 

2. 在 [設定] 標題之下，選取 [設定]。

   ![configure.png](media/scale-your-environment/configure.png)

3. 調整 [容量] 滑桿來選取符合您所需輸入速率和儲存容量的容量。 請注意，輸入速率、儲存容量和估計成本會動態更新，以顯示變更的影響。 

   ![滑桿](media/scale-your-environment/slider.png)

   或者，您可以在滑桿右邊的文字方塊中，輸入容量乘數的數值。 

4. 選取 [儲存] 來調整環境。 認可變更之後，系統就會立即顯示進度列指示器。 

## <a name="next-steps"></a>後續步驟
請確定新容量足夠以避免產生節流。 如需詳細資訊，請參閱[如果您的環境受到節流](time-series-insights-diagnose-and-solve-problems.md)。
