---
title: "設定 Web 應用程式防火牆 - Azure 應用程式閘道 | Microsoft Docs"
description: "本文提供如何在現有的或新的應用程式閘道上開始使用 Web 應用程式防火牆的指引。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ceca07e1244af2937ed66f41e5cddc41014fdde2
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>在新的或現有的應用程式閘道上設定 Web 應用程式防火牆

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

了解如何建立已啟用 Web 應用程式防火牆的應用程式閘道，或將 Web 應用程式防火牆新增至現有應用程式閘道。

Azure 應用程式閘道中的 Web 應用程式防火牆 (WAF) 可保護 Web 應用程式，不致遭受常見的 Web 型攻擊，例如 SQL 插入式攻擊、跨網站指令碼攻擊和工作階段攔截。

Azure 應用程式閘道是第 7 層負載平衡器。 不論是在雲端或內部部署中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。 應用程式閘道提供許多應用程式傳遞控制器 (ADC) 功能，包括 HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、安全通訊端層 (SSL) 卸載、自訂健康狀態探查、多網站支援，以及許多其他功能。 若要尋找完整的支援功能清單，請瀏覽：[應用程式閘道概觀](application-gateway-introduction.md)。

下列文章說明如何[將 Web 應用程式防火牆新增至現有應用程式閘道](#add-web-application-firewall-to-an-existing-application-gateway)和[建立使用 Web 應用程式防火牆的應用程式閘道](#create-an-application-gateway-with-web-application-firewall)。

![案例影像][scenario]

## <a name="waf-configuration-differences"></a>WAF 組態差異

如果您已經讀過 [使用 PowerShell 建立應用程式閘道](application-gateway-create-gateway-arm.md)，您會了解建立應用程式閘道時要設定的 SKU 設定。 在應用程式閘道上設定 SKU 時，WAF 會提供其他可定義的設定。 您對應用程式閘道本身不會進行任何其他變更。

| **設定** | **詳細資料**
|---|---|
|**SKU** |不具有 WAF 的一般應用程式閘道支援 **Standard\_Small**、**Standard\_Medium** 和 **Standard\_Large** 大小。 WAF 推出後另外出現了兩個 SKU，分別是 **WAF\_Medium** 和 **WAF\_Large**。 小型應用程式閘道不支援 WAF。|
|**層級** | 可用的值為**標準**或 **WAF**。 使用 Web 應用程式防火牆時，必須選擇 **WAF** 。|
|**模式** | 此設定是 WAF 的模式。 允許的值為**偵測**和**防止**。 當 WAF 設定為偵測模式時，所有威脅會儲存在記錄檔中。 在防止模式時仍會記錄事件，但攻擊者會從應用程式閘道收到 403 未經授權的回應。|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>對現有應用程式閘道新增 Web 應用程式防火牆

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

1. 登入您的 Azure 帳戶。

    ```powershell
    Login-AzureRmAccount
    ```

2. 選取要用於此案例的訂用帳戶。

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. 擷取要在其中新增 Web 應用程式防火牆的閘道。

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

1. 設定 Web 應用程式防火牆 SKU。 可用大小為 **WAF\_Large** 和 **WAF\_Medium**。 使用 Web 應用程式防火牆時，必須為 **WAF** 層，設定 SKU 時必須確認容量。

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

1. 如下列範例所定義地設定 WAF 設定︰

   在 **WafMode** 中，可用值是「防止」和「偵測」。

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

1. 使用上一個步驟所定義的設定來更新應用程式閘道。

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

此命令會更新具有 Web 應用程式防火牆的應用程式閘道。 請瀏覽[應用程式閘道診斷](application-gateway-diagnostics.md)，了解如何檢視應用程式閘道中的記錄檔。 由於 WAF 的安全性本質，您必須定期檢閱記錄檔，以了解 Web 應用程式的安全性狀態。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>建立具有 Web 應用程式防火牆的應用程式閘道

下列步驟會帶您從頭到尾完成建立具有 Web 應用程式防火牆之應用程式閘道的整個程序。

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

1. 執行 `Login-AzureRmAccount` 來登入 Azure，系統會提示使用您的認證進行驗證。

1. 執行 `Get-AzureRmSubscription` 來檢查帳戶的訂用帳戶。

1. 選擇要使用哪一個 Azure 訂用帳戶。

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>建立資源群組

建立應用程式閘道的資源群組。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager 需要所有的資源群組指定一個位置。 此位置用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 "appgw-RG" 的資源群組，且位置為美國西部 ("West US")。

> [!NOTE]
> 如果您需要為應用程式閘道設定自訂探查，請參閱 [使用 PowerShell 建立具有自訂探查的應用程式閘道](application-gateway-create-probe-ps.md)。 請參閱 [自訂探查和健全狀況監視](application-gateway-probe-overview.md) 以取得詳細資訊。

### <a name="configure-virtual-network"></a>設定虛擬網路

應用程式閘道需要有自己的子網路。 在此步驟中，您會建立具有 10.0.0.0/16 位址空間的虛擬網路和兩個子網路，其中，一個用於應用程式閘道，另一個用於後端集區成員。

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for Application Gateway instances. With a smaller subnet, you may not be able to add more instance of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the backend pool members subnet
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previous created subnets
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-public-ip-address"></a>設定公用 IP 位址

為了處理外部要求，應用程式閘道需要公用 IP 位址。 這個公用 IP 位址不能定義 `DomainNameLabel` 給應用程式閘道使用。

```powershell
# Create a public IP address for use with the application gateway. Defining the domainnamelabel during creation is not supported for use with application gateway
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>設定應用程式閘道

```powershell
# Create a IP configuration. This configures what subnet the Application Gateway uses. When Application Gateway starts, it picks up an IP address from the subnet configured and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a backend pool to hold the addresses or NICs for the application that application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authenication certificate that will be used to communicate with the backend servers
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Conifugre the backend HTTP settings to be used to define how traffic is routed to the backend pool. The authenication certificate used in the previous step is added to the backend http settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a frontend port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a frontend IP configuration to associate the public IP address with the application gateway
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end ip configuration, port, and ssl certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

#Create a load balancer routing rule that configures the load balancer behavior. In this example, a basic round robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

#Configure the waf configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway utilizing all the previously created configuration objects
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> 基於保護的目的，使用基本 Web 應用程式防火牆設定建立的應用程式閘道，是使用 CRS 3.0 所設定。

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 [在 Azure 中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 若要設定別名，請使用連接至應用程式閘道的 PublicIPAddress 項目，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 應用程式閘道的 DNS 名稱應該用來建立將兩個 Web 應用程式指向此 DNS 名稱的 CNAME 記錄。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>後續步驟

請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)，以了解如何設定診斷記錄，以及如何記錄 Web 應用程式防火牆偵測到或防止的事件

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

