---
title: "如何調整您的 Azure Time Series Insights 環境規模 | Microsoft Docs"
description: "本教學課程將說明如何調整您的 Azure Time Series Insights 環境規模"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8f6c66ea2173c98179ec899d6626c2ab6f7ec4b6
ms.contentlocale: zh-tw
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-scale-your-time-series-insights-environment"></a>如何調整您的 Time Series Insights 環境規模

本教學課程將說明如何調整您的 Time Series Insights 環境規模。

> [!NOTE]
> 若 SKU 類型不同則無法進行相應增加。 S1 SKU 的環境不能轉換成 S2 環境。

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

## <a name="changing-the-capacity-of-your-environment"></a>變更您的環境容量

1. 在 Azure 入口網站中，選取您想要變更其容量的環境。
1. 在 [設定] 下方按一下 [設定]。
1. 使用 [容量] 滑桿來選取符合您所需輸入速率和儲存容量的容量。

## <a name="next-steps"></a>後續步驟

* 請確定新容量足夠以避免產生節流。 如需詳細資訊，請參閱[這裡](time-series-insights-diagnose-and-solve-problems.md)的*您的環境可能正在進行節流*一節。
