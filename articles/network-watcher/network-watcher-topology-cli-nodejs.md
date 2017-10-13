---
title: "檢視 Azure 網路監看員拓撲 - Azure CLI 1.0 | Microsoft Docs"
description: "本文將描述如何使用 Azure CLI 1.0 來查詢網路拓撲。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bf12e1bde56c06e496d29ad27ba3da65cd94629e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-azure-cli-10"></a>使用 Azure CLI 1.0 檢視網路監看員拓撲

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

網路監看員的拓撲功能可以視覺方式呈現訂用帳戶中的網路資源。 在入口網站中，會自動向您呈現這個視覺效果。 透過 PowerShell 即可擷取入口網站拓撲檢視背後的資訊。
這項功能可讓您活用拓撲資訊，因為資料可供其他工具取用以建立視覺效果。

本文使用跨平台 Azure CLI 1.0，這適用於 Windows、Mac 和 Linux。 

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

在此案例中，您會使用 `network watcher topology` Cmdlet 來擷取拓撲資訊。 另有一篇文章說明如何[使用 REST API 擷取網路拓撲](network-watcher-topology-rest.md)。

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。

## <a name="scenario"></a>案例

本文涵蓋的案例會擷取指定資源群組的拓撲回應。

## <a name="retrieve-topology"></a>擷取拓撲

`network watcher topology` Cmdlet 會擷取指定資源群組的拓撲。 新增引數 "--json" 即可以 json 格式檢視輸出

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>結果

所傳回的結果有屬性名稱「資源」，其中包含 `network watcher topology` Cmdlet 的 json 回應主體。  回應包含網路安全性群組中的資源和其關聯 (也就是包含、相關聯)。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>後續步驟

請瀏覽[安全性群組檢視概觀](network-watcher-security-group-view-overview.md)以深入了解套用到網路資源的安全性規則
