---
title: "了解 Azure 中的輸出連線 | Microsoft Docs"
description: "本文說明如何讓 Azure VM 與公用網際網路服務進行通訊。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 03cb14b5710b6dd17599a3c4eab21380c76c2b40
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---

# <a name="understanding-outbound-connections-in-azure"></a>了解 Azure 中的輸出連線

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure 中的虛擬機器 (VM) 可在公用 IP 位址空間中與 Azure 外部的端點進行通訊。 當 VM 啟動輸出流程至公用 IP 位址空間中的目的地時，Azure 會將 VM 的私人 IP 位址對應到一個公用 IP 位址，並允許傳回到達 VM 的流量。

Azure 提供三種不同的方法來達成輸出連線。 每個方法都有自己的功能和條件約束。 仔細檢閱每一種方法以選擇符合您需求的方法。

| 案例 | 方法 | 注意 |
| --- | --- | --- |
| 獨立 VM (無負載平衡器，無執行個體層級公用 IP 位址) |預設 SNAT |Azure 關聯 SNAT 的公用 IP 位址 |
| 負載平衡的 VM (無 VM 上的執行個體層級公用 IP 位址) |使用負載平衡器 的 SNAT |Azure 會使用 SNAT 的負載平衡器公用 IP 位址 |
| 具有執行個體層級公用 IP 位址的 VM (無論是否有負載平衡器) |未使用 SNAT |Azure 會使用指派給 VM 的公用 IP |

如果您不想要 VM 與公用 IP 位址空間中的 Azure 外部端點進行通訊，您可以使用網路安全性群組 (NSG) 來封鎖存取。 [防止公用連線](#preventing-public-connectivity)中會更詳細地討論使用 NSG。

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>無執行個體層級公用 IP 位址的獨立 VM

在此案例中，VM 不是 Azure Load Balancer 集區的一部分，而且沒有指派給它的執行個體層級公用 IP (ILPIP) 位址。 當 VM 建立輸出流程時，Azure 會將輸出流量的公用來源 IP 位址轉譯為私用來源 IP 位址。 此輸出流量所用的公用 IP 位址無法進行設定，而且不利於訂用帳戶的公用 IP 資源限制。 Azure 會使用來源網路位址轉譯 (SNAT) 執行這項功能。 公用 IP 位址的暫時連接埠用來區分源自 VM 的個別流程。 建立流程時，SNAT 會動態配置暫時連接埠。 在此情況下，用於 SNAT 的暫時連接埠稱為 SNAT 連接埠。

SNAT 連接埠是可能會耗盡的有限資源。 請務必了解取用的方式。 每個流程會取用一個 SNAT 連接埠至單一目的地 IP 位址。 針對相同目的地 IP 位址的多個流程，每個流程取用單一 SNAT 連接埠。 這可確保自相同公用 IP 位址至相同目的地 IP 位址時，流程是唯一的。 每個前往不同目的地 IP 位址的多個流程，每個目的地會取用單一 SNAT 連接埠。 目的地 IP 位址會使流程唯一。

您可以使用[負載平衡器的 Log Analytics](load-balancer-monitor-log.md) 和 [要監視之 SNAT 連接埠耗盡訊息的警示事件記錄檔](load-balancer-monitor-log.md#alert-event-log)。 當 SNAT 連接埠資源耗盡時，輸出流程失敗，直到現有流程釋放 SNAT 連接埠為止。 負載平衡器會使用 4 分鐘閒置逾時以收回 SNAT 連接埠。

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>無執行個體層級公用 IP 位址的負載平衡 VM

在此案例中，VM 是 Azure Load Balancer 集區的一部分。  VM 沒有指派給它的公用 IP 位址。 必須使用連結公用 IP 前端與後端集區的規則來設定 Load Balancer 資源。  如果您未完成此設定，行為就如前一節[不含執行個體層級公用 IP 的獨立 VM](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address) 中所述。

當負載平衡的 VM 建立輸出流程時，Azure 會將輸出流量的私用來源 IP 位址轉譯為公用負載平衡器前端的公用 IP 位址。 Azure 會使用來源網路位址轉譯 (SNAT) 執行這項功能。 負載平衡器公用 IP 位址的暫時連接埠用來區分源自 VM 的個別流程。 建立輸出流程時，SNAT 會動態配置暫時連接埠。 在此情況下，用於 SNAT 的暫時連接埠稱為 SNAT 連接埠。

SNAT 連接埠是可能會耗盡的有限資源。 請務必了解取用的方式。 每個流程會取用一個 SNAT 連接埠至單一目的地 IP 位址。 針對相同目的地 IP 位址的多個流程，每個流程取用單一 SNAT 連接埠。 這可確保自相同公用 IP 位址至相同目的地 IP 位址時，流程是唯一的。 每個前往不同目的地 IP 位址的多個流程，每個目的地會取用單一 SNAT 連接埠。 目的地 IP 位址會使流程唯一。

您可以使用[負載平衡器的 Log Analytics](load-balancer-monitor-log.md) 和 [要監視之 SNAT 連接埠耗盡訊息的警示事件記錄檔](load-balancer-monitor-log.md#alert-event-log)。 當 SNAT 連接埠資源耗盡時，輸出流程失敗，直到現有流程釋放 SNAT 連接埠為止。 負載平衡器會使用 4 分鐘閒置逾時以收回 SNAT 連接埠。

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>具有執行個體層級公用 IP 位址的 VM (無論是否有負載平衡器)

在此案例中，VM 具有指派給它的執行個體層級公用 IP (ILPIP)。 VM 進行負載平衡與否並不重要。 使用 ILPIP 時，不會使用來源網路位址轉譯 (SNAT)。 VM 會針對所有輸出流程使用 ILPIP。 如果您的應用程式起始許多輸出流程且遇到 SNAT 耗盡，您應該考慮指派 ILPIP 以避免 SNAT 限制。

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>探索指定 VM 使用的公用 IP

有許多方式可判斷輸出連線的公用來源 IP 位址。 OpenDNS 提供可顯示您 VM 公用 IP 位址的服務。 使用 nslookup 命令，您可以將名稱 myip.opendns.com 的 DNS 查詢傳送至 OpenDNS 解析程式。 服務會傳回用來傳送查詢的來源 IP 位址。 當您從 VM 執行下列查詢時，回應會是用於該 VM 的公用 IP。

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>防止公用連線

有時並不想允許 VM 建立輸出流程，或可能需要管理可以使用輸出流程到達的目的地。 在此情況下，您使用[網路安全性群組 (NSG)](../virtual-network/virtual-networks-nsg.md) 來管理 VM 可到達的目的地。 當您將 NSG 套用到負載平衡的 VM 時，需要注意[預設標籤](../virtual-network/virtual-networks-nsg.md#default-tags)和[預設規則](../virtual-network/virtual-networks-nsg.md#default-rules)。

您必須確定 VM 可以從 Azure Load Balancer 接收健康情況探查要求。 如果 NSG 封鎖來自 AZURE_LOADBALANCER 預設標籤的健全狀況探查要求，您的 VM 健全狀況探查會失敗，且會將 VM 標示為離線。 負載平衡器會停止將新的流程傳送到該 VM。

## <a name="limitations"></a>限制

若[多個 (公用) IP 位址與負載平衡器關聯](load-balancer-multivip-overview.md)這些公用 IP 位址中的任一位址都可以是輸出流程的候選項。

Azure 會使用演算法，根據集區的大小決定可用的 SNAT 連接埠數目。  目前無法進行設定。

請務必記住，可用的 SNAT 連接埠號碼不會直接轉譯為連線數目。 如需何時及如何配置 SNAT 連接埠以及如何管理這個可耗盡資源的詳細資料，請參閱上述內容。

