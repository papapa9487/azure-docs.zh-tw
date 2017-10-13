---
title: "建立應用程式閘道來裝載多個站台 | Microsoft Docs"
description: "本頁面提供建立和設定 Azure 應用程式閘道以在相同閘道上裝載多個 Web 應用程式的指示。"
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: b107d647-c9be-499f-8b55-809c4310c783
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
ms.openlocfilehash: d42efa7d359f5c87c14afbfd138328b37c8ae6c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>建立應用程式閘道來裝載多個 Web 應用程式

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-multisite-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)

多站台裝載可讓您在相同的應用程式閘道上部署多個 Web 應用程式。 它需倚賴在連入的 HTTP 要求中有主機標頭存在，以判斷哪一個接聽程式要接收流量。 然後再由接聽程式將流量導向閘道的規則定義中所設定的適當後端集區。 在已啟用 SSL 功能的 Web 應用程式中，則應用程式閘道會依賴「伺服器名稱指示」(SNI) 擴充功能來選擇正確的 Web 流量接聽程式。 多站台裝載的常見用法是將不同 Web 網域的要求負載平衡至不同的後端伺服器集區。 同樣地，相同根網域的多個子網域也可以裝載在相同的應用程式閘道上。

## <a name="scenario"></a>案例

在下列範例中，應用程式閘道會利用兩個後端伺服器集區來為 contoso.com 和 fabrikam.com 的流量提供服務：contoso 伺服器集區和 fabrikam 伺服器集區。 類似的設定也可用來裝載子網域，例如 app.contoso.com 和 blog.contoso.com。

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。 您可以從 **下載頁面** 的 [Windows PowerShell](https://azure.microsoft.com/downloads/)區段下載並安裝最新版本。
2. 新增至後端集區以使用應用程式閘道的伺服器必須存在，或是在虛擬網路的個別子網路中建立其端點，或是已指派公用 IP/VIP。

## <a name="requirements"></a>需求

* **後端伺服器集區：** 後端伺服器的 IP 位址清單。 列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。 您也可以使用 FQDN。
* **後端伺服器集區設定：** 每個集區都包括一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
* **前端連接埠：** 此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會到達此連接埠，然後重新導向至其中一個後端伺服器。
* **接聽程式：** 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，這些值都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。 針對啟用多站台功能的應用程式閘道，會一併新增主機名稱和 SNI 指示器。
* **規則：**規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。 會以規則列出的順序進行處理，且不論精確性，都會透過相符的第一個規則將流量進行導向。 例如，如果您在相同的連接埠上同時使用基本接聽程式的規則和多站台接聽程式的規則，則必須將多站台接聽程式的規則列於基本接聽程式的規則之前，多站台規則才能如預期般運作。

## <a name="create-an-application-gateway"></a>建立應用程式閘道

以下是建立應用程式閘道所需的步驟：

1. 建立資源管理員的資源群組。
2. 建立應用程式閘道的虛擬網路、子網路和公用 IP。
3. 建立應用程式閘道組態物件。
4. 建立應用程式閘道資源。

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源管理員的資源群組

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，可在[搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md) 取得。

### <a name="step-1"></a>步驟 1

登入 Azure

```powershell
Login-AzureRmAccount
```
系統會提示使用您的認證進行驗證。

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>步驟 3

選擇要使用哪一個 Azure 訂用帳戶。

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>步驟 4

建立資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
New-AzureRmResourceGroup -Name appgw-RG -location "West US"
```

或者，您也可以針對應用程式閘道建立資源群組的標記：

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}
```

Azure Resource Manager 需要所有的資源群組指定一個位置。 此位置用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 **appgw-RG** 的資源群組，且位置為美國西部 (**West US**)。

> [!NOTE]
> 如果您需要設定應用程式閘道的自訂探查，請參閱 [使用 PowerShell 建立具有自訂探查的應用程式閘道](application-gateway-create-probe-ps.md)。 請造訪[自訂探查和健全狀況監視](application-gateway-probe-overview.md)以取得詳細資訊。

## <a name="create-a-virtual-network-and-subnets"></a>建立虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路。 此步驟中會建立兩個子網路。 第一個子網路用於應用程式閘道本身。 應用程式閘道需要自己的子網路來保存其執行個體。 在該子網路中只能部署其他應用程式閘道。 第二個子網路用來保存應用程式後端伺服器。

### <a name="step-1"></a>步驟 1

指派用於保存應用程式閘道的位址範圍 10.0.0.0/24 給子網路變數。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24
```
### <a name="step-2"></a>步驟 2

指派用於後端集區的位址範圍 10.0.1.0/24 給子網路 2 變數。

```powershell
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>步驟 3

使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24 和 10.0.1.0/24，在美國西部 (West US) 區域的 **appgw-rg** 資源群組中建立名為 **appgwvnet** 的虛擬網路。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2
```

### <a name="step-4"></a>步驟 4

針對建立應用程式閘道的後續步驟，指派子網路變數。

```powershell
$appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
$backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

在美國西部區域的 **appgw-rg** 資源群組中建立公用 IP 資源 **publicIP01**。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

在服務啟動時，系統會將 IP 位址指派至應用程式閘道。

## <a name="create-application-gateway-configuration"></a>建立應用程式閘道組態

您必須先設定所有組態項目，再建立應用程式閘道。 下列步驟會建立應用程式閘道資源所需的組態項目。

### <a name="step-1"></a>步驟 1

建立名為 **gatewayIP01** 的應用程式閘道 IP 組態。 當應用程式閘道啟動時，它會從已設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet
```

### <a name="step-2"></a>步驟 2

設定名為 **pool01** 和 **pool2** 的後端 IP 位址集區，**pool1** 的 IP 位址為 **134.170.185.46**、**134.170.188.221**、**134.170.185.50**，而 **pool2** 的 IP 位址為 **134.170.186.46**、**134.170.189.221**、**134.170.186.50**。

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105
```

此範例中有兩個後端集區，根據要求的網站路由傳送網路流量。 其中一個集區會接收來自 "contoso.com" 站台的流量，而另一個集區則會接收來自 "fabrikam.com" 站台的流量。 您必須取代上述 IP 位址來新增自己的應用程式 IP 位址端點。 您也可以使用公用 IP 位址、FQDN 或 VM 的後端執行個體 NIC 來取代內部 IP 位址。 若要在 PowerShell 中指定 FQDN (而不是 IP)，請使用 "-BackendFQDNs" 參數。

### <a name="step-3"></a>步驟 3

針對後端集區中負載平衡的網路流量，設定應用程式閘道設定 **poolsetting01** 和 **poolsetting02**。 在此範例中，您會針對後端集區設定不同的後端集區設定。 每個後端集區都可以有它自己的後端集區設定。

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>步驟 4

利用公用 IP 端點設定前端 IP。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>步驟 5

設定應用程式閘道的前端連接埠。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443
```

### <a name="step-6"></a>步驟 6

為我們將在此範例中支援的兩個網站設定兩個 SSL 憑證。 一個憑證用於 contoso.com 流量，另一個則用於 fabrikam.com 流量。 這些憑證應該是「憑證授權單位」為您的網站簽發的憑證。 也支援自我簽署憑證，但不建議用於生產環境流量。

```powershell
$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>
```

### <a name="step-7"></a>步驟 7

為此範例中的兩個網站設定兩個接聽程式。 此步驟會針對用來接收連入流量的公用 IP 位址、連接埠及主機設定接聽程式。 若要獲得多站台支援，必須要有 HostName 參數，並且應該設定為接收其流量的適當網站。 針對在多主機案例中需要 SSL 支援的網站，RequireServerNameIndication 參數應該設定為 true。 如果需要 SSL 支援，則您也必須指定用來保護該 Web 應用程式流量的 SSL 憑證。 FrontendIPConfiguration、FrontendPort 及 HostName 的組合對接聽程式而言必須是唯一的。 每個接聽程式可以支援一個憑證。

```powershell
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02
```

### <a name="step-8"></a>步驟 8

為此範例中的兩個 Web 應用程式建立兩個規則設定。 規則會將接聽程式、後端集區及 http 設定繫結在一起。 此步驟會設定讓應用程式閘道使用基本路由規則 (每個網站一個規則)。 每個網站的流量都會由其已設定的接聽程式接收，然後再使用 BackendHttpSettings 中指定的屬性來導向到其已設定的後端集區中。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2
```

### <a name="step-9"></a>步驟 9

設定執行個體數目和應用程式閘道的大小。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>建立應用程式閘道

利用上述步驟中的所有組態物件來建立應用程式閘道。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02
```

> [!IMPORTANT]
> 「應用程式閘道」佈建是一個長時間執行的作業，可能需要一些時間才能完成。
> 
> 

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

了解如何使用[應用程式閘道 - Web 應用程式防火牆](application-gateway-webapplicationfirewall-overview.md)保護您的網站

