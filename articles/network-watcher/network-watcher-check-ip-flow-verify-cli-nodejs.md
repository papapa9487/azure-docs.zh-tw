---
title: "使用 Azure 網路監看員 IP 流量驗證來驗證流量 - Azure CLI | Microsoft Docs"
description: "本文說明如何使用 Azure CLI 檢查虛擬機器中的流入或流出流量是被允許或拒絕"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f1355cd861722848211277250155c434da1e774d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>使用 Azure 網路監看員的 IP 流量驗證元件來檢查 VM 中的流入或流出流量是被允許或拒絕

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


IP 流量驗證是網路監看員的一項功能，可讓您驗證虛擬機器中的流入或流出流量是否被允許。 此案例可用於取得虛擬機器的目前狀態，以了解其是否可以和外部資源或後端通訊。 IP 流量驗證可用來驗證是否已正確設定網路安全性群組 (NSG) 規則，並針對 NSG 規則所封鎖的流量進行疑難排解。 使用 IP 流量驗證的另一個原因是要確保您想要封鎖的流量會被 NSG 正確封鎖。

本文使用跨平台 Azure CLI 1.0，這適用於 Windows、Mac 和 Linux。

## <a name="before-you-begin"></a>開始之前

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員，或您擁有現有的網路監看員執行個體。 此案例也假設已有具有有效虛擬機器的資源群組可供使用。

## <a name="scenario"></a>案例

此案例使用 IP 流量驗證來驗證虛擬機器是否可以和已知的 Bing IP 位址通訊。 如果流量被拒絕，它會傳回拒絕該流量的安全性規則。 若要深入了解 IP 流量驗證，請造訪 [IP 流量驗證概觀](network-watcher-ip-flow-verify-overview.md)


## <a name="get-a-vm"></a>取得 VM

IP 流量驗證會測試虛擬機器上的 IP 位址流向遠端目的地的流量，反之亦然。 Cmdlet 需要虛擬機器的識別碼。 如果您已經知道要使用的虛擬機器識別碼，您可以略過此步驟。

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>取得 NICS

您需要虛擬機器上 NIC 的 IP 位址，在此範例中，我們會擷取虛擬機器上的 NIC。 如果您已經知道您想要在虛擬機器上測試的 IP 位址，您可以略過此步驟。

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>執行 IP 流量驗證

既然我們已經擁有了執行 Cmdlet 所需的資訊，我們可以執行 `network watcher ip-flow-verify` Cmdlet 來測試流量。 在此範例中，我們會使用第一個 NIC 上的第一個 IP 位址。

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> IP 流量驗證需要配置 VM 資源以供執行。

## <a name="review-results"></a>檢閱結果

`network watcher ip-flow-verify` 執行後會傳回結果，下列範例就是上一個步驟所傳回的結果。

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>後續步驟

如果流量遭到封鎖，但不應如此，請參閱[管理網路安全性群組](../virtual-network/virtual-network-manage-nsg-arm-portal.md)以追蹤網路安全性群組和所定義的安全性規則。

請造訪[使用網路監看員稽核網路安全性群組 (NSG)](network-watcher-nsg-auditing-powershell.md) 以了解如何稽核 NSG 設定。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
