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
ms.date: 06/20/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ac6c629ceaf1a8036643f593ce3d7ef9ea096ef8
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>以 Azure CLI 在新的或現有的應用程式閘道上設定 Web 應用程式防火牆

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

了解如何建立已啟用 Web 應用程式防火牆的應用程式閘道，或將 Web 應用程式防火牆新增至現有應用程式閘道。

Azure 應用程式閘道中的 Web 應用程式防火牆 (WAF) 可保護 Web 應用程式，不致遭受常見的 Web 型攻擊，例如 SQL 插入式攻擊、跨網站指令碼攻擊和工作階段攔截。

Azure 應用程式閘道是第 7 層負載平衡器。 不論是在雲端或內部部署中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。 應用程式閘道提供許多應用程式傳遞控制器 (ADC) 功能，包括 HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、安全通訊端層 (SSL) 卸載、自訂健康狀態探查、多網站支援，以及許多其他功能。 若要尋找完整的支援功能清單，請瀏覽：[應用程式閘道概觀](application-gateway-introduction.md)。

下列文章說明如何[將 Web 應用程式防火牆新增至現有應用程式閘道](#add-web-application-firewall-to-an-existing-application-gateway)和[建立使用 Web 應用程式防火牆的應用程式閘道](#create-an-application-gateway-with-web-application-firewall)。

![案例影像][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>必要條件：安裝 Azure CLI 2.0

若要執行本文的步驟，您需要[安裝適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。

## <a name="waf-configuration-differences"></a>WAF 組態差異

如果您已經讀過[使用 Azure CLI 建立應用程式閘道](application-gateway-create-gateway-cli.md)，您會了解建立應用程式閘道時要設定的 SKU 設定。 在應用程式閘道上設定 SKU 時，WAF 會提供其他可定義的設定。 您對應用程式閘道本身不會進行任何其他變更。

| **設定** | **詳細資料**
|---|---|
|**SKU** |不具有 WAF 的一般應用程式閘道支援 **Standard\_Small**、**Standard\_Medium** 和 **Standard\_Large** 大小。 WAF 推出後另外出現了兩個 SKU，分別是 **WAF\_Medium** 和 **WAF\_Large**。 小型應用程式閘道不支援 WAF。|
|**模式** | 此設定是 WAF 的模式。 允許的值為**偵測**和**防止**。 當 WAF 設定為偵測模式時，所有威脅會儲存在記錄檔中。 在防止模式時仍會記錄事件，但攻擊者會從應用程式閘道收到 403 未經授權的回應。|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>對現有應用程式閘道新增 Web 應用程式防火牆

下列命令會將現有的標準應用程式閘道變更為啟用 WAF 的應用程式閘道。

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

此命令會更新具有 Web 應用程式防火牆的應用程式閘道。 請瀏覽[應用程式閘道診斷](application-gateway-diagnostics.md)，了解如何檢視應用程式閘道中的記錄檔。 由於 WAF 的安全性本質，您必須定期檢閱記錄檔，以了解 Web 應用程式的安全性狀態。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>建立具有 Web 應用程式防火牆的應用程式閘道

下列命令會建立具有 Web 應用程式防火牆的應用程式閘道。

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> 基於保護的目的，使用基本 Web 應用程式防火牆設定建立的應用程式閘道，是使用 CRS 3.0 所設定。

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 [在 Azure 中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 若要設定 CNAME 記錄，使用連接至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 應用程式閘道的 DNS 名稱應該用來建立將兩個 Web 應用程式指向此 DNS 名稱的 CNAME 記錄。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
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

了解如何自訂 WAF 規則，請瀏覽：[透過 Azure CLI 2.0 自訂 Web 應用程式防火牆規則](application-gateway-customize-waf-rules-cli.md)。

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png

