---
title: "使用 Azure PowerShell 管理 Azure DDoS Protection Standard | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 管理 Azure DDoS Protection Standard。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: baac97db61b84000557e7150a64ffb64d81ce00c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure DDoS Protection Standard

了解如何啟用和停用分散式阻斷服務 (DDoS) 保護，並使用遙測技術透過 Azure DDoS Protection Standard 來降低 DDoS 攻擊。 DDoS Protection Standard 會保護任何與 Azure [公用 IP 位址](virtual-network-public-ip-address.md)相關聯的 Azure 資源，例如虛擬機器、負載平衡器和應用程式閘道。 若要了解有關 DDoS Protection Standard 和其功能的詳細資訊，請參閱 [DDoS Protection Standard 概觀](ddos-protection-overview.md)。 

>[!IMPORTANT]
>Azure DDoS Protection Standard (DDoS Protection) 目前為預覽狀態。 支援 DDoS Protection 的 Azure 資源數量有限，並僅在幾個精選的區域提供支援。 如需適用區域的清單，請參閱 [DDoS Protection Standard 概觀](ddos-protection-overview.md)。 您必須在有限的預覽期間[註冊此服務](http://aka.ms/ddosprotection)，才能為您的訂用帳戶啟用 DDoS Protection。 註冊後，Azure DDoS 小組會與您連絡，引導您完成啟用程序。


## <a name="log-in-to-azure"></a>登入 Azure

使用 `Login-AzureRmAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 如果您需要安裝或升級 Azure PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>啟用 DDoS Protection Standard - 新的虛擬網路

若要建立已啟用 DDoS Protection 的虛擬網路，請執行下列範例：

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

此範例建立的虛擬網路具有兩個子網路和兩個 DNS 伺服器。 指定虛擬網路上 DNS 伺服器的作用是部署到此虛擬網路的 NIC/VM 會繼承這些 DNS 伺服器作為預設值。 虛擬網路中所有的受保護資源都已啟用 DDoS Protection。

> [!WARNING]
> 選取區域時，請從[概觀](ddos-protection-overview.md)中的清單選擇支援區域。

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>在現有虛擬網路上啟用 DDoS Protection

若要在現有虛擬網路上啟用 DDoS Protection，請執行下列範例：

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> 虛擬網路必須位於支援的區域內。 如需支援區域的清單，請參閱 [Azure DDoS Protection Standard 概觀](ddos-protection-overview.md)。

## <a name="disable-ddos-protection-on-a-virtual-network"></a>在虛擬網路上停用 DDoS Protection

若要在虛擬網路上停用 DDoS Protection，請執行下列範例：

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>檢閱虛擬網路的 DDoS Protection 狀態 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>使用 DDoS Protection 遙測

攻擊的遙測可透過 Azure 監視器即時提供。 遙測只適用於公用 IP 位址在安全防護之下的期間。 在攻擊風險降低之前或之後，您都看不到遙測。

### <a name="configure-alerts-on-ddos-protection-metrics"></a>設定 DDoS Protection 計量的警示

運用 Azure 監視器的警示設定，您可以選取任何可用的 DDoS Protection 計量，以在攻擊期間警示何時有作用中的安全防護功能。

#### <a name="configure-email-alert-rules-via-azure"></a>透過 Azure 設定電子郵件警示規則

1. 取得您可使用的訂用帳戶清單。 確認您使用正確的訂用帳戶。 若不是，請使用 Get-AzureRmSubscription 的輸出將它設為正確的帳戶。 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. 若要列出資源群組上的現有規則，使用下列命令： 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. 若要建立規則，您必須先具有下列資訊︰ 

    - 您想要為其設定警示之資源的資源識別碼。
    - 該資源可用的計量定義。 取得資源識別碼的方法之一，是使用 Azure 入口網站。 假設已建立資源，請在 Azure 入口網站中選取它。 然後在下一個頁面中，選取 [設定] 區段下的 [屬性]。 [資源識別碼] 是下一個頁面中的欄位。 另一種方法是使用 [Azure 資源總管](https://resources.azure.com/)。 公用 IP 的資源識別碼範例如下：`/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    下列範例會為與虛擬網路中資源相關聯的公用 IP 位址建立警示。 要建立警示的計量為 [是否正遭受 DDoS 攻擊]。 這是布林值 1 或 0。 **1** 表示您正遭受攻擊。 **0** 表示並未遭受攻擊。 攻擊啟動的前 5 分鐘內，警示便會建立。

    若要在建立警示時建立 Webhook 或傳送電子郵件，請先建立電子郵件和/或 Webhook。 建立電子郵件或 Webhook 之後，立即使用 `-Actions` 標記建立規則，如下列範例所示。 您無法使用 PowerShell 將 Webhook 或電子郵件與現有規則建立關聯。

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. 請查看規則，確認您已正確建立警示：

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

您也可以深入了解如何[設定 Webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[邏輯應用程式](../logic-apps/logic-apps-what-are-logic-apps.md)，以便建立警示。

## <a name="configure-logging-on-ddos-protection-metrics"></a>設定 DDoS Protection 計量的記錄

請參閱 [PowerShell 快速入門範例](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，協助您透過 PowerShell 存取和設定 Azure 診斷記錄。

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure 診斷記錄檔](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 Log Analytics 分析來自 Azure 儲存體的記錄](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [開始使用事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)