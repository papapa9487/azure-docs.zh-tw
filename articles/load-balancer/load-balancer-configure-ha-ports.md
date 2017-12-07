---
title: "設定 Azure Load Balancer 的高可用性連接埠| Microsoft Docs"
description: "了解如何使用高可用性連接埠來對所有連接埠上的內部流量進行負載平衡"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2017
ms.author: kumud
ms.openlocfilehash: 646ade828e96810bdc3b07d4dc5c0276a1621969
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>如何設定內部負載平衡器的高可用性連接埠

本文提供如何在內部負載平衡器上部署高可用性 (HA) 連接埠的範例。 如需網路虛擬設備 (NVA) 特有的組態，請參閱對應的提供者網站。

>[!NOTE]
> 高可用性連接埠概觀目前為預覽版。 在預覽階段，功能可能沒有與正式發行版本功能相同層級的可用性和可靠性。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

圖 1 會說明本文所述部署範例的下列組態：
- NVA 部署在 HA 連接埠組態背後之內部負載平衡器的後端集區中。 
- 套用在 DMZ 子網路的 UDR 會將下一個躍點設為內部負載平衡器的虛擬 IP，以將所有流量路由傳送至 NVA。 
- 內部負載平衡器會根據 LB 演算法，將流量分散到其中一個作用中的 NVA。
- NVA 處理流量，並將流量轉送到後端子網路中的原始目的地。
- 如果在後端子網路設定了對應的 UDR，則傳回路徑也可採用相同的路由。 

![HA 連接埠的部署範例](./media/load-balancer-configure-ha-ports/haports.png)

圖 1 - 部署在具有高可用性連接埠之內部負載平衡器後方的網路虛擬設備 

## <a name="preview-sign-up"></a>註冊預覽

若要參加 Load Balancer Standard 中 HA 連接埠功能的預覽，請使用 Azure CLI 2.0 或 PowerShell 來註冊您的訂用帳戶以獲得存取。 註冊您的[Azure Load Balancer Standard 預覽版](https://aka.ms/lbpreview#preview-sign-up)訂用帳戶。

>[!NOTE]
>註冊 Load Balancer Standard 預覽版最多可能需要一小時的時間。

## <a name="configuring-ha-ports"></a>設定 HA 連接埠

HA 連接埠的設定作業包括設定內部負載平衡器 (於後端集區中備有 NVA)、對應的負載平衡器健康情況探查組態 (用以偵測 NVA 健康情況)，以及具有 HA 連接埠的負載平衡器規則。 [開始使用](load-balancer-get-started-ilb-arm-portal.md)中會說明一般負載平衡器的相關組態。 本文主要在說明 HA 連接埠組態。

設定過程基本上涉及將前端連接埠和後端連接埠值設定為 **0**，以及將通訊協定值設為**全部**。 本文說明如何使用 Azure 入口網站、PowerShell 和 Azure CLI 2.0 來設定高可用性連接埠。

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>使用 Azure 入口網站設定 HA 連接埠負載平衡器規則

Azure 入口網站包含 [HA 連接埠] 選項，勾選核取方塊即可啟用此組態。 選取此選項時，系統會自動填入相關的連接埠和通訊協定組態。 

![透過 Azure 入口網站設定 HA 連接埠組態](./media/load-balancer-configure-ha-ports/haports-portal.png)

圖 2 - 透過入口網站設定 HA 連接埠

### <a name="configure-ha-ports-lb-rule-via-resource-manager-template"></a>透過 Resource Manager 範本設定 HA 連接埠 LB 規則

您可以使用適用於 Load Balancer 資源中之 Microsoft.Network/loadBalancers 的 2017-08-01 API 版本，來設定 HA 連接埠。 下列 JSON 程式碼片段示範透過 REST API 來變更 HA 連接埠的 Load Balancer 設定。

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>使用 PowerShell 設定 HA 連接埠負載平衡器規則

在使用 PowerShell 建立內部負載平衡器時，透過下列命令來建立 HA 連接埠負載平衡器規則：

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>使用 Azure CLI 2.0 設定 HA 連接埠負載平衡器規則

在[建立內部負載平衡器集合](load-balancer-get-started-ilb-arm-cli.md)的步驟 4 中，使用下列命令來建立 HA 連接埠負載平衡器規則。

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>後續步驟

- 深入了解[高可用性連接埠](load-balancer-ha-ports-overview.md)
