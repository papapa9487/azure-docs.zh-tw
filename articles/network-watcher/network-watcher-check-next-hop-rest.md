---
title: "使用 Azure 網路監看員下一個躍點來尋找下一個躍點 - REST | Microsoft Docs"
description: "本文會說明如何使用 Azure REST API，利用下一個躍點功能來得知下一個躍點類型和 IP 位址"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 644713d365191bf5e51517d0cc565efbc2abc144
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017

---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>使用 Azure REST API，利用 Azure 網路監看員的下一個躍點功能找出下一個躍點類型

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

下一個躍點是網路監看員的一項功能，可根據指定的虛擬機器取得下一個躍點類型和 IP 位址。 這項功能可用於判斷離開虛擬機器的流量是否會周遊閘道、網際網路或虛擬網路，以抵達其目的地。

## <a name="before-you-begin"></a>開始之前

使用 ARMclient 透過 PowerShell 呼叫 REST API。 您可以在 chocolatey 的 [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient) 上找到 ARMClient

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。

## <a name="scenario"></a>案例

本文涵蓋的案例會使用網路監看員的下一個躍點功能，以找出資源的下一個躍點類型和 IP 位址。 若要深入了解下一個躍點，請造訪[下一個躍點概觀](network-watcher-next-hop-overview.md)。

在此案例中，您將會：

* 擷取虛擬機器的下一個躍點。

## <a name="log-in-with-armclient"></a>使用 ARMClient 登入

使用 Azure 認證登入 Armclient。

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>擷取虛擬機器

執行下列指令碼，以傳回虛擬機器。 執行下一個躍點需要這項資訊。

下列程式碼需要下列變數的值︰

- **subscriptionId** - 要使用的訂用帳戶識別碼。
- **resourceGroupName** - 包含虛擬機器的資源群組名稱。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

從下列的輸出，會在下列範例中使用虛擬機器的識別碼︰

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>取得下一個躍點

一旦建立授權標頭後，就可以從虛擬機器擷取下一個躍點。 必須取代下列程式碼範例的值才可運作。

> [!Important]
> 針對網路監看員 REST API 呼叫，要求 URI 中的資源群組名稱是包含網路監看員的資源群組，而非您要執行診斷動作的資源。

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> 下一個躍點需要配置 VM 資源以供執行。

## <a name="results"></a>結果

以下程式碼片段是所接收輸出的範例。 結果包含下列值：

* **nextHopType** - 這個值可以是下列值之一︰Internet、VirtualAppliance、VirtualNetworkGateway、VnetLocal、HyperNetGateway 或 None。
* **nextHopIpAddress** - 下一個躍點的 IP 位址。
* **routeTableId** - 值是與路由相關聯的路由資料表的 URI，或者如果沒有定義使用者定義的路由，會傳回系統路由的值。

以下是 json 格式的結果。

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>後續步驟

一旦您已能夠找出虛擬機器的下一個躍點，就可以檢視您網路資源的安全性，請造訪[安全性檢視概觀](network-watcher-security-group-view-overview.md)















