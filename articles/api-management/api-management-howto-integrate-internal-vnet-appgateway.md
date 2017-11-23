---
title: "如何搭配使用虛擬網路中的 Azure API 管理與應用程式閘道 | Microsoft Docs"
description: "了解如何以應用程式閘道 (WAF) 做為前端在內部虛擬網路中安裝和設定 Azure API 管理"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: e138241139329b8bb956157ab55b7d22dc2a9b67
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2017
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>整合內部 VNET 中的 API 管理與應用程式閘道 

##<a name="overview"></a>概觀
 
API 管理服務可以內部模式設定於虛擬網路中，因此只能從虛擬網路中加以存取。 Azure 應用程式閘道是一項 PAAS 服務，可提供第 7 層負載平衡器。 它可做為反向 Proxy 服務，並在其供應項目中提供 Web 應用程式防火牆 (WAF)。

結合使用內部 VNET 中佈建的 API 管理與應用程式閘道前端，可實現下列案例︰

* 使用相同的 API 管理資源供內部取用者和外部取用者取用。
* 使用單一 API 管理資源，並在 API 管理中定義一部分 API 供外部取用者使用。
* 提供周全的方法來開啟和關閉從公用網際網路對 API 管理的存取權。 

## <a name="prerequisites"></a>必要條件

若要執行本文所述的步驟，您必須具有：

+ 有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 執行個體。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。

##<a name="scenario"></a>案例
本文討論如何將單一 API 管理服務用於內部和外部取用者，並使之成為內部部署和雲端 API 的單一前端。 您也會看到使用應用程式閘道中提供的 PathBasedRouting 功能，只公開您 API 的一部分 (在範例中以綠色醒目提示) 供外部取用。

在第一個設定範例中，您所有的 API 只能從虛擬網路內部進行管理。 內部取用者 (以橘色醒目提示) 則可存取所有的內部和外部 API。 流量永遠不會傳出網際網路，高效能會透過 Expressroute 電路傳送。

![URL 路由](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a>開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。 您可以從 **下載頁面** 的 [Windows PowerShell](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)區段下載並安裝最新版本。
2. 建立虛擬網路，並為 API 管理和應用程式閘道建立個別子網路。 
3. 如果您想要建立虛擬網路的自訂 DNS 伺服器，請在開始部署之前進行。 確保在新的子網路中建立虛擬網路以再次確認它的運作方式，虛擬機器可以解析並存取所有 Azure 服務端點。

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>在 API 管理和應用程式閘道之間建立整合的所需條件為何？

* **後端伺服器集區︰**這是 API 管理服務的內部虛擬 IP 位址。
* **後端伺服器集區設定：** 每個集區都包括一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。 這些設定會套用至集區內所有伺服器。
* **前端連接埠：**這是在應用程式閘道上開啟的公用連接埠。 到達的流量會重新導向至其中一個後端伺服器。
* **接聽程式：** 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，這些值都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
* **規則︰**規則會繫結接聽程式至後端伺服器集區。
* **自訂健全狀況探查︰**應用程式閘道預設會使用 IP 位址型探查，來找出 BackendAddressPool 中有哪些伺服器正在作用中。 API 管理服務只會回應具有正確主機標頭的要求，因此預設探查會失敗。 需要定義自訂的健全狀況探查以協助應用程式閘道判斷服務正在執行，因此它應該轉送要求。
* **自訂網域憑證︰**若要從網際網路存取 API 管理，您需要建立其主機名稱和應用程式閘道前端 DNS 名稱的 CNAME 對應。 這可確保主機名稱的標頭和憑證傳送到轉送至 API 管理的應用程式閘道，是 APIM 可以辨識為有效的。

## <a name="overview-steps"></a>整合 API 管理和應用程式閘道的所需步驟 

1. 建立資源管理員的資源群組。
2. 建立應用程式閘道的虛擬網路、子網路和公用 IP。 為 API 管理建立其他子網路。
3. 在上面所建立的 VNET 子網路內建立 API 管理服務，並確保您使用內部模式。
4. 在 API 管理服務中設定自訂網域名稱。
5. 建立應用程式閘道組態物件。
6. 建立應用程式閘道資源。
7. 從應用程式閘道的公用 DNS 名稱建立 CNAME 到 API 管理 Proxy 主機名稱。

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源管理員的資源群組

確定您使用最新版本的 Azure PowerShell。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與資源管理員](https://docs.microsoft.com/en-us/azure/azure-resource-manager/powershell-azure-resource-manager)。

### <a name="step-1"></a>步驟 1

登入 Azure

```powershell
Login-AzureRmAccount
```

使用您的認證進行驗證。<BR>

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶並加以選取。

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>步驟 3

建立資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Azure Resource Manager 需要所有的資源群組指定一個位置。 這用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立應用程式閘道的虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路。

### <a name="step-1"></a>步驟 1

對要在建立虛擬網路時用於應用程式閘道的子網路變數指派位址範圍 10.0.0.0/24。

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>步驟 2

對要在建立虛擬網路時用於 API 管理的子網路變數指派位址範圍 10.0.1.0/24。

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>步驟 3

使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24 和 10.0.1.0/24，在美國西部 (West US) 區域的 **apim-appGw-RG** 資源群組中建立名為 **appgwvnet** 的虛擬網路。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>步驟 4

指派子網路變數以供後續步驟使用

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>在以內部模式設定的 VNET 內建立 API 管理服務

下列範例示範如何以僅針對內部存取設定的 VNET 建立 API 管理服務。

### <a name="step-1"></a>步驟 1
使用上面所建立的子網路 $apimsubnetdata 建立 API 管理虛擬網路物件。

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>步驟 2
在虛擬網路內建立 API 管理服務。

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
上述命令執行成功之後，請參閱[要存取內部 VNET API 管理服務所需的 DNS 組態](api-management-using-with-internal-vnet.md#apim-dns-configuration)來存取它。

## <a name="set-up-a-custom-domain-name-in-api-management"></a>在 API 管理中設定自訂網域名稱

### <a name="step-1"></a>步驟 1
使用網域的私密金鑰上傳憑證。 這個範例中就會是 `*.contoso.net`。 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>步驟 2
一旦上傳憑證後，以 `api.contoso.net` 的主機名稱建立 proxy 的主機名稱組態物件，如範例憑證提供 `*.contoso.net` 網域的授權。 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

在美國西部區域的 **apim-appGw-RG** 資源群組中建立公用 IP 資源 **publicIP01**。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

在服務啟動時，系統會將 IP 位址指派至應用程式閘道。

## <a name="create-application-gateway-configuration"></a>建立應用程式閘道組態

建立應用程式閘道之前，必須先設定所有組態項目。 下列步驟會建立應用程式閘道資源所需的組態項目。

### <a name="step-1"></a>步驟 1

建立名為 **gatewayIP01** 的應用程式閘道 IP 組態。 當「應用程式閘道」啟動時，它會從已設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>步驟 2

設定公用 IP 端點的前端 IP 連接埠。 此連接埠是供使用者連接到的連接埠。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>步驟 3

利用公用 IP 端點設定前端 IP。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>步驟 4

設定應用程式閘道的憑證，可用來解密和重新加密流過之流量。

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>步驟 5

建立應用程式閘道的 HTTP 接聽程式。 指派前端 IP 組態、連接埠和 SSL 憑證給它。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>步驟 6

建立 API 管理服務 `ContosoApi` Proxy 網域端點的自訂探查。 `/status-0123456789abcdef` 路徑是裝載於所有 API 管理服務上的預設健全狀況端點。 將 `api.contoso.net` 設為自訂探查主機名稱，利用 SSL 憑證來保護它。

> [!NOTE]
> 主機名稱 `contosoapi.azure-api.net` 則是名為 `contosoapi` 的服務在公用 Azure 中建立時所設定的預設 Proxy 主機名稱。 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>步驟 7

上傳要在已啟用 SSL 的後端集區資源上使用的憑證。 此憑證與您在上述步驟 4 中提供的憑證相同。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>步驟 8

設定應用程式閘道的 HTTP 後端設定。 這包括設定之後會取消的後端要求逾時限制。 此值與探查逾時不同。

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>步驟 9

以上面建立的 API 管理服務內部虛擬 IP 位址設定名為 **apimbackend** 的後端 IP 位址集區。

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>步驟 10

建立虛擬 (不存在) 後端的設定。 不想要透過應用程式閘道從 API 管理公開的 API 路徑要求將會叫用這個後端，並傳回 404。

設定虛擬後端的 HTTP 設定。

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

設定虛擬後端 **dummyBackendPool**，以指向 FQDN 位址 **dummybackend.com**。這個 FQDN 位址不存在於虛擬網路中。

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

建立應用程式閘道預設將使用的規則設定，以指向虛擬網路中不存在的後端 **dummybackend.com**。

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>步驟 11

設定後端集區的 URL 規則路徑。 這可以從要公開的 API 管理中只選取某些 API。 例如，如果有 `Echo API` (/echo/)、`Calculator API` (/calc/) 等，則讓 `Echo API` 只能從網際網路存取。 

下列範例會為將流量路由傳送至後端「apimProxyBackendPool」的「/echo/」路徑建立簡單的規則。

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

如果路徑不符合我們想要從 API 管理啟用的路徑規則，則規則路徑對應設定也會設定名為 **dummyBackendPool** 的預設後端位址集區。 例如，http://api.contoso.net/calc/* 會前往 **dummyBackendPool**，因為它定義為不相符流量的預設集區。

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

上面的步驟可確保只有「"/echo"」路徑的要求可以通過應用程式閘道。 對 API 管理中所設定之其他 API 的要求，則會在從網際網路存取時，從應用程式閘道擲回 404 錯誤。 

### <a name="step-12"></a>步驟 12

建立規則設定以供應用程式閘道使用 URL 路徑型路由。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>步驟 13

設定執行個體數目和應用程式閘道的大小。 在這裡，我們使用 [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) 以提高 API 管理資源的安全性。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>步驟 14

將 WAF 設定為「防止」模式。
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>建立應用程式閘道

利用上述步驟中的所有組態物件來建立應用程式閘道。

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>將 API 管理 Proxy 主機名稱 CNAME 到應用程式閘道資源的公用 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (可能不易記住)。 

您應該使用應用程式閘道的 DNS 名稱來建立 CNAME 記錄，將 APIM Proxy 主機名稱 (例如，上面範例中的 `api.contoso.net`) 指向此 DNS 名稱。 若要設定前端 IP CNAME 記錄，使用 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 不建議使用 A-records，因為重新啟動閘道時，VIP 可能會變更。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"></a>摘要
VNET 中設定的 Azure API 針對所有管理的 API 管理提供了單一閘道介面，不論它們是裝載在內部部署環境或雲端中。 整合應用程式閘道與 API 管理提供選擇性地使特定 API 可在網際網路上存取的彈性，並提供 Web 應用程式防火牆來做為 API 管理執行個體的前端。

##<a name="next-steps"></a>後續步驟
* 深入了解 Azure 應用程式閘道
  * [應用程式閘道概觀](../application-gateway/application-gateway-introduction.md)
  * [應用程式閘道 Web 應用程式防火牆](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [使用路徑型路由的應用程式閘道](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* 深入了解 API 管理和 VNET
  * [使用只在 VNET 內提供的 API 管理](api-management-using-with-internal-vnet.md)
  * [在 VNET 中使用 API 管理](api-management-using-with-vnet.md)
