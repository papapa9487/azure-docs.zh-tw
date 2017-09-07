---
title: "使用 Azure 網路監看員管理網路安全性群組流量記錄 - Azure CLI 1.0 | Microsoft Docs"
description: "此頁面說明如何使用 Azure CLI 1.0 在 Azure 網路監看員中管理網路安全性群組流量記錄"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 2ea8543857c062e76f96da99fb295ce831c3c5f2
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017

---


# <a name="configuring-network-security-group-flow-logs-with-azure-cli-10"></a>使用 Azure CLI 1.0 設定網路安全性群組流量記錄

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

網路安全性群組流量記錄是網路監看員的一項功能，可讓您檢視透過網路安全性群組傳輸之輸入和輸出 IP 流量的相關資訊。 這些流量記錄是以 json 格式撰寫，會顯示每一規則的輸出和輸入流量、流量套用至的 NIC、有關流量的 5 個 Tuple 資訊 (來源/目的地 IP、來源/目的地連接埠、通訊協定)，以及流量是被允許或拒絕。

本文使用跨平台 Azure CLI 1.0，這適用於 Windows、Mac 和 Linux。 網路監看員目前使用 Azure CLI 1.0 提供 CLI 支援。

## <a name="register-insights-provider"></a>註冊 Insights 提供者

若要讓流量記錄成功運作，必須註冊 **Microsoft.Insights** 提供者。 如果您不確定是否已註冊 **Microsoft.Insights** 提供者，請執行下列程式碼。

```azurecli
azure provider register --namespace Microsoft.Insights --subscription <subscriptionid>
```

## <a name="enable-network-security-group-flow-logs"></a>啟用網路安全性群組流量記錄

用來啟用流量記錄的命令如下列範例所示︰

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e true
```

## <a name="disable-network-security-group-flow-logs"></a>停用網路安全性群組流量記錄

使用下列範例來停用流量記錄︰

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e false
```

## <a name="download-a-flow-log"></a>下載流量記錄

流量記錄的儲存位置會在建立時定義。 若要存取這些儲存至儲存體帳戶的流量記錄，Microsoft Azure 儲存體總管是很便利的工具，您可以在這裡下載︰http://storageexplorer.com/

如果指定了儲存體帳戶，封包擷取檔案便會儲存到儲存體帳戶的下列位置︰

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId%3D/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.network/networksecuritygroups/{nsgName}/{year}/{month}/{day}/PT1H.json
```

如需記錄結構的相關資訊，請造訪[網路安全性群組流量記錄概觀](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>後續步驟

了解如何[使用 PowerBI 視覺化 NSG 流量記錄](network-watcher-visualize-nsg-flow-logs-power-bi.md)

了解如何[使用開放原始碼工具視覺化 NSG 流量記錄](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

