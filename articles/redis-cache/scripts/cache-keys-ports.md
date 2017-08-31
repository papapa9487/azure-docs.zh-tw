---
title: "Azure CLI 指令碼範例 - 取得 Azure Redis Cache 的主機名稱、連接埠和金鑰 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 取得 Azure Redis Cache 執行個體的主機名稱、連接埠和金鑰"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: cd9adc784bceb0fff5e7c2bbee2be0950c51c8f6
ms.contentlocale: zh-tw
ms.lasthandoff: 04/15/2017

---

# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>取得 Azure Redis Cache 的主機名稱、 連接埠和金鑰

在此案例中，您會了解如何擷取主機名稱、連接埠和金鑰，以用於連接到 Azure Redis Cache 執行個體。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis 快取")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令擷取 Azure Redis Cache 執行個體的主機名稱、金鑰和連接埠。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#show) | 取得 Azure Redis Cache 執行個體的詳細資料。 |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#list-keys) | 取得 Azure Redis Cache 執行個體的存取金鑰。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Redis Cache 文件](../cli-samples.md)中找到其他 Azure Redis Cache CLI 指令碼範例。
