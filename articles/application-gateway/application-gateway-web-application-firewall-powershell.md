---
title: "設定 Web 應用程式防火牆：Azure 應用程式閘道 | Microsoft Docs"
description: "本文提供如何在現有的或新的應用程式閘道上，開始使用 Web 應用程式防火牆的指引。"
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
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 3d01763709e58d25047a6dec4361bcf6553d54e0
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>在新的或現有的應用程式閘道上設定 Web 應用程式防火牆

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

了解如何建立已啟用 Web 應用程式防火牆 (WAF) 的應用程式閘道。 也會了解如何將 WAF 新增至現有的應用程式閘道。

Azure 應用程式閘道中的 WAF 可保護 Web 應用程式，免於遭受常見的 Web 型攻擊，例如 SQL 插入式攻擊、跨網站指令碼攻擊和工作階段攔截。

 應用程式閘道是第 7 層負載平衡器。 不論是在雲端或內部部署中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。 應用程式閘道提供許多應用程式傳遞控制站 (ADC) 功能：

 * HTTP 負載平衡
 * Cookie 型工作階段同質
 * 安全通訊端層 (SSL) 卸載
 * 自訂健康情況探查
 * 支援多網站功能
 
 若要尋找完整的支援功能清單，請參閱[應用程式閘道概觀](application-gateway-introduction.md)。

這篇文章會示範如何[將 WAF 新增至現有的應用程式閘道](#add-web-application-firewall-to-an-existing-application-gateway)。 也會示範如何[建立會使用 WAF 的應用程式閘道](#create-an-application-gateway-with-web-application-firewall)。

![案例映像][scenario]

## <a name="waf-configuration-differences"></a>WAF 組態差異

如果您已經讀過[使用 PowerShell 建立應用程式閘道](application-gateway-create-gateway-arm.md)，您會了解建立應用程式閘道時要設定的 SKU 設定。 在應用程式閘道上設定 SKU 時，WAF 會提供其他可定義的設定。 您對應用程式閘道本身不會進行任何其他變更。

| **設定** | **詳細資料**
|---|---|
|**SKU** |不具有 WAF 的一般應用程式閘道支援 **Standard\_Small**、**Standard\_Medium** 和 **Standard\_Large** 大小。 WAF 推出後另外出現了兩個 SKU，分別是 **WAF\_Medium** 和 **WAF\_Large**。 小型應用程式閘道不支援 WAF。|
|**層級** | 可用的值為**標準**或 **WAF**。 當您使用 WAF 時，您必須選擇 **WAF**。|
|**模式** | 此設定是 WAF 的模式。 允許的值為**偵測**和**防止**。 當 WAF 設定為**偵測**模式時，所有威脅會儲存在記錄檔中。 在**防止**模式時仍會記錄事件，但攻擊者會從應用程式閘道收到 403 未經授權的回應。|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>對現有應用程式閘道新增 Web 應用程式防火牆

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，請參閱[搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。

1. 登入您的 Azure 帳戶。

    ```powershell
    Login-AzureRmAccount
    ```

2. 選取要用於此案例的訂用帳戶。

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. 擷取您要新增 WAF 的閘道。

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. 設定 WAF SKU。 可用大小為 **WAF\_Large** 和 **WAF\_Medium**。 當您使用 WAF 時，階層必須是 **WAF**。 當您設定 SKU 時確認容量。

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. 如下列範例所定義地設定 WAF 設定。 在 **FirewallMode** 中，可用值是**防止**和**偵測**。

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. 使用上一個步驟所定義的設定來更新應用程式閘道。

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

此命令會更新具有 WAF 的應用程式閘道。 若要了解如何檢視應用程式閘道中的記錄，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。 由於 WAF 的安全性本質，您必須定期檢閱記錄，以了解 Web 應用程式的安全性狀態。

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>建立具有 Web 應用程式防火牆的應用程式閘道

下列步驟會帶您逐步完成建立具有 WAF 之應用程式閘道的整個程序。

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，請參閱[搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。

1. 執行 `Login-AzureRmAccount` 以登入 Azure。 系統會提示使用您的認證進行驗證。

2. 執行 `Get-AzureRmSubscription` 來檢查帳戶的訂用帳戶。

3. 選擇要使用的 Azure 訂用帳戶。

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
> 如果您需要為應用程式閘道設定自訂探查，請參閱 [使用 PowerShell 建立具有自訂探查的應用程式閘道](application-gateway-create-probe-ps.md)。 如需詳細資訊，請參閱[自訂探查和健康情況監視](application-gateway-probe-overview.md)。

### <a name="configure-a-virtual-network"></a>設定虛擬網路

應用程式閘道需要有自己的子網路。 在此步驟中，您會建立具有 10.0.0.0/16 位址空間的虛擬網路和兩個子網路，其中，一個用於應用程式閘道，另一個用於後端集區成員。

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>設定公用 IP 位址

為了處理外部要求，應用程式閘道需要公用 IP 位址。 這個公用 IP 位址不能定義 `DomainNameLabel` 給應用程式閘道使用。

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>設定應用程式閘道

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> 基於保護的目的，使用基本 WAF 設定建立的應用程式閘道，是使用 CRS 3.0 來設定。

## <a name="get-an-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，請使用 CNAME 記錄來指向應用程式閘道的公用端點。 如需詳細資訊，請參閱[針對 Azure 雲端服務設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 

若要設定別名，請使用連結至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 使用應用程式閘道的 DNS 名稱所建立的 CNAME 記錄，可將兩個 Web 應用程式指向此 DNS 名稱。 不建議使用 A 記錄，因為應用程式閘道重新啟動時，VIP 可能會變更。

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

若要了解如何設定診斷記錄，以記錄 WAF 偵測到或防止的事件，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

