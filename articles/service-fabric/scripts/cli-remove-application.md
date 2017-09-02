---
title: "Azure Service Fabric CLI 指令碼移除範例"
description: "使用 Azure Service Fabric CLI 將應用程式從 Azure Service Fabric 叢集中移除"
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
ms.topic: sample
ms.date: 08/22/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 6ea52da6698647bb21d8f4c9a47a73bd5eef2a29
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>從 Service Fabric 叢集移除應用程式

這個範例指令碼會刪除執行中的 Service Fabric 應用程式執行個體，並從叢集取消註冊應用程式類型和版本。  刪除應用程式執行個體也會刪除應用程式相關聯的所有執行中服務執行個體。 接下來，系統會從映像存放區刪除應用程式檔案。 

如有需要，請安裝[ Service Fabric SDK](../service-fabric-cli.md)。

## <a name="sample-script"></a>範例指令碼

[!code-sh[主要](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "從叢集移除應用程式")]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Service Fabric CLI 文件](../service-fabric-cli.md)。

您可以在 [Service Fabric CLI 範例](../samples-cli.md)中找到適用於 Azure Service Fabric 的其他 Service Fabric CLI 範例。

