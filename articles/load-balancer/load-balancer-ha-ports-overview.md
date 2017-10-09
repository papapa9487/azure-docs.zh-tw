---
title: "Azure 中的高可用性連接埠概觀 | Microsoft Docs"
description: "深入了解內部負載平衡器上的高可用性連接埠負載平衡"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 2219aeb725b207fd92ff3e7603d7ee9c78f2844c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/28/2017

---

# <a name="high-availability-ports-overview-preview"></a>高可用性連接埠概觀 (預覽)

Azure Load Balancer 的標準 SKU 導入了高可用性 (HA) 連接埠功能，以便分散來自所有連接埠的流量，並適用於所有支援的通訊協定。 在設定內部負載平衡器時，使用者可以設定 HA 連接埠規則，以將前端和後端連接埠設定為 **0**，並將通訊協定設定為**全部**，從而讓所有流量可以流經內部負載平衡器。

>[!NOTE]
> 高可用性連接埠概觀目前為預覽版。 在預覽階段，功能可能沒有與正式發行版本功能相同層級的可用性和可靠性。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

負載平衡演算法維持不變，目的地則會依據五個 Tuple <來源 IP 位址、來源連接埠、目的地 IP 位址、目的地連接埠、通訊協定> 來做選取。 但是，這個組態可讓您使用單一 LB 規則就能處理所有可用流量，並減少組態複雜度，以及減少使用者所能新增之負載平衡規則數目上限所施加的任何限制。

HA 連接埠所能實現的其中一個重要案例，是在 Azure 虛擬網路中部署高可用性的網路虛擬設備。 此外，HA 連接埠所能實現的另一個常見案例，是為各種連接埠提供負載平衡。 

## <a name="why-use-high-ha-ports"></a>為何要使用高 HA 連接埠

Azure 客戶極度仰賴網路虛擬設備 (NVA) 來保護他們的工作負載，使其免於遭受多種安全性威脅。 此外，NVA 必須可靠且具有高可用性。  

HA 連接埠不需要較為複雜的解決方案 (例如 ZooKeeper)，所以能減少 NVA HA 實例的複雜性，並可透過速度更快的容錯移轉和相應放大選項來提升可靠性。 現在您只要在 Azure 內部負載平衡器的後端集區中新增 NVA，然後設定 HA 連接埠負載平衡器規則，就可以實現 NVA HA。

下列範例顯示中樞和輪輻虛擬網路部署，在此部署中，輪輻會強制讓其流量先經由通道透過 NVA 流往中樞虛擬網路，然後才離開受信任的空間。 NVA 的位置是在具有 HA 連接埠組態的內部負載平衡器後方，因此可以處理所有流量並據以轉送。 

![HA 連接埠範例](./media/load-balancer-ha-ports-overview/nvaha.png)

圖 1 - 中樞和輪輻虛擬網路 (具有以 HA 模式部署的 NVA)


## <a name="region-availability"></a>區域可用性

HA 連接埠目前已在以下地區內上市：
- 美國東部 2
- 美國中部
- 北歐
- 美國中西部
- 西歐
- 東南亞 

## <a name="preview-sign-up"></a>註冊預覽

若要參加 Load Balancer Standard SKU 中 HA 連接埠功能的預覽，請使用 PowerShell 或 Azure CLI 2.0 來註冊您的訂用帳戶以獲得存取。

- 使用 PowerShell 註冊

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- 使用 Azure CLI 2.0 註冊

    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network 
    ```
## <a name="caveats"></a>需要注意的事項

以下是 HA 連接埠的支援組態或例外狀況：

- 單一前端 ipConfiguration 既可以在 HA 連接埠 (所有連接埠) 建立單一的 DSR 負載平衡器規則，也可以在 HA 連接埠 (所有連接埠) 建立單一的非 DSR 負載平衡器規則。 但兩者不能同時存在。
- 單一網路介面的 IP 組態只能在 HA 連接埠建立一個非 DSR 負載平衡器規則。 此 ipconfig 不能設定任何其他規則。
- 單一網路介面的 IP 組態可以在 HA 連接埠建立一或多個 DSR 負載平衡器規則，但前提是其各自的前端 ip 組態都是唯一的。
- 如果所有負載平衡規則都是 HA 連接埠 (只有 DSR)，則兩個 (或以上) 指向相同後端集區的負載平衡器規則可以同時存在，否則所有規則都必須是非 HA 連接埠 (DSR 與非 DSR)。 如果同時使用 HA 連接埠和非 HA 連接埠規則，則兩個這類 LB 規則不能同時存在。
- 在啟用 IPv6 的租用戶上無法使用 HA 連接埠。


## <a name="next-steps"></a>後續步驟

[在內部負載平衡器上設定 HA 連接埠](load-balancer-configure-ha-ports.md)


