---
title: "Azure CLI 指令碼範例 - 建立 Azure Redis 快取 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立 Azure Redis 快取"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: afd7f6e0-9297-4c98-a95e-597be939cef7
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 75b0602a6c6a0e252ae0bb5eab41ef5cc5636dc5
ms.contentlocale: zh-tw
ms.lasthandoff: 04/07/2017

---

# <a name="create-an-azure-redis-cache"></a>建立 Azure Redis 快取

在此案例中，您會了解如何建立 Azure Redis 快取。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Redis 快取")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組與 redis 快取。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#create) | 建立 Redis 快取執行個體。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Redis 快取文件](../cli-samples.md)中找到其他 Azure Redis 快取 CLI 指令碼範例。
