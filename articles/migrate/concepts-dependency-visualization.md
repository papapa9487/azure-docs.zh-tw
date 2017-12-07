---
title: "Azure Migrate 的相依性視覺效果 | Microsoft Docs"
description: "概括介紹 Azure Migrate 服務中的評量計算。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 78e52157-edfd-4b09-923f-f0df0880e0e0
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: a8a8cee327dac8adfb0ae53d101c382ef20599d2
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="dependency-visualization"></a>相依性視覺效果

[Azure Migrate](migrate-overview.md) 服務會評估要移轉至 Azure 的內部部署機器群組。 若要將多台機器設定為群組，您可以使用相依性視覺效果。 本文提供此功能的相關資訊。


## <a name="overview"></a>概觀

Azure Migrate 的相依性視覺效果可建立用於移轉評估的群組，藉以提升信心。 您可以使用相依性視覺效果，檢視特定電腦或一整個機器群組的網路相依性。 若有多部電腦在執行應用程式和工作負載，這就相當實用，可確保在移轉流程中不漏失任何功能 (或遺忘機器)。  

## <a name="how-does-it-work"></a>運作方式

為提供相依性的視覺效果，Azure Migrate 採用了 [Log Analytics](../log-analytics/log-analytics-overview.md) 的[服務對應](../operations-management-suite/operations-management-suite-service-map.md)解決方案。
- 您建立 Azure 移轉專案時，會在您的訂用帳戶中建立 OMS Log Analytics 工作區。
- 工作區名稱是您為移轉專案指定的名稱，前面加上 **migrate-**，後面則可選擇要不要加數字。 
- 從專案 [概觀] 頁面的 [基本資訊] 區段中，瀏覽至 Log Analytics 工作區。
- 建立的工作區會以索引鍵 **MigrateProject** 和**專案名稱**的值標記。 您可以使用上述資訊在 Azure 入口網站進行搜尋。  

    ![Log Analytics 工作區](./media/concepts-dependency-visualization/oms-workspace.png)

若要使用相依性視覺效果，您需要在待分析的每個內部部署機器上，下載及安裝代理程式。  

## <a name="do-i-need-to-pay-for-it"></a>我需要支付多少費用？

是。 會預設建立 Log Analytics 工作區，但是，除非您在 Azure Migrate 中使用相依性視覺效果，否則並不會使用到。 如果您使用相依性視覺效果，或使用 Azure Migrate 外部的工作區，您就必須支付工作區使用費用。  [深入了解](https://azure.microsoft.com/pricing/details/insight-analytics/)服務對應解決方案定價。 

## <a name="how-do-i-manage-the-workspace"></a>如何管理工作區？

您可以使用 Azure Migrate 外部的 Log Analytics 工作區。 如果您刪除建立工作區的移轉專案，並不會刪除工作區。 如果不再需要工作區中，請手動[刪除工作區](../log-analytics/log-analytics-manage-access.md)。

除非您刪除移轉專案，否則請勿刪除 Azure Migrate 建立的工作區。 如果您這樣做，相依性將無法如預期般正常運作。

## <a name="next-steps"></a>後續步驟

[使用機器相依性的群組機器](how-to-create-group-machine-dependencies.md)