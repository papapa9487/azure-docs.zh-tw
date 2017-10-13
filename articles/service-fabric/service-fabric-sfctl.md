---
title: Azure Service Fabric CLI- sfctl | Microsoft Docs
description: "描述 Service Fabric CLI sfctl 命令。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 2dd30470ee0f6c038a8601bfca73fc97091de2fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl"></a>sfctl 
用於管理 Service Fabric 叢集和實體的命令。 這個版本與 Service Fabric 6.0 執行階段相容。 命令依循名詞動詞模式，如需詳細資訊，請參閱下列子群組。

## <a name="subgroups"></a>子群組
|子群組|說明|
| --- | --- |
| [application](service-fabric-sfctl-application.md)| 建立、刪除與管理應用程式和應用程式類型。|
| [chaos](service-fabric-sfctl-chaos.md)   | 啟動、停止及報告 chaos 測試服務。|
| [cluster](service-fabric-sfctl-cluster.md) | 選取、管理和操作 Service Fabric 叢集。|
| [compose](service-fabric-sfctl-compose.md) | 建立、刪除和管理 Docker Compose 應用程式。|
| [is](service-fabric-sfctl-is.md)      | 查詢命令，並將其傳送至基礎結構服務。|
| [node](service-fabric-sfctl-node.md)    | 管理形成叢集的節點。|
| [partition](service-fabric-sfctl-partition.md)  | 查詢和管理任何服務的資料分割。|
| [rpm](service-fabric-sfctl-rpm.md)        | 查詢命令，並將其傳送至修復管理員服務。|
| [replica](service-fabric-sfctl-replica.md) | 管理屬於服務資料分割的複本。|
| [service](service-fabric-sfctl-service.md) | 建立、刪除與管理服務、服務類型和服務套件。|
| [store](service-fabric-sfctl-store.md)   | 在叢集映像存放區上執行基本檔案層級作業。|

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。