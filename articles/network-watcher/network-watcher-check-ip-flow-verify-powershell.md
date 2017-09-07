---
title: "使用 Azure 網路監看員 IP 流程驗證來驗證流量 - PowerShell | Microsoft Docs"
description: "本文說明如何使用 PowerShell 檢查虛擬機器中的流入或流出流量是被允許或拒絕"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: bf0c01a9af0e28647d11ad89a9d164716d5c8312
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017

---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>使用 Azure 網路監看員的 IP 流程驗證元件來檢查流入或流出 VM 的流量是被允許或拒絕

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


IP 流量驗證是網路監看員的一項功能，可讓您驗證虛擬機器中的流入或流出流量是否被允許。 此案例可用於取得虛擬機器的目前狀態，以了解其是否可以和外部資源或後端通訊。 IP 流量驗證可用來驗證是否已正確設定網路安全性群組 (NSG) 規則，並針對 NSG 規則所封鎖的流量進行疑難排解。 使用 IP 流量驗證的另一個原因是可以確保 NSG 會正確封鎖您想要封鎖的流量。

## <a name="before-you-begin"></a>開始之前

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員，或您擁有現有的網路監看員執行個體。 此案例也假設已有具有有效虛擬機器的資源群組可供使用。

## <a name="scenario"></a>案例

此案例使用 IP 流程驗證來確認虛擬機器是否可以與已知的 Bing IP 位址進行通訊。 如果流量被拒絕，它會傳回拒絕該流量的安全性規則。 若要深入了解 IP 流程驗證，請瀏覽 [IP 流程驗證概觀 (英文)](network-watcher-ip-flow-verify-overview.md)

## <a name="retrieve-network-watcher"></a>擷取網路監看員

第一步是擷取網路監看員執行個體。 `$networkWatcher` 變數會傳遞至 IP 流量驗證 Cmdlet。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>取得 VM

IP 流量驗證會測試虛擬機器上的 IP 位址流向遠端目的地的流量，反之亦然。 Cmdlet 需要虛擬機器的識別碼。 如果您已經知道要使用的虛擬機器識別碼，您可以略過此步驟。

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>取得 NICS

您需要虛擬機器上 NIC 的 IP 位址，在此範例中，我們會擷取虛擬機器上的 NIC。 如果您已經知道您想要在虛擬機器上測試的 IP 位址，您可以略過此步驟。

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>執行 IP 流量驗證

既然我們已經擁有了執行 Cmdlet 所需的資訊，我們可以執行 `Test-AzureRmNetworkWatcherIPFlow` Cmdlet 來測試流量。 在此範例中，我們會使用第一個 NIC 上的第一個 IP 位址。

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> IP 流程驗證需要配置 VM 資源才能執行。

## <a name="review-results"></a>檢閱結果

`Test-AzureRmNetworkWatcherIPFlow` 執行後會傳回結果，下列範例就是上一個步驟所傳回的結果。

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>後續步驟

如果流量遭到封鎖，但不應如此，請參閱[管理網路安全性群組](../virtual-network/virtual-network-manage-nsg-arm-portal.md)以追蹤網路安全性群組和所定義的安全性規則。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png














