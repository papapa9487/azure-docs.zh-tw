---
title: "監視適用於 PostgreSQL 的 Azure 資料庫 | Microsoft Docs"
description: "本文說明適用於 PostgreSQL 的 Azure 資料庫之監視和警示的計量，包括 CPU、限制、儲存和連線統計資料。"
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: d48159fbc5e52bf1916a744e3912ca7ceb0ab60f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-postgresql"></a>監視適用於 PostgreSQL 的 Azure 資料庫
監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 適用於 PostgreSQL 的 Azure 資料庫提供多種計量，可讓您深入了解支援 PostgreSQL 伺服器資源的行為。 

## <a name="metrics"></a>度量
所有 Azure 計量都有一分鐘頻率，且每個計量皆提供 30 天的記錄。 

您可以在計量上設定警示。 如需逐步指引，請參閱[如何設定警示](howto-alert-on-metric.md)。 

其他工作包含設定自動化動作、執行進階分析，以及封存記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>計量清單
這些計量可供適用於 PostgreSQL 的 Azure 資料庫使用：

|計量|計量顯示名稱|單位|說明|
|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用中的 CPU 百分比。|
|compute_limit|計算單位限制|Count|此伺服器的計算單位數上限|
|compute_consumption_percent|計算單位百分比|百分比|使用的計算單位佔伺服器最大值的百分比。|
|memory_percent|記憶體百分比|百分比|使用中記憶體的百分比。|
|io_consumption_percent|IO 百分比|百分比|使用中 IO 的百分比。|
|storage_percent|儲存體百分比|百分比|使用的儲存體佔伺服器最大值的百分比。|
|storage_used|已使用儲存體|位元組|使用中的儲存體數量。 此服務所使用的儲存體包括資料庫檔案、交易記錄和伺服器記錄檔。|
|storage_limit|儲存體限制|位元組|此伺服器的儲存體上限。|
|active_connections|作用中的連接總數|Count|伺服器的使用中連線數量。|
|connections_failed|失敗的連接總數|Count|伺服器的失敗連線數量。|


> [!NOTE]
> 計算單位包含記憶體和 CPU。 計算單位百分比為 max(memory%, cpu%)。 檢查記憶體和 cpu 圖表以指出計算單位百分比變更的最大原因。 如需詳細資訊，請參閱[計算單位](concepts-compute-unit-and-storage.md)。

## <a name="next-steps"></a>後續步驟
- 如需逐步指引，請參閱[如何設定警示](howto-alert-on-metric.md)。 
- 如需如何使用 Azure 入口網站、REST API 或 CLI 存取及匯出計量的詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
