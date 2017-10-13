---
title: "檢視 Azure 網路監看員拓撲 - PowerShell | Microsoft Docs"
description: "本文會說明如何使用 PowerShell 來查詢網路拓撲。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-powershell"></a>使用 PowerShell 檢視網路監看員拓撲

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

網路監看員的拓撲功能可以視覺方式呈現訂用帳戶中的網路資源。 在入口網站中，會自動向您呈現這個視覺效果。 透過 PowerShell 即可擷取入口網站拓撲檢視背後的資訊。
這項功能可讓您活用拓撲資訊，因為資料可供其他工具取用以建立視覺效果。

系統會根據兩項關聯性建立彼此間聯繫關係的模型。

- **內含項目** - 範例︰VNet 包含子網路包含 NIC
- **相關聯** - 範例︰NIC 與 VM 相關聯

下列清單是查詢拓撲 REST API 時所傳回的屬性。

* **名稱** - 資源的名稱
* **識別碼** - 資源的 URI。
* **位置** - 資源所在的位置。
* **關聯** - 與參考物件之關聯的清單。
    * **名稱** - 所參考資源的名稱。
    * **resourceId** - resourceId 是關聯中所參考資源的 URI。
    * **associationType** - 這個值會參考子物件與父系之間的關聯性。 有效值為 [包含] 或 [相關聯]。

## <a name="before-you-begin"></a>開始之前

在此案例中，您會使用 `Get-AzureRmNetworkWatcherTopology` Cmdlet 來擷取拓撲資訊。 另有一篇文章說明如何[使用 REST API 擷取網路拓撲](network-watcher-topology-rest.md)。

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。

## <a name="scenario"></a>案例

本文涵蓋的案例會擷取指定資源群組的拓撲回應。

## <a name="retrieve-network-watcher"></a>擷取網路監看員

第一步是擷取網路監看員執行個體。 `$networkWatcher` 變數會傳遞至 `Get-AzureRmNetworkWatcherTopology` Cmdlet。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>擷取拓撲

`Get-AzureRmNetworkWatcherTopology` Cmdlet 會擷取指定資源群組的拓撲。

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>結果

所傳回的結果有屬性名稱「資源」，其中包含 `Get-AzureRmNetworkWatcherTopology` Cmdlet 的 json 回應主體。  回應包含網路安全性群組中的資源和其關聯 (也就是包含、相關聯)。

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>後續步驟

若要了解如何利用 Power BI 將 NSG 流量記錄視覺化，請瀏覽[利用 Power BI 將 NSG 流量記錄視覺](network-watcher-visualize-nsg-flow-logs-power-bi.md)


