---
title: "Azure CLI 指令碼範例 - 從叢集移除應用程式"
description: "Azure CLI 指令碼範例 - 從 Service Fabric 叢集移除應用程式。"
services: service-fabric
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 77fa78199854d10c4897d56da12c8e359ae491f3
ms.contentlocale: zh-tw
ms.lasthandoff: 07/25/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>從 Service Fabric 叢集移除應用程式

這個範例指令碼會刪除執行中的 Service Fabric 應用程式執行個體，並從叢集取消註冊應用程式類型和版本。  刪除應用程式執行個體也會刪除應用程式相關聯的所有執行中服務執行個體。 接下來，系統會從映像存放區刪除應用程式檔案。 

視需要安裝 [Azure CLI](../service-fabric-azure-cli-2-0.md)。

## <a name="sample-script"></a>範例指令碼

[!code-sh[主要](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "從叢集移除應用程式")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | 選取要使用的叢集。 |
| [sf application delete](/cli/azure/sf/application#delete) | 從叢集刪除應用程式執行個體。 |
| [sf application unprovision](/cli/azure/sf/application#unprovision) | 從叢集取消註冊 Service Fabric 應用程式類型和版本。|
| [sf application package-delete](/cli/azure/sf/application#package-delete) | 從映像存放區移除 Service Fabric 應用程式封裝。 |

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure CLI 文件](../service-fabric-azure-cli-2-0.md)。

您可以在 [Azure CLI 範例](../samples-cli.md)中找到適用於 Azure Service Fabric 的其他 Azure CLI 範例。

