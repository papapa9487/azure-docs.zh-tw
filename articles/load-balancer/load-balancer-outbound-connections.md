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
ms.openlocfilehash: d02960017b8793eccc2990a17e3d854991e877b6
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
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

SNAT 連接埠是可能會耗盡的有限資源。 請務必了解取用的方式。 每個流程都會取用一個 SNAT 連接埠至單一目的地 IP。 針對相同目的地 IP 位址的多個流程，每個流程都會取用單一 SNAT 連接埠。 這可確保源自相同公用 IP 位址至相同目的地 IP 位址和連接埠時，流程是唯一的。 多個各自前往不同目的地 IP 位址和連接埠的流程會共用單一 SNAT 連接埠。 目的地 IP 位址和連接埠會使流程成為唯一的。

您可以使用[負載平衡器的記錄分析](load-balancer-monitor-log.md)和[要監視之 SNAT 連接埠耗盡訊息的警示事件記錄檔](load-balancer-monitor-log.md#alert-event-log)，來監視連出連線的健康情況。 當 SNAT 連接埠資源耗盡時，輸出流程失敗，直到現有流程釋放 SNAT 連接埠為止。 負載平衡器會使用 4 分鐘閒置逾時以收回 SNAT 連接埠。  檢閱下一節[具有執行個體層級公用 IP 位址的 VM (無論是否有負載平衡器)](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer)，以及[管理 SNAT 耗盡](#snatexhaust)。

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>無執行個體層級公用 IP 位址的負載平衡 VM

在此案例中，VM 是 Azure Load Balancer 集區的一部分。  VM 沒有指派給它的公用 IP 位址。 必須使用連結公用 IP 前端與後端集區的規則來設定 Load Balancer 資源。  如果您未完成此設定，行為就如前一節[不含執行個體層級公用 IP 的獨立 VM](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address) 中所述。

當負載平衡的 VM 建立輸出流程時，Azure 會將輸出流量的私用來源 IP 位址轉譯為公用負載平衡器前端的公用 IP 位址。 Azure 會使用來源網路位址轉譯 (SNAT) 執行這項功能。 負載平衡器公用 IP 位址的暫時連接埠用來區分源自 VM 的個別流程。 建立輸出流程時，SNAT 會動態配置暫時連接埠。 在此情況下，用於 SNAT 的暫時連接埠稱為 SNAT 連接埠。

SNAT 連接埠是可能會耗盡的有限資源。 請務必了解取用的方式。 每個流程都會取用一個 SNAT 連接埠至單一目的地 IP 位址和連接埠。 針對相同目的地 IP 位址的多個流程，每個流程都會取用單一 SNAT 連接埠。 這可確保源自相同公用 IP 位址至相同目的地 IP 位址和連接埠時，流程是唯一的。 多個各自前往不同目的地 IP 位址和連接埠的流程會共用單一 SNAT 連接埠。 目的地 IP 位址和連接埠會使流程成為唯一的。

您可以使用[負載平衡器的記錄分析](load-balancer-monitor-log.md)和[要監視之 SNAT 連接埠耗盡訊息的警示事件記錄檔](load-balancer-monitor-log.md#alert-event-log)，來監視連出連線的健康情況。 當 SNAT 連接埠資源耗盡時，輸出流程失敗，直到現有流程釋放 SNAT 連接埠為止。 負載平衡器會使用 4 分鐘閒置逾時以收回 SNAT 連接埠。  檢閱下一節及[管理 SNAT 耗盡](#snatexhaust)。

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>具有執行個體層級公用 IP 位址的 VM (無論是否有負載平衡器)

在此案例中，VM 具有指派給它的執行個體層級公用 IP (ILPIP)。 VM 進行負載平衡與否並不重要。 使用 ILPIP 時，不會使用來源網路位址轉譯 (SNAT)。 VM 會針對所有輸出流程使用 ILPIP。 如果您的應用程式起始許多輸出流程而發生 SNAT 耗盡的情況，您應該考慮指派 ILPIP 來緩和 SNAT 限制的問題。

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>探索指定 VM 使用的公用 IP

有許多方式可判斷輸出連線的公用來源 IP 位址。 OpenDNS 提供可顯示您 VM 公用 IP 位址的服務。 使用 nslookup 命令，您可以將名稱 myip.opendns.com 的 DNS 查詢傳送至 OpenDNS 解析程式。 服務會傳回用來傳送查詢的來源 IP 位址。 當您從 VM 執行下列查詢時，回應會是用於該 VM 的公用 IP。

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>防止公用連線

有時您並不想允許 VM 建立輸出流程，或可能需要管理可使用輸出流程來連線到哪些目的地，或哪些目的地可以發起輸入流程。 在此情況下，您需使用[網路安全性群組 (NSG)](../virtual-network/virtual-networks-nsg.md) 來管理 VM 可連線的目的地，以及哪些公用目的地可以起始輸入流程。 當您將 NSG 套用到負載平衡的 VM 時，需要注意[預設標籤](../virtual-network/virtual-networks-nsg.md#default-tags)和[預設規則](../virtual-network/virtual-networks-nsg.md#default-rules)。

您必須確定 VM 可以從 Azure Load Balancer 接收健康情況探查要求。 如果 NSG 封鎖來自 AZURE_LOADBALANCER 預設標籤的健全狀況探查要求，您的 VM 健全狀況探查會失敗，且會將 VM 標示為離線。 負載平衡器會停止將新的流程傳送到該 VM。

## <a name="snatexhaust"></a>管理 SNAT 耗盡

如[無執行個體層級公用 IP 位址的獨立 VM](#standalone-vm-with-no-instance-level-public-ip-address)和[無執行個體層級公用 IP 位址的負載平衡 VM](#standalone-vm-with-no-instance-level-public-ip-address)中所述，用於 SNAT 的暫時連接埠是可耗盡的資源。

如果您知道將會對相同的目的地 IP 位址和連接埠起始許多連出 TCP 或 UDP 連線、觀察到失敗的連出連線，或是支援人員告知您 SNAT 連接埠將耗盡，您有數個可緩和這些問題的一般選項。  請檢閱這些選項並判斷哪一個最適合您的案例。  可能會有一或多個選項有助於管理此案例。

### <a name="modify-application-to-reuse-connections"></a>將應用程式修改成重複使用連線 
您可以在應用程式中重複使用連線，以降低對用於 SNAT 之暫時連接埠的需求。  對於 HTTP/1.1 之類的通訊協定尤其如此，因為連線重複使用是預設值。  而使用 HTTP 做為其傳輸方式的其他通訊協定也會因而受益。  重複使用一律比每個要求的獨立且不可部分完成的 TCP 連線來得好，而且可帶來效能更好又非常有效率的 TCP 傳輸。

### <a name="modify-application-to-use-connection-pooling"></a>將應用程式修改成使用連線共用
您可以在應用程式中採用連線集區配置，這樣要求會在內部分布到一固定的連線集合 (每個都盡可能地重複使用)。  這麼做會限制使用中的暫時連接埠的數目，並建立較可預測的環境。  當作業之回應的單一連線被封鎖時，這麼做可支援多個同時進行的作業，進而增加要求的輸送量。  連線集區可能已存在於您用來開發應用程式的架構中，或是您應用程式的組態設定中。  您可以結合此作法與連線重複使用，這樣多個要求就會取用固定且可預測的數個對應相同目的地 IP 位址和連接埠的連接埠，同時也受益於非常有效率地使用 TCP 交易，而減少延遲和資源使用率。

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>將應用程式修改成使用較不積極的重試邏輯
當用於 SNAT 的暫時連接埠耗盡或發生應用程式失敗時，不含衰減和降速邏輯的積極或暴力重試會造成耗盡的情況發生或持續存在。 您可以使用較不積極的重試邏輯，以降低對暫時連接埠的需求。   暫時連接埠有 4 分鐘的閒置逾時 (無法調整)，如果重試太過積極，耗盡情況就沒有機會進行自我清理。  因此，考慮應用程式重試交易的方法和頻率是設計的重要考量。

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>指派執行個體層級公用 IP 給每個 VM
這會將您的案例變更為 [VM 的執行個體層級公用 IP](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer)。  用於每個 VM 的所有公用 IP 暫時連接埠都可供 VM 使用 (與公用 IP 暫時連接埠會與個別後端集區之所有相關 VM 共用的案例相反)。  要考慮的取捨點如 IP 位址的額外成本，以及將大量的個別 IP 位址加入白名單的潛在影響。

## <a name="limitations"></a>限制

若[多個 (公用) IP 位址與負載平衡器關聯](load-balancer-multivip-overview.md)這些公用 IP 位址中的任一位址都可以是輸出流程的候選項。

Azure 會使用演算法，根據集區的大小決定可用的 SNAT 連接埠數目。  目前無法進行設定。

連出連線有 4 分鐘的閒置逾時。  此值無法調整。

請務必記住，可用的 SNAT 連接埠號碼不會直接轉譯為連線數目。 如需何時及如何配置 SNAT 連接埠以及[如何管理這個可耗盡資源](#snatexhaust)的詳細資料，請參閱上一節。
