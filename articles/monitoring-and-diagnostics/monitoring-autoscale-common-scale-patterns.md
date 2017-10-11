---
title: "常見的自動調整規模模式概觀 | Microsoft Docs"
description: "了解一些在 Azure 中自動調整資源規模的常見模式。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.openlocfilehash: fce51546e041c8989d813c3935e058c52b38ba77
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="overview-of-common-autoscale-patterns"></a>常見的自動調整規模模式概觀
本文說明一些在 Azure 中調整資源規模的常見模式。

Azure 監視器自動調整規模僅適用於虛擬機器擴展集 (VMSS)、雲端服務、App Service 方案及 App Service 環境。 

# <a name="lets-get-started"></a>開始使用

本文假設您已熟悉如何使用自動調整規模。 您可以[從這裡開始調整資源規模][1]。 下列是一些常見的調整規模模式。

## <a name="scale-based-on-cpu"></a>根據 CPU 調整規模

您具有 Web 應用程式 (/VMSS/雲端服務角色)，而且 

- 您想要根據 CPU 進行相應放大/縮小。
- 此外，您想要確定執行個體數目會有下限。 
- 另外還想要確定，您會將上限設定為您可調整到的執行個體數目。

![根據 CPU 調整規模][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>在工作日與週末以不同方式調整規模

您具有 Web 應用程式 (/VMSS/雲端服務角色)，而且

- 您預設想要 3 個執行個體 (在工作日)
- 您不預期週末會有流量，因此想要在週末相應減少為 1 個執行個體。

![在工作日與週末以不同方式調整規模][3]

## <a name="scale-differently-during-holidays"></a>在假日期間以不同方式調整規模

您具有 Web 應用程式 (/VMSS/雲端服務角色)，而且 

- 您預設想要根據 CPU 使用量相應增加/減少
- 不過，在假日期間 (或對貴企業很重要的特定天數)，您會想要覆寫預設值，以便有更多可供使用的容量。

![在假日期間以不同方式調整規模][4]

## <a name="scale-based-on-custom-metric"></a>根據自訂計量調整規模

您具有一個 Web 前端以及可與後端通訊的 API 層。 

- 您想要根據前端的自訂事件來調整 API 層的規模 (例如：您想要根據購物車中的項目數來調整結帳程序)

![根據自訂計量調整規模][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png