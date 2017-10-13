---
title: "Azure CLI 指令碼範例 - 刪除 Azure Redis 快取 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 刪除 Azure Redis 快取"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.openlocfilehash: 11d228cc633fee8c6a2984e2eb23690a43d4c0fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="delete-an-azure-redis-cache"></a>刪除 Azure Redis 快取

在此案例中，您會了解如何刪除 Azure Redis 快取。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，刪除 Azure Redis 快取執行個體。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az redis 刪除](https://docs.microsoft.com/cli/azure/redis#az_redis_delete) | 刪除 Redis 快取執行個體。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Redis Cache 文件](../cli-samples.md)中找到其他 Azure Redis Cache CLI 指令碼範例。