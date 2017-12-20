---
title: "建立和管理 Azure 應用程式閘道 - PowerShell | Microsoft Docs"
description: "本頁面提供使用 Azure 資源管理員建立、設定、啟動和刪除 Azure 應用程式閘道的指示。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: c419e1032476818e430251246022ae14e4355024
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2017
---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>使用 Azure 資源管理員建立、啟動或刪除應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 範本](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 應用程式閘道是第 7 層負載平衡器。 不論是在雲端或內部部署環境中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。 應用程式閘道提供許多應用程式傳遞控制器 (ADC) 功能，包括 HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、安全通訊端層 (SSL) 卸載、自訂健全狀態探查、多網站支援，以及許多其他功能。 若要尋找完整的支援功能清單，請瀏覽[應用程式閘道概觀](application-gateway-introduction.md)。

本文將逐步引導您完成建立、設定、啟動及刪除應用程式閘道的步驟。

> [!IMPORTANT]
> 使用 Azure 資源之前，請務必了解 Azure 目前有「資源管理員」和「傳統」兩種部署模型。 在使用任何 Azure 資源之前，請先確認您了解 [部署模型和工具](../azure-classic-rm.md) 。 您可以按一下本文頂端的索引標籤，檢視不同工具的文件。 本文件會討論如何使用 Azure Resource Manager 建立應用程式閘道。 若要使用傳統的版本，請移至 [使用 PowerShell 建立應用程式閘道傳統部署](application-gateway-create-gateway.md)。

## <a name="before-you-begin"></a>開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。 您可以從 **下載頁面** 的 [Windows PowerShell](https://azure.microsoft.com/downloads/)區段下載並安裝最新版本。
1. 如果您有現有的虛擬網路，請選取現有的空白子網路，或在現有的虛擬網路中建立子網路，僅供應用程式閘道使用。 您無法將應用程式閘道部署到與您打算部署於應用程式閘道後方的資源不同的虛擬網路。
1. 您要設定來使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是已指派公用 IP/VIP。

## <a name="what-is-required-to-create-an-application-gateway"></a>建立應用程式閘道需要什麼？

* **後端伺服器集區：**後端伺服器的 IP 位址清單、FQDN 或 NIC。 如果使用 IP 位址，它們應屬於虛擬網路子網路或是公用 IP/VIP。
* **後端伺服器集區設定：** 每個集區都有一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
* **前端連接埠：**此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會到達此連接埠，然後重新導向至其中一個後端伺服器。
* **接聽程式：** 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，這些值都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
* **規則：** 規則會繫結接聽程式和後端伺服器集區，並定義當流量到達特定接聽程式時，應該導向到哪個後端伺服器集區。

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源管理員的資源群組

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，可在 [搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)取得。

1. 登入 Azure 並輸入您的認證。

  ```powershell
  Login-AzureRmAccount
  ```

2. 檢查帳戶的訂用帳戶。

  ```powershell
  Get-AzureRmSubscription
  ```

3. 選擇要使用哪一個 Azure 訂用帳戶。

  ```powershell
  Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
  ```

4. 建立資源群組 (若使用現有的資源群組，請略過此步驟)。

  ```powershell
  New-AzureRmResourceGroup -Name ContosoRG -Location "West US"
  ```

Azure Resource Manager 需要所有的資源群組指定一個位置。 此位置用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立了名為 **ContosoRG** 且位於 [美國東部] 的資源群組。

> [!NOTE]
> 如果您需要為應用程式閘道設定自訂探查，請造訪：[使用 PowerShell 建立具有自訂探查的應用程式閘道](application-gateway-create-probe-ps.md)。 請參閱 [自訂探查和健全狀況監視](application-gateway-probe-overview.md) 以取得詳細資訊。


## <a name="create-the-application-gateway-configuration-objects"></a>建立應用程式閘道組態物件

建立應用程式閘道之前，必須先設定所有組態項目。 下列步驟會建立應用程式閘道資源所需的組態項目。

```powershell
# Create a subnet and assign the address space of 10.0.0.0/24
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network with the address space of 10.0.0.0/16 and add the subnet
$vnet = New-AzureRmVirtualNetwork -Name ContosoVNET -ResourceGroupName ContosoRG -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the newly created subnet
$subnet=$vnet.Subnets[0]

# Create a public IP address that is used to connect to the application gateway. Application Gateway does not support custom DNS names on public IP addresses.  If a custom name is required for the public endpoint, a CNAME record should be created to point to the automatically generated DNS name for the public IP address.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName ContosoRG -name publicIP01 -location "East US" -AllocationMethod Dynamic

# Create a gateway IP configuration. The gateway picks up an IP addressfrom the configured subnet and routes network traffic to the IP addresses in the backend IP pool. Keep in mind that each instance takes one IP address.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Configure a backend pool with the addresses of your web servers. These backend pool members are all validated to be healthy by probes, whether they are basic probes or custom probes.  Traffic is then routed to them when requests come into the application gateway. Backend pools can be used by multiple rules within the application gateway, which means one backend pool could be used for multiple web applications that reside on the same host.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Configure backend http settings to determine the protocol and port that is used when sending traffic to the backend servers. Cookie-based sessions are also determined by the backend HTTP settings.  If enabled, cookie-based session affinity sends traffic to the same backend as previous requests for each packet.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

# Configure a frontend port that is used to connect to the application gateway through the public IP address
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

# Configure the frontend IP configuration with the public IP address created earlier.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Configure the listener.  The listener is a combination of the front end IP configuration, protocol, and port and is used to receive incoming network traffic. 
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Configure a basic rule that is used to route traffic to the backend servers. The backend pool settings, listener, and backend pool created in the previous steps make up the rule. Based on the criteria defined traffic is routed to the appropriate backend.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU for the application gateway, this determines the size and whether or not WAF is used.
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Create the application gateway
$appgw = New-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

完成時，從附加至應用程式閘道的公用 IP 資源，擷取應用程式閘道的 DNS 和 VIP 詳細資料。

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName ContosoRG
```

## <a name="delete-the-application-gateway"></a>刪除應用程式閘道

下列範例會刪除應用程式閘道。

```powershell
# Retrieve the application gateway
$gw = Get-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG

# Stops the application gateway
Stop-AzureRmApplicationGateway -ApplicationGateway $gw

# Once the application gateway is in a stopped state, use the `Remove-AzureRmApplicationGateway` cmdlet to remove the service.
Remove-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG -Force
```

> [!NOTE]
> **-force** 參數可用來隱藏移除確認訊息。

若要確認已移除服務，您可以使用 `Get-AzureRmApplicationGateway` Cmdlet。 這不是必要步驟。

```powershell
Get-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG
```

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 做法是使用連接至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 這可透過 Azure DNS 或其他 DNS 提供者完成，方法是建立一筆指向[公用 IP 位址](../dns/dns-custom-domain.md#public-ip-address)的 CNAME 記錄。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。

> [!NOTE]
> 在服務啟動時，系統會將 IP 位址指派至應用程式閘道。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName ContosoRG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : ContosoRG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/ContosoAppGateway/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="delete-all-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請完成下列步驟︰

```powershell
Remove-AzureRmResourceGroup -Name ContosoRG
```

## <a name="next-steps"></a>後續步驟

如果您想要設定 SSL 卸載，請造訪：[設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想要將應用程式閘道設為與內部負載平衡器搭配使用，請造訪：[建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

如果您想進一步了解一般負載平衡選項，請造訪：

* [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)
