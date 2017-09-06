---
title: "Azure 事件中樞專用容量概觀 | Microsoft Docs"
description: "Microsoft Azure 事件中樞專用容量概觀。"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sethm;babanisa
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: db8b119178de0e565b2064e9a52d5e9989d60d38
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---

# <a name="overview-of-event-hubs-dedicated"></a>事件中樞專用的概觀

「事件中樞專用」容量可為需求最嚴苛的客戶提供單一租用戶部署。 在完整規模 Azure 事件中樞可以每秒輸入超過 2 百萬個事件，或使用完全耐久性儲存體遙測，每秒可高達 2 GB 且延遲不到一秒。 這也可在相同系統上，啟用能即時和批次處理的整合式解決方案。 透過供應項目中包含的[事件中樞擷取](event-hubs-capture-overview.md)，您可讓單一資料流同時支援即時和批次型管線，以降低解決方案的複雜度。

下表比較事件中樞可用的服務層。 相較於大部分標準功能的使用量定價，事件中樞專用供應項目的每月費用固定。 專用層提供標準方案的功能，但可對工作負載需求高的客戶提供企業規模容量。 

| 功能 | 標準 | 專用 |
| --- |:---:|:---:|:---:|
| 輸入事件 | 按百萬個事件付費 | 已包括 |
| 輸送量單位 (每秒 1 MB 輸入，每秒 2 MB 輸出) | 按小時付費 | 已包括 |
| 訊息大小 | 256 KB | 1 MB |
| 發行者原則 | 是 | 是 |   
| 用戶群組 | 20 | 20 |
| 訊息重播 | 是 | 是 |
| 最大輸送量單位 | 20 (可通融至 100)   | 1 CU≈200 |
| 代理連線 | 包含 1,000 個 | 包含 10 萬個 |
| 其他代理連線 | 是 | 是 |
| 訊息保留期 | 含 1 天 | 最多含 7 天 |
| 擷取 | 按小時付費 | 已包括 |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>事件中樞專用容量的優點

使用事件中樞專用時，有以下的優點：

* 使用單一租用戶裝載，不會受到其他租用戶的干擾。
* 相較於標準的 256 KB，訊息大小增加至 1 MB。
* 可重複每次效能。
* 容量保證符合您暴增的需求。
* 可在 1 與 8 容量單位 (CU) 之間調整，每秒最多可提供 2 百萬個輸入事件。
  * CU 可管理事件中樞專用的規模，每個 CU 可提供大約相當於 200 個輸送量單位 (TU)。
* 零維護：負載平衡、作業系統更新、安全性修補程式及資料分割都由我們管理。
* 每月價格固定。

事件中樞專用也會移除一些標準供應項目的輸送量限制。 標準層的輸送量單位可讓您每個 TU 的輸入為每秒 1000 個事件或每秒 1 MB，而輸出則為該數量的兩倍。 專用規模的供應項目的輸入和輸出事件計數沒有限制。 這些限制僅取決於所購買事件中樞的處理能力。

這項服務以最大型遙測使用者為目標，同時提供給企業合約客戶。

## <a name="how-to-onboard"></a>如何加入

事件中樞專用平台是透過企業合約，以不同的 CU 大小來提供。 每個 CU 可提供大約相當於 200 個輸送量單位。 您可以根據需求新增或移除 CU 於當月上下調整容量。 專用方案的獨特之處在於可從事件中樞產品小組獲得更為實際的加入體驗，以最適合您的方式彈性部署。 

## <a name="next-steps"></a>後續步驟
請連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。 您也可以造訪下列連結以深入了解事件中樞︰

如需價格的詳細資訊，請造訪下列連結：

- [事件中樞專用價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 您也可以連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。
- [事件中樞常見問題集](event-hubs-faq.md)包含價格資訊，並提供一些常見問題的解答。 

