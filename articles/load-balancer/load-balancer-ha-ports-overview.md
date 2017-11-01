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
ms.openlocfilehash: 62d78e067e50183f25af84e547db2e11c0014f5d
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="high-availability-ports-overview-preview"></a>高可用性連接埠概觀 (預覽)

Azure Load Balancer Standard 導入了新的功能，可在使用內部負載平衡器時，同時對所有連接埠上的 TCP 和 UDP 流量進行負載平衡。 

>[!NOTE]
> Load Balancer Standard 有提供「高可用性連接埠」功能，且目前為預覽版。 在預覽階段，功能可能沒有與正式發行版本功能相同層級的可用性和可靠性。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 必須註冊 Load Balancer Standard 預覽版，才能將「HA 連接埠」與 Load Balancer Standard 資源搭配使用。 除了 Load Balancer [Standard 預覽版](https://aka.ms/lbpreview#preview-sign-up)之外，請也依照指示進行註冊。

HA 連接埠規則是內部 Load Balancer Standard 上所設定之負載平衡規則的變化。  案例則會簡化，以提供單一 LB 規則來將抵達內部 Load Balancer Standard 前端所有連接埠的所有 TCP 和 UDP 流量進行負載平衡。 系統會根據來源 IP 位址、來源連接埠、目的地 IP 位址、目的地連接埠和通訊協定所構成的五 Tuple，來決定每個流量的負載平衡。

HA 連接埠可實現虛擬網路內網路虛擬設備 (NVA) 之高可用性和縮放之類的重要案例，以及必須負載平衡大量連接埠的其他案例。 

若要設定 HA 連接埠，您可以將前端和後端連接埠設定為 **0**，並將通訊協定設定為**全部**。  內部負載平衡器資源現在會平衡所有 TCP 和 UDP 流量，而不會理會連接埠號碼。

## <a name="why-use-ha-ports"></a>為何要使用 HA 連接埠

### <a name="network-virtual-appliances"></a>網路虛擬設備

您可以使用網路虛擬設備 (NVA) 來保護您的 Azure 工作負載，使其不會遭受多個類型的安全性威脅。 當您在這些案例中使用 NVA 時，它們必須具有可靠性、高可用性，並可依需要相應放大。

要在您的案例中達成這些目標，您只需要將 NVA 執行個體新增至 Azure 內部負載平衡器的後端集區，並設定 HA 連接埠負載平衡器規則即可。

HA 連接埠可為 NVA HA 案例提供幾個好處：
- 透過執行個體健康情況探查快速容錯移轉至狀況良好的執行個體
- 透過相應放大為 n-active 執行個體而提升效能
- n-active 和主動-被動案例
- 不需要使用複雜的解決方案 (例如，Zookeeper 節點) 來監視設備

下列範例顯示中樞和輪輻虛擬網路部署，在此部署中，輪輻會強制讓其流量先經由通道透過 NVA 流往中樞虛擬網路，然後才離開受信任的空間。 在 HA 連接埠組態中，NVA 位於內部 Load Balancer Standard 後面。  所有流量皆可據以處理並轉送。 

![HA 連接埠範例](./media/load-balancer-ha-ports-overview/nvaha.png)

圖 1 - 中樞和輪輻虛擬網路 (具有以 HA 模式部署的 NVA)

如果您使用網路虛擬設備，請與個別提供者確認如何以最佳方式使用 HA 連接埠，以及所支援的案例有哪些。

### <a name="load-balancing-large-numbers-of-ports"></a>負載平衡大量連接埠

您也可以對需要負載平衡大量連接埠的應用程式案例使用 HA 連接埠。 這些案例可以使用內部 [Load Balancer Standard](https://aka.ms/lbpreview) 與 HA 連接埠來簡化，利用單一負載平衡規則取代多個個別的負載平衡規則，並讓每個連接埠有一個這樣的規則。

## <a name="region-availability"></a>區域可用性

HA 連接埠可在[和 Load Balancer Standard 相同的區域](https://aka.ms/lbpreview#region-availability)中取得。  

## <a name="preview-sign-up"></a>註冊預覽

若要參加 Load Balancer Standard 中 HA 連接埠功能的預覽，請使用 Azure CLI 2.0 或 PowerShell 來註冊您的訂用帳戶以獲得存取。  遵循下列三個步驟：

>[!NOTE]
>若要使用此功能，除了「HA 連接埠」之外，您必須也註冊 Load Balancer [Standard 預覽版](https://aka.ms/lbpreview#preview-sign-up)。 註冊「HA 連接埠」或 Load Balancer Standard 預覽版最多可能需要一小時的時間。

### <a name="sign-up-using-azure-cli-20"></a>使用 Azure CLI 2.0 註冊

1. 向提供者註冊功能
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. 上述作業最多可能需要 10 分鐘的時間才能完成。  您可以使用下列命令來檢查作業狀態：

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    當功能註冊狀態傳回 'Registered' 時 (如下所示)，請繼續進行步驟 3：
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. 請向資源提供者重新註冊您的訂用帳戶，以完成預覽版註冊：

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>使用 PowerShell 註冊

1. 向提供者註冊功能
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. 上述作業最多可能需要 10 分鐘的時間才能完成。  您可以使用下列命令來檢查作業狀態：

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    當功能註冊狀態傳回 'Registered' 時 (如下所示)，請繼續進行步驟 3：
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. 請向資源提供者重新註冊您的訂用帳戶，以完成預覽版註冊：

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>限制

以下是 HA 連接埠的支援組態或例外狀況：

- 單一前端 IP 組態可以有一個與 HA 連接埠搭配的單一 DSR 負載平衡器規則，或是一個與 HA 連接埠搭配的單一非 DSR 負載平衡器規則。 但兩者不能同時存在。
- 單一網路介面的 IP 組態只能在 HA 連接埠建立一個非 DSR 負載平衡器規則。 此 ipconfig 不能設定任何其他規則。
- 單一網路介面的 IP 組態可以在 HA 連接埠建立一或多個 DSR 負載平衡器規則，但前提是其各自的前端 IP 組態都是唯一的。
- 如果所有負載平衡規則都是 HA 連接埠 (只有 DSR)，或是所有規則都是非 HA 連接埠 (DSR 與非 DSR)，則兩個 (或以上) 指向相同後端集區的負載平衡器規則可以同時存在。 如果同時使用 HA 連接埠和非 HA 連接埠規則，則兩個這類負載平衡規則不能同時存在。
- HA 連接埠不適用於 IPv6。


## <a name="next-steps"></a>後續步驟

- [在內部 Load Balancer Standard 上設定 HA 連接埠](load-balancer-configure-ha-ports.md)
- [深入了解 Load Balancer Standard 預覽版](https://aka.ms/lbpreview)

