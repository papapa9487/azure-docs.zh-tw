---
title: "Azure Service Fabric 作業通道 | Microsoft Docs"
description: "完整列出 Azure Service Fabric 叢集的作業通道中所產生的記錄。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: dekapur
ms.openlocfilehash: 934719868ab9968db352db2b440014d35dbc0274
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2017
---
# <a name="operational-channel"></a>作業通道 

作業通道包含 Service Fabric 在您的節點和叢集上所執行的高階動作記錄。 當叢集啟用「診斷」時，Azure 診斷代理程式會部署在您的叢集上，而且預設會設定為從作業通道讀入記錄。 深入了解如何設定 [Azure 診斷代理程式](service-fabric-diagnostics-event-aggregation-wad.md)修改叢集的診斷設定，以選取更多記錄或效能計數器。 

## <a name="operational-channel-logs"></a>作業通道記錄 

以下是 Service Fabric 的作業通道中所提供的完整記錄清單。 

| EventId | 名稱 | 來源 (工作) | 等級 |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 資訊 |
| 25621 | NodeOpenedSuccess | FabricNode | 資訊 |
| 25622 | NodeOpenedFailed | FabricNode | 資訊 |
| 25623 | NodeClosing | FabricNode | 資訊 |
| 25624 | NodeClosed | FabricNode | 資訊 |
| 25625 | NodeAborting | FabricNode | 資訊 |
| 25626 | NodeAborted | FabricNode | 資訊 |
| 29627 | ClusterUpgradeStart | CM | 資訊 |
| 29628 | ClusterUpgradeComplete | CM | 資訊 |
| 29629 | ClusterUpgradeRollback | CM | 資訊 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 資訊 |
| 29631 | ClusterUpgradeDomainComplete | CM | 資訊 |
| 23074 | ContainerActivated | 裝載 | 資訊 |
| 23075 | ContainerDeactivated | 裝載 | 資訊 |
| 29620 | ApplicationCreated | CM | 資訊 |
| 29621 | ApplicationUpgradeStart | CM | 資訊 |
| 29622 | ApplicationUpgradeComplete | CM | 資訊 |
| 29623 | ApplicationUpgradeRollback | CM | 資訊 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 資訊 |
| 29625 | ApplicationDeleted | CM | 資訊 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 資訊 |
| 18566 | ServiceCreated | FM | 資訊 |
| 18567 | ServiceDeleted | FM | 資訊 |

## <a name="next-steps"></a>後續步驟

* 深入了解 Service Fabric 中整個[平台層級的事件產生](service-fabric-diagnostics-event-generation-infra.md)
* 修改您的 [Azure 診斷](service-fabric-diagnostics-event-aggregation-wad.md)設定以收集更多記錄
* [設定 Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) 以查看您的作業通道記錄
