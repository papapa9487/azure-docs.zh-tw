---
title: "使用 Azure 網路監看員下一個躍點來尋找下一個躍點 - PowerShell | Microsoft Docs"
description: "本文會說明如何使用 PowerShell，利用下一個躍點功能來得知下一個躍點類型和 IP 位址。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>使用 PowerShell，利用 Azure 網路監看員的下一個躍點功能找出下一個躍點類型

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

下一個躍點是網路監看員的一項功能，可根據指定的虛擬機器取得下一個躍點類型和 IP 位址。 這項功能可用於判斷離開虛擬機器的流量是否會周遊閘道、網際網路或虛擬網路，以抵達其目的地。

## <a name="before-you-begin"></a>開始之前

在此案例中，您會使用 Azure 入口網站來尋找下一個躍點類型和 IP 位址。

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。 此案例也假設已有具有有效虛擬機器的資源群組可供使用。

## <a name="scenario"></a>案例

本文涵蓋的案例會使用網路監看員的下一個躍點功能，以找出資源的下一個躍點類型和 IP 位址。 若要深入了解下一個躍點，請造訪[下一個躍點概觀](network-watcher-next-hop-overview.md)。

## <a name="retrieve-network-watcher"></a>擷取網路監看員

第一步是擷取網路監看員執行個體。 `$networkWatcher` 變數會傳遞至下一個躍點驗證 cmdlet。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>取得虛擬機器

下一個躍點會從虛擬機器傳回下一個躍點和下一個躍點的 IP 位址。 Cmdlet 需要虛擬機器的識別碼。 如果您已經知道要使用的虛擬機器識別碼，您可以略過此步驟。

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> 下一個躍點需要配置 VM 資源以供執行。

## <a name="get-the-network-interfaces"></a>取得網路介面

您需要虛擬機器上 NIC 的 IP 位址，在此範例中，我們會擷取虛擬機器上的 NIC。 如果您已經知道您想要在虛擬機器上測試的 IP 位址，您可以略過此步驟。

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>取得下一個躍點

現在我們呼叫 `Get-AzureRmNetworkWatcherNextHop` cmdlet。 我們會將網路監看員、虛擬機器識別碼、來源 IP 位址和目的地 IP 位址傳遞給此 Cmdlet。 在此範例中，目的地 IP 位址是在另一個虛擬網路的 VM。 兩個虛擬網路之間有虛擬網路閘道。

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>檢閱結果

完成時會提供結果。 所傳回的有下一個躍點 IP 位址以及其所屬的資源類型。 在此案例中，它是虛擬網路閘道的公用 IP 位址。

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

下列清單顯示目前可用的 NextHopType 值︰

**下一個躍點類型**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

## <a name="next-steps"></a>後續步驟

請造訪[使用網路監看員稽核 NSG](network-watcher-nsg-auditing-powershell.md)，以了解如何以程式設計方式檢閱網路安全性群組設定

















