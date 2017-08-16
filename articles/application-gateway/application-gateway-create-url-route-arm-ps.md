---
title: "使用 URL 路由規則建立應用程式閘道 |Microsoft Docs"
description: "本頁面提供使用 URL 路由規則建立和設定 Azure 應用程式閘道的指示。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ba756d3262b9780c5701e69faad860ba32bba08b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-using-path-based-routing"></a>使用路徑型路由建立應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

以 URL 路徑為基礎的路由可讓您根據 Http 要求的 URL 路徑來關聯路由。 它會檢查應用程式閘道中是否有路由指向 URL 設定的後端集區。 然後將網路流量傳送到已定義的後端集區。 URL 型路由的常見用法是將不同內容類型的要求負載平衡至不同的後端伺服器集區。

URL 型路由會將新的規則類型引進應用程式閘道。 應用程式閘道具有 2 種規則類型：基本和 PathBasedRouting。 基本規則類型會針對後端集區提供循環配置資源服務，而 PathBasedRouting 除了循環配置資源發佈之外也會在選擇後端集區時將要求 URL 的路徑模式納入考慮。

## <a name="scenario"></a>案例

在下列範例中，應用程式閘道會利用兩個後端伺服器集區來為 contoso.com 提供流量：視訊伺服器集區和映像伺服器集區。

對 http://contoso.com/image* 的要求會路由傳送至影像伺服器集區 (pool1)，而 http://contoso.com/video* 則會路由傳送至影片伺服器集區 (pool2)。 如果沒有任何路徑模式相符，則會選取預設的伺服器集區 (pool1)。

![URL 路由](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。 您可以從 **下載頁面** 的 [Windows PowerShell](https://azure.microsoft.com/downloads/)區段下載並安裝最新版本。
2. 您會建立應用程式閘道的虛擬網路和子網路。 請確定沒有虛擬機器或是雲端部署正在使用子網路。 應用程式閘道必須單獨位於虛擬網路子網路中。
3. 加入後端集區以使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是指派公用 IP/VIP。

## <a name="what-is-required-to-create-an-application-gateway"></a>建立應用程式閘道需要什麼？

* **後端伺服器集區：** 後端伺服器的 IP 位址清單。 列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。
* **後端伺服器集區設定：** 每個集區都包括一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
* **前端連接埠：** 此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會到達此連接埠，然後重新導向至其中一個後端伺服器。
* **接聽程式：** 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，這些值都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
* **規則：** 規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。

## <a name="create-an-application-gateway"></a>建立應用程式閘道

使用「Azure 傳統」和「Azure Resource Manager」的差別，在於您建立應用程式閘道和需設定項目的順序。

透過 Resource Manager，組成應用程式閘道的所有項目會個別進行設定，然後一併建立應用程式閘道資源。

以下是建立應用程式閘道所需的步驟：

1. 建立資源管理員的資源群組。
2. 建立應用程式閘道的虛擬網路、子網路和公用 IP。
3. 建立應用程式閘道組態物件。
4. 建立應用程式閘道資源。

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源管理員的資源群組

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

### <a name="step-1"></a>步驟 1

登入 Azure

```powershell
Login-AzureRmAccount
```

系統會提示使用您的認證進行驗證。<BR>

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>步驟 3

選擇要使用哪一個 Azure 訂用帳戶。 <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>步驟 4

建立資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

或者，您也可以針對應用程式閘道建立資源群組的標記：

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure Resource Manager 需要所有的資源群組指定一個位置。 此資源群組用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 "appgw-RG" 的資源群組，且位置為美國西部 ("West US")。

> [!NOTE]
> 如果您需要為應用程式閘道設定自訂探查，請參閱 [使用 PowerShell 建立具有自訂探查的應用程式閘道](application-gateway-create-probe-ps.md)。 請參閱 [自訂探查和健全狀況監視](application-gateway-probe-overview.md) 以取得詳細資訊。
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立應用程式閘道的虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路。 這個範例會建立應用程式閘道的 VNET。 應用程式閘道需要自己的子網路，因此針對應用程式閘道建立的子網路會小於 VNET 位址空間。 這允許其他資源，包括但不限於要在相同 VNET 中設定的 Web 伺服器。

### <a name="step-1"></a>步驟 1

指派用於建立虛擬網路的位址範圍 10.0.0.0/24 給子網路變數。  這會為下一個範例使用的應用程式閘道建立子網路設定物件。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>步驟 2

使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24，在美國西部 (West US) 區域的 **appgw-rg** 資源群組中建立名為 **appgwvnet** 的虛擬網路。 這樣就完成 VNET 的設定，有單一子網路放置應用程式閘道。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>步驟 3

指派後續步驟的子網路變數，這會在未來的步驟中傳遞至 `New-AzureRMApplicationGateway` Cmdlet。

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

在美國西部區域的 **appgw-rg** 資源群組中建立公用 IP 資源 **publicIP01**。 應用程式閘道可以使用公用 IP 位址、內部 IP 位址或兩者來接收進行負載平衡的要求。  此範例中僅使用公用 IP 位址。 在下列範例中，未設定 DNS 名稱以建立公用 IP 位址。  應用程式閘道在不支援公用 IP 位址上自訂 DNS 名稱。  如果公用端點需要自訂名稱，應該建立 CNAME 記錄以指向針對公用 IP 位址自動產生的 DNS 名稱。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

在服務啟動時，系統會將 IP 位址指派至應用程式閘道。

## <a name="create-application-gateway-configuration"></a>建立應用程式閘道組態

建立應用程式閘道之前，必須先設定所有組態項目。 下列步驟會建立應用程式閘道資源所需的組態項目。

### <a name="step-1"></a>步驟 1

建立名為 **gatewayIP01** 的應用程式閘道 IP 組態。 當「應用程式閘道」啟動時，它會從已設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>步驟 2

設定名為 **pool01** 和 **pool2** 的後端 IP 位址集區，並指定 **pool1** 和 **pool2** 的 IP 位址。 這些 IP 位址是託管 Web 應用程式 (由應用程式閘道保護) 之資源的 IP 位址。 這些後端集區成員都由探查 (無論是基本探查或自訂探查) 驗證為健康狀態。  當要求進入應用程式閘道時，流量便會路由至它們。 後端集區可在應用程式閘道內供多個規則使用，這表示一個後端集區可用於位在相同主機上的多個 web 應用程式。

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

此範例中有兩個後端集區，根據 URL 路徑路由傳送網路流量。 有一個集區會接收來自 URL 路徑 "/video" 的流量，而另一個集區會接收來自路徑 "/image" 的流量。 取代上述 IP 位址來新增自己的應用程式 IP 位址端點。 

### <a name="step-3"></a>步驟 3

針對後端集區中負載平衡的網路流量，設定應用程式閘道設定 **poolsetting01** 和 **poolsetting02**。 在此範例中，您會針對後端集區設定不同的後端集區設定。 每個後端集區都可以有它自己的後端集區設定。  規則會使用後端 HTTP 設定，將流量路由傳送至正確的後端集區成員。 這會決定將流量傳送至後端集區成員時使用的通訊協定和連接埠。 Cookie 型工作階段也是由後端 HTTP 設定決定。  啟用時，Cookie 型工作階段親和性會如每個封包的先前要求將流量至相同的後端。

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>步驟 4

利用公用 IP 端點設定前端 IP。 接聽程式會使用前端 IP 設定物件，將對外 IP 位址與接聽程式相關聯。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>步驟 5

設定應用程式閘道的前端連接埠。 接聽程式會使用前端連接埠組態物件來定義應用程式閘道會接聽哪個連接埠以取得接聽程式上的流量。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>步驟 6

設定接聽程式。 這個步驟會針對用來接收連入網路流量的公用 IP 位址和連接埠設定接聽程式。 下列範例會採用先前設定的前端 IP 組態、前端連接埠組態及通訊協定 (http 或 https)，並設定接聽程式。 在此範例中，接聽程式會接聽稍早建立的公用 IP 位址上連接埠 80 的 HTTP 流量。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>步驟 7

設定後端集區的 URL 規則路徑。 這個步驟會設定應用程式閘道所使用的相對路徑，並定義 URL 路徑和已指派的後端集區之間的對應，以處理傳入流量。

> [!IMPORTANT]
> 每個路徑都必須以 / 開頭，而且只有結尾允許使用 "\*"。 有效範例包括 /xyz、/xyz* 或 /xyz/*。 傳送給路徑比對器的字串未在第一個 "?" 或 "#" 之後包含任何文字，而這些字元是不允許的。 

下列範例會建立兩個規則：一個適用於將流量路由傳送到後端 "pool1" 的 "/image/" 路徑，另一個則適用於將流量路由傳送到後端 "pool2" 的 "/video/" 路徑。 這些規則確保每一組 url 的流量都路由傳送至後端。 例如，http://contoso.com/image/figure1.jpg 會傳送至 pool1，http://contoso.com/video/example.mp4 會傳送至 pool2。

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

如果路徑不符合任何預先定義的路徑規則，規則路徑對應組態也會設定預設的後端位址集區。 例如，http://contoso.com/shoppingcart/test.html 會傳送至 pool1，因為它定義為不相符流量的預設集區。

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>步驟 8

建立規則設定。 這個步驟會設定應用程式閘道來使用 URL 路徑型路由。 稍早步驟中定義的 `$urlPathMap` 變數，現在用來建立以路徑為基礎的規則。 在此步驟中，我們將規則與接聽程式和稍早建立的 url 路徑對應相關聯。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>步驟 9

設定執行個體數目和應用程式閘道的大小。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>建立應用程式閘道

利用上述步驟中的所有組態物件來建立應用程式閘道。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 [在 Azure 中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 若要設定前端 IP CNAME 記錄，使用連接至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 應用程式閘道的 DNS 名稱應該用於建立 CNAME 記錄。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。

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

如果您想要了解「安全通訊端層」(SSL) 卸載，請參閱 [設定適用於 SSL 卸載的應用程式閘道](application-gateway-ssl-arm.md)。


