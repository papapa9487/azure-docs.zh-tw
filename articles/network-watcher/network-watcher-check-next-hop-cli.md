---
title: "使用 Azure 網路監看員的「下一個躍點」功能來尋找下一個躍點 - Azure CLI 2.0 | Microsoft Docs"
description: "本文會說明如何使用 Azure CLI，利用下一個躍點功能來得知下一個躍點類型和 IP 位址。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c865793190e6079227ca093a018b0a6f98528d4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-20"></a>使用採用 Azure CLI 2.0 之 Azure 網路監看員的「下一個躍點」功能，得知下一個躍點類型

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

下一個躍點是網路監看員的一項功能，可根據指定的虛擬機器取得下一個躍點類型和 IP 位址。 這項功能可用於判斷離開虛擬機器的流量是否會周遊閘道、網際網路或虛擬網路，以抵達其目的地。

本文使用資源管理部署模型的新一代 CLI：Azure CLI 2.0，它適用於 Windows、Mac 和 Linux。

若要執行本文的步驟，您需要[安裝適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。

## <a name="before-you-begin"></a>開始之前

在此案例中，您會使用 Azure CLI 來尋找下一個躍點類型和 IP 位址。

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。 此案例也假設已有具有有效虛擬機器的資源群組可供使用。

## <a name="scenario"></a>案例

本文涵蓋的案例會使用網路監看員的下一個躍點功能，以找出資源的下一個躍點類型和 IP 位址。 若要深入了解下一個躍點，請造訪[下一個躍點概觀](network-watcher-next-hop-overview.md)。


## <a name="get-next-hop"></a>取得下一個躍點

若要取得下一個躍點，我們可呼叫 `az network watcher show-next-hop` Cmdlet。 我們會將網路監看員資源群組 NetworkWatcher、虛擬機器識別碼、來源 IP 位址和目的地 IP 位址傳遞給此 Cmdlet。 在此範例中，目的地 IP 位址是在另一個虛擬網路的 VM。 兩個虛擬網路之間有虛擬網路閘道。

安裝及設定最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) (若您尚未這麼做)，並使用 [az login](/cli/azure/#login) 來登入 Azure 帳戶。 然後，執行下列命令：

```azurecli
az network watcher show-next-hop --resource-group <resourcegroupName> --vm <vmNameorID> --source-ip <source-ip> --dest-ip <destination-ip>

```

> [!NOTE]
如果 VM 具有多個 NIC，而且任一 NIC 啟用了 IP 轉送，則必須指定 NIC 參數 (-i nic-id)。 否則為選擇性。

## <a name="review-results"></a>檢閱結果

完成時會提供結果。 所傳回的有下一個躍點 IP 位址以及其所屬的資源類型。

```azurecli
{
    "nextHopIpAddress": null,
    "nextHopType": "Internet",
    "routeTableId": "System Route"
}
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
