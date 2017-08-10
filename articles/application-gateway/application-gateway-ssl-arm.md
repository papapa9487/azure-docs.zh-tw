---
title: "設定 SSL 卸載 - Azure 應用程式閘道 - PowerShell | Microsoft Docs"
description: "本頁面提供使用 Azure 資源管理員範本，建立具有 SSL 卸載之應用程式閘道的指示。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 21a45f8ac5b4cb5fd0f5513fb43f7ca263977393
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>使用 Azure 資源管理員設定適用於 SSL 的應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure 應用程式閘道可以設定為在閘道終止安全通訊端層 (SSL) 工作階段，以避免 Web 伺服陣列發生高成本的 SSL 解密工作。 SSL 卸載也可以簡化 Web 應用程式的前端伺服器設定和管理。

## <a name="before-you-begin"></a>開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。 您可以從 **下載頁面** 的 [Windows PowerShell](https://azure.microsoft.com/downloads/)區段下載並安裝最新版本。
2. 建立應用程式閘道的虛擬網路和子網路。 請確定沒有虛擬機器或是雲端部署正在使用子網路。 應用程式閘道必須單獨在虛擬網路子網路中。
3. 您要設定來使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是已指派公用 IP/VIP。

## <a name="what-is-required-to-create-an-application-gateway"></a>建立應用程式閘道需要什麼？

* **後端伺服器集區：** 後端伺服器的 IP 位址清單。 列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。
* **後端伺服器集區設定：** 每個集區都包括一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
* **前端連接埠：** 此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會達到此連接埠，然後重新導向至其中一個後端伺服器。
* **接聽程式：** 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，這些設定都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
* **規則：** 規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。 目前只支援 *基本* 規則。 *基本* 規則是循環配置資源的負載分配。

**其他組態注意事項**

針對 SSL 憑證組態，**HttpListener** 中的通訊協定應該變更為 *Https* (區分大小寫)。 將 **SslCertificate** 元素新增至 **HttpListener**，並針對 SSL 憑證設定變數值。 前端連接埠應該更新為 443。

**啟用以 Cookie 為基礎的同質性**：您可以設定應用程式閘道，以確保來自用戶端工作階段的要求一律會導向至 Web 伺服陣列中的相同 VM。 此案例透過插入允許閘道適當導向流量的工作階段 Cookie 來完成。 若要啟用以 Cookie 為基礎的同質性，請在 **BackendHttpSettings** 元素中將 **CookieBasedAffinity** 設定為 *Enabled*。

## <a name="create-an-application-gateway"></a>建立應用程式閘道

使用 Azure 傳統部署模型和 Azure Resource Manager 的差別，在於您建立應用程式閘道和需設定項目的順序。

透過 Resource Manager，應用程式閘道的所有元件會個別進行設定，然後放在一起建立應用程式閘道資源。

以下是建立應用程式閘道所需的步驟：

1. 建立資源管理員的資源群組
2. 建立應用程式閘道的虛擬網路、子網路和公用 IP
3. 建立應用程式閘道組態物件
4. 建立應用程式閘道資源

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源管理員的資源群組

請確定您切換 PowerShell 模式以使用 Azure 資源管理員 Cmdlet。 如需詳細資訊，可在 [搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)取得。

### <a name="step-1"></a>步驟 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

系統會提示使用您的認證進行驗證。

### <a name="step-3"></a>步驟 3

選擇要使用哪一個 Azure 訂用帳戶。

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>步驟 4

建立資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure 資源管理員需要所有的資源群組指定一個位置。 此設定用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 **appgw-RG** 的資源群組，且位置為 **West US** (美國西部)。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立應用程式閘道的虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路：

### <a name="step-1"></a>步驟 1

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

此範例會將位址範圍 10.0.0.0/24 指派給用於建立虛擬網路的子網路變數。

### <a name="step-2"></a>步驟 2

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

此範例會使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24，在美國西部 (West US) 區域的 **appgw-rg** 資源群組中建立名為 **appgwvnet** 的虛擬網路。

### <a name="step-3"></a>步驟 3

```powershell
$subnet = $vnet.Subnets[0]
```

此範例會將子網路物件指派給下一個步驟的變數 $subnet。

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

此範例會在美國西部區域的 **appgw-rg** 資源群組中建立公用 IP 資源 **publicIP01**。

## <a name="create-an-application-gateway-configuration-object"></a>建立應用程式閘道組態物件

### <a name="step-1"></a>步驟 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

此範例會建立名為 **gatewayIP01** 的應用程式閘道 IP 組態。 當「應用程式閘道」啟動時，它會從已設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

### <a name="step-2"></a>步驟 2

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
```

此範例會設定名為 **pool01** 的後端 IP 位址集區，其 IP 位址有 **134.170.185.46**、**134.170.188.221**、**134.170.185.50**。 這些值為 IP 位址，可接收來自前端 IP 端點的網路流量。 以您的 Web 應用程式端點的 IP 位址取代前述範例中的 IP 位址。

### <a name="step-3"></a>步驟 3

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
```

此範例會設定後端集區中負載平衡網路流量的應用程式閘道設定 **poolsetting01**。

### <a name="step-4"></a>步驟 4

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
```

此範例會設定公用 IP 端點的前端 IP 連接埠 **frontendport01**。

### <a name="step-5"></a>步驟 5

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
```

此範例會設定 SSL 連接所使用的憑證。 憑證必須是 .pfx 格式，而密碼則必須介於 4 到 12 個字元。

### <a name="step-6"></a>步驟 6

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

此範例會建立名為 **fipconfig01** 的前端 IP 組態，並將公用 IP 位址與前端 IP 組態產生關聯。

### <a name="step-7"></a>步驟 7

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

此範例會建立名為 **listener01** 的接聽程式，並將前端連接埠與前端 IP 組態和憑證產生關聯。

### <a name="step-8"></a>步驟 8

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

此範例會建立名為 **rule01** 的負載平衡器路由規則，設定負載平衡器的行為。

### <a name="step-9"></a>步驟 9

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

此範例會設定應用程式閘道的執行個體大小。

> [!NOTE]
> *InstanceCount* 的預設值是 2，且最大值是 10。 GatewaySize  的預設值是 Medium。 您可以在 Standard_Small、Standard_Medium 和 Standard_Large 之間選擇。

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>使用 New-AzureApplicationGateway 建立應用程式閘道

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert
```

此範例利用上述步驟中的所有組態項目來建立應用程式閘道。 範例中的應用程式閘道名為 **appgwtest**。

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 [在 Azure 中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 做法是使用連接至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 應用程式閘道的 DNS 名稱應該用來建立將兩個 Web 應用程式指向此 DNS 名稱的 CNAME 記錄。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。


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

如果您想要將應用程式閘道設為與內部負載平衡器 (ILB) 搭配使用，請參閱 [建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

如果您想進一步了解一般負載平衡選項，請參閱：

* [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)


