---
title: "使用 Azure 網路監看員 IP 流程驗證來驗證流量 - REST | Microsoft Docs"
description: "本文說明如何檢查虛擬機器中的流入或流出流量是被允許或拒絕"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3ccef9ef521b86ffc1eb6047174f4f9e5d9e4296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>使用 Azure 網路監看員的 IP 流程驗證元件來檢查流量是被允許或拒絕

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


IP 流量驗證是網路監看員的一項功能，可讓您驗證虛擬機器中的流入或流出流量是否被允許。 可執行傳入或傳出流量的驗證。 此案例可用於取得虛擬機器的目前狀態，以了解其是否可以和外部資源或後端通訊。 IP 流量驗證可用來驗證是否已正確設定網路安全性群組 (NSG) 規則，並針對 NSG 規則所封鎖的流量進行疑難排解。 使用 IP 流量驗證的另一個原因是可以確保 NSG 會正確封鎖您想要封鎖的流量。

## <a name="before-you-begin"></a>開始之前

使用 ARMclient 透過 PowerShell 呼叫 REST API。 您可以在 chocolatey 的 [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient) 上找到 ARMClient

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。

## <a name="scenario"></a>案例

此案例使用 IP 流程驗證來確認虛擬機器是否可以透過連接埠 443 與其他機器進行通訊。 如果流量被拒絕，它會傳回拒絕該流量的安全性規則。 若要深入了解 IP 流程驗證，請瀏覽 [IP 流程驗證概觀 (英文)](network-watcher-ip-flow-verify-overview.md)

在此案例中，您將會：

* 擷取虛擬機器
* 呼叫 IP 流程驗證
* 驗證結果

## <a name="log-in-with-armclient"></a>使用 ARMClient 登入

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>擷取虛擬機器

執行下列指令碼，以傳回虛擬機器。 下列程式碼需要變數值︰

* **subscriptionId** - 要使用的訂用帳戶識別碼。
* **resourceGroupName** - 包含虛擬機器的資源群組名稱。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

所需資訊是類型 `Microsoft.Compute/virtualMachines` 下的識別碼。 結果應該會與下列程式碼範例類似：

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>呼叫 IP 流程驗證

下列範例會建立要求來驗證指定虛擬機器的流量。 回應會傳回流量是被允許或拒絕。 如果流量被拒絕，它也會傳回封鎖該流量的規則。

> [!NOTE]
> IP 流量驗證需要配置 VM 資源。

指令碼需要虛擬機器的資源識別碼和虛擬機器上網路介面的資源識別碼。 上述輸出會提供這些值。

> [!Important]
> 針對所有網路監看員 REST 呼叫，要求 URI 中的資源群組名稱是包含網路監看員執行個體的那一個，而非要對其執行診斷動作的資源。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>了解結果

您收到的回應會告訴您流量是被允許或拒絕。 回應看起來就像下列其中一個範例︰

**允許**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**拒絕**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>後續步驟

如果流量遭到封鎖，但不應如此，請參閱[管理網路安全性群組](../virtual-network/virtual-network-manage-nsg-arm-portal.md)以深入了解網路安全性群組。












