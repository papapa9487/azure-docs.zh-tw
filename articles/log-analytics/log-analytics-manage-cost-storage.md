---
title: "管理 Azure Log Analytics 中的資料保留成本 | Microsoft Docs"
description: "了解如何在 Azure 入口網站中變更您 Log Analytics 工作區的定價方案和資料保留期原則。"
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 35064c792b72222d59b1d3f0913a92a4a2b34612
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>管理您 Log Analytics 工作區的資料保留成本
當您註冊 Log Analytics 時，視所選取的方案而定，會限制您將連線來源所產生的資料儲存在工作區中的時間長短。  本文將重點放在 可對此資料的不同保留期成本產生影響的考量，以及如何設定該限制。   

由於 Log Analytics 可能從內部部署、雲端及混合式環境中的來源取用大量資料，因此視下列因素而定，該資料的儲存期成本可能會相當可觀：

* 您要從中收集資料的系統、基礎結構元件、雲端資源等的數目
* 來源所建立的資料類型 (例如訊息佇列、記錄、事件、安全性相關資料，或效能計量)
* 這些來源所產生的資料磁碟區 
* 已啟用的管理解決方案數目、資料來源及收集頻率

> [!NOTE]
> 請參閱每個解決方案的文件，因為它會提供所收集資料數量的預估值。   

如果您使用的是 [免費] 方案，則資料的保留期僅限 7 天。  如果是 [獨立] 或 [付費] 層，則可提供過去 31 天收集的資料。  

使用 [付費] 方案時，如果發現一直超出所允許的數量，您可以將工作區變更成付費方案，以收集超出此限制的資料。 

> [!NOTE]
> 如果您選擇為付費層選取一個較長的保留期，就需要支付費用。 您可以隨時變更方案類型，如需有關定價的詳細資訊，請參閱[定價詳細資料](https://azure.microsoft.com/pricing/details/log-analytics/)。 

## <a name="change-the-data-retention-period"></a>變更資料保留期 

1. 登入 [Azure 入口網站](http://portal.azure.com)。 
2. 按一下左下角的 [更多服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。
3. 在 Log Analytics 訂用帳戶窗格中，從清單中選取要修改的工作區。
4. 在 [工作區] 頁面上，按一下左側窗格中的 [保留期]。
5. 在工作區保留期窗格上，移動滑桿來增加或減少天數，然後按一下 [儲存]。  如果您位於「免費」層，將無法修改資料保留期，必須升級至付費層，才能控制此設定。<br><br> ![變更工作區資料保留期設定](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>後續步驟  

若要判斷收集的資料有多少、哪些來源傳送這些資料及所傳送資料的不同類型，以協助管理取用和成本，請參閱[在 Log Analytics 中分析資料使用量](log-analytics-usage.md)