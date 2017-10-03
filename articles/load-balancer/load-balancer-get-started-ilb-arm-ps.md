---
title: "建立 Azure 內部負載平衡器 - PowerShell | Microsoft Docs"
description: "了解如何使用資源管理員中的 PowerShell 建立內部負載平衡器"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8feb3b5f9dddc7b54b9c5e733675c2a9aca2f223
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="create-an-internal-load-balancer-using-powershell"></a>使用 PowerShell 建立內部負載平衡器

> [!div class="op_single_selector"]
> * [Azure 入口網站](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [範本](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。  本文涵蓋之內容包括使用 Resource Manager 部署模型，Microsoft 建議大部分的新部署使用此模型，而不是[傳統部署模型](load-balancer-get-started-ilb-classic-ps.md)。

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

下列步驟說明如何搭配 PowerShell 使用 Azure Resource Manager，來建立內部負載平衡器。 使用 Azure Resource Manager 時，會個別設定建立內部負載平衡器所需的項目，然後結合在一起以建立負載平衡器。

建立並設定下列物件以部署負載平衡器：

* 前端 IP 組態 - 會設定傳入網路流量的私人 IP 位址。
* 後端位址集區 - 會設定可接收來自前端 IP 集區之負載平衡流量的網路介面。
* 負載平衡規則 - 負載平衡器的來源與本機連接埠組態。
* 探查 - 設定虛擬機器執行個體的健全狀態探查。
* 傳入 NAT 規則 - 設定直接存取其中一個虛擬機器執行個體的連接埠規則。

您可以在 [Azure 資源管理員的負載平衡器支援](load-balancer-arm.md)中取得關於負載平衡器元件與 Azure 資源管理員的詳細資訊。

下列步驟說明如何在兩部虛擬機器之間設定負載平衡器。

## <a name="set-up-powershell-to-use-resource-manager"></a>設定 PowerShell 以使用 Resource Manager

請確定您的 PowerShell 的 Azure 模組具有最新的實際執行版本，以及正確設定 PowerShell 以存取您的 Azure 訂用帳戶。

### <a name="step-1"></a>步驟 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶

```powershell
Get-AzureRmSubscription
```

系統會提示使用您的認證進行驗證。

### <a name="step-3"></a>步驟 3

選擇其中一個要使用的 Azure 訂用帳戶。

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="create-resource-group-for-load-balancer"></a>建立負載平衡器的資源群組

建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager 需要所有的資源群組指定一個位置。 此位置會作為該資源群組中之資源的預設位置。 請確定所有建立負載平衡器的命令都是使用同一個資源群組。

在上述範例中，我們建立了名為 **NRP-RG** 的資源群組，且位置為**美國西部**。

## <a name="create-a-virtual-network-and-a-private-ip-address-for-a-front-end-ip-pool"></a>建立前端 IP 集區的虛擬網路和私人 IP 位址

建立虛擬網路的子網路，並指派給變數 $backendSubnet

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

建立虛擬網路：

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

建立虛擬網路，並將子網路 lb-subnet-be 加入至虛擬網路 NRPVNet，並指派給變數 $vnet

## <a name="create-a-front-end-ip-pool-and-back-end-address-pool"></a>建立前端 IP 集區與後端位址集區

設定傳入負載平衡器網路流量的前端 IP 集區以及後端位址集區，以接收負載平衡流量。

### <a name="step-1"></a>步驟 1

使用私人 IP 位址 10.0.2.5 為子網路 10.0.2.0/24 建立前端 IP 集區，作為傳入網路流量端點。

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2"></a>步驟 2

設定用來從前端 IP 集區接收傳入流量的後端位址集區：

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-load-balancing-rules-nat-rules-probe-and-load-balancer"></a>建立負載平衡規則、NAT 規則、探查及負載平衡器

建立前端 IP 集區和後端位址集區之後，請建立屬於負載平衡器資源的規則：

### <a name="step-1"></a>步驟 1

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

上述範例會建立下列項目：

* NAT 規則，讓連接埠 3441 的所有傳入流量都移至連接埠 3389。
* 第二個 NAT 規則，讓連接埠 3442 的所有傳入流量都移至連接埠 3389。
* 負載平衡器規則，將公用連接埠 80 上的所有傳入流量，負載平衡至後端位址集區中的本機連接埠 80。
* 探查規則，以檢查路徑 "HealthProbe.aspx" 的健康情況

### <a name="step-2"></a>步驟 2

建立負載平衡器，並將所有物件 (NAT 規則、負載平衡器規則、探查組態) 加在一起：

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-network-interfaces"></a>建立網路介面

建立內部負載平衡器之後，您需要定義哪些網路介面可以接收傳入的負載平衡網路流量、NAT 規則和探查。 在此情況下需個別設定網路介面，並在稍後指派給虛擬機器。

### <a name="step-1"></a>步驟 1

取得資源的虛擬網路和子網路以建立網路介面：

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

此步驟會建立屬於負載平衡器後端集區的網路介面，並針對此網路介面的 RDP 與第一個 NAT 規則建立關聯：

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2"></a>步驟 2

建立第二個網路介面，稱為 LB-Nic2-BE：

此步驟會建立第二個網路介面，並指派給同一個負載平衡器後端集區，然後與針對 RDP 所建立的第二個 NAT 規則建立關聯：

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

最終結果會顯示下列輸出：

    $backendnic1

預期的輸出：

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>步驟 3

使用 AzureRmVMNetworkInterface 命令將 NIC 指派給虛擬機器。

您可以在下列文件中找到建立虛擬機器並指派給 NIC 的逐步指示︰[使用 PowerShell 建立 Azure VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)。

## <a name="add-the-network-interface"></a>加入網路介面

如果您已建立虛擬機器，您可以透過下列步驟新增網路介面：

### <a name="step-1"></a>步驟 1

將負載平衡器資源載入到變數之中 (如果您還沒這麼做)。 所使用的變數稱為 $lb，並使用來自上述步驟所建立之負載平衡器資源的相同名稱。

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2"></a>步驟 2

將後端組態載入到變數之中。

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3"></a>步驟 3

將已建立的網路介面載入到變數之中。 所使用的變數名稱是 $nic。 所使用的網路介面名稱是來自上述範例的相同名稱。

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4"></a>步驟 4

變更網路介面上的後端組態。

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5"></a>步驟 5

儲存網路介面物件。

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

網路介面在新增至負載平衡器的後端集區後，便會開始根據該負載平衡器資源的負載平衡規則接收網路流量。

## <a name="update-an-existing-load-balancer"></a>更新現有負載平衡器

### <a name="step-1"></a>步驟 1
使用上述範例中的負載平衡器，透過 Get-AzureRmLoadBalancer 將負載平衡器物件指派給變數 $slb

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2"></a>步驟 2

在下列範例中，您會使用前端的連接埠 81 和後端集區的連接埠 8181，將新的輸入 NAT 規則加入現有的負載平衡器

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3"></a>步驟 3

使用 Set-AzureLoadBalancer 儲存新組態

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-a-load-balancer"></a>移除負載平衡器

使用命令 Remove-AzureRmLoadBalancer 刪除資源群組 "NRP-RG" 中先前建立的負載平衡器 "NRP-LB"

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> 您可以使用選擇性參數 -Force 以避免出現刪除提示。

## <a name="next-steps"></a>後續步驟

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

