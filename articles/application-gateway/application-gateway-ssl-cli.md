---
title: "設定 SSL 卸載 - Azure 應用程式閘道 - Azure CLI 2.0 | Microsoft Docs"
description: "本文提供的指示，說明如何使用 Azure CLI 2.0 來建立具有 SSL 卸載的應用程式閘道"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 032a514ddab625e4f7c5ef23a1da03a0162f43e3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 設定適用於 SSL 卸載的應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure 應用程式閘道可以設定為在閘道終止安全通訊端層 (SSL) 工作階段，以避免 Web 伺服陣列發生高成本的 SSL 解密工作。 SSL 卸載也可簡化在前端伺服器的憑證管理。

## <a name="prerequisite-install-the-azure-cli-20"></a>必要條件：安裝 Azure CLI 2.0

若要執行本文中的步驟，您必須[安裝適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。

## <a name="required-components"></a>必要的元件

* **後端伺服器集區**：後端伺服器的 IP 位址清單。 列出的 IP 位址應屬於虛擬網路子網路或是公用 IP 位址或虛擬 IP 位址 (VIP)。
* **後端伺服器集區設定**：每個集區都包括一些設定，例如連接埠、通訊協定和 Cookie 親和性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
* **前端連接埠**：此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會到達此連接埠，然後重新導向至其中一個後端伺服器。
* **接聽程式：**接聽程式具有前端連接埠、通訊協定 (Http 或 Https；這些設定都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
* **規則**：規則會繫結接聽程式和後端伺服器集區，並定義流量叫用特定接聽程式時，應該導向至哪個後端伺服器集區。 目前只支援 *基本* 規則。 *基本* 規則是循環配置資源的負載分配。

**其他組態注意事項**

針對 SSL 憑證組態，**HttpListener** 中的通訊協定應該變更為 *Https* (區分大小寫)。 將 **SslCertificate** 元素新增至 **HttpListener**，並針對 SSL 憑證設定變數值。 前端連接埠應該更新為 **443**。

**啟用 Cookie 親和性**：您可以設定應用程式閘道，以確保來自用戶端工作階段的要求一律會導向至 Web 伺服陣列中的相同 VM。 若要完成這項作業，請插入允許閘道適當導向流量的工作階段 Cookie。 若要啟用以 Cookie 為基礎的同質性，請在 **BackendHttpSettings** 元素中將 **CookieBasedAffinity** 設定為 *Enabled*。

## <a name="configure-ssl-offload-on-an-existing-application-gateway"></a>在現有的應用程式閘道上設定 SSL 卸載

輸入下列命令可將 SSL 卸載設定為現有的應用程式閘道：

```azurecli-interactive
#!/bin/bash

# Create a new front end port to be used for SSL
az network application-gateway frontend-port create \
  --name sslport \
  --port 443 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Upload the .pfx certificate for SSL offload
az network application-gateway ssl-cert create \
  --name "newcert" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new listener referencing the port and certificate created earlier
az network application-gateway http-listener create \
  --frontend-ip "appGatewayFrontendIP" \
  --frontend-port sslport  \
  --name sslListener \
  --ssl-cert newcert \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new back-end pool to be used
az network application-gateway address-pool create \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG" \
  --name "appGatewayBackendPool2" \
  --servers 10.0.0.7 10.0.0.8

# Create a new back-end HTTP settings using the new probe
az network application-gateway http-settings create \
  --name "settings2" \
  --port 80 \
  --cookie-based-affinity Enabled \
  --protocol "Http" \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new rule linking the listener to the back-end pool
az network application-gateway rule create \
  --name "rule2" \
  --rule-type Basic \
  --http-settings settings2 \
  --http-listener ssllistener \
  --address-pool temp1 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

```

## <a name="create-an-application-gateway-with-ssl-offload"></a>建立具有 SSL 卸載的應用程式閘道

下列範例會建立具有 SSL 卸載的應用程式閘道。 憑證和憑證密碼，都必須更新為有效的私密金鑰。

```azurecli-interactive
#!/bin/bash

# Creates an application gateway with SSL offload
az network application-gateway create \
  --name "AdatumAppGateway3" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG2" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet" \
  --subnet-address-prefix "10.0.0.0/28" \
  --frontend-port 443 \
  --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 \
  --sku "Standard_Small" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip" \
  --public-ip-address-allocation "dynamic"
```

## <a name="get-an-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。  使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 如需詳細資訊，請參閱[在 Azure 中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 

若要設定別名，請使用連結至應用程式閘道的 **PublicIPAddress** 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 使用應用程式閘道的 DNS 名稱所建立的 CNAME 記錄，可將兩個 Web 應用程式指向此 DNS 名稱。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>後續步驟

如果您需要設定要與內部負載平衡器搭配使用的應用程式閘道，請參閱[建立具有內部負載平衡器的應用程式閘道](application-gateway-ilb.md)。

如需一般負載平衡選項的詳細資訊，請參閱：

* [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)

