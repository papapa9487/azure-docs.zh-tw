---
title: "建立應用程式閘道 - Azure CLI 2.0 | Microsoft Docs"
description: "了解如何使用資源管理員中的 Azure CLI 2.0 建立應用程式閘道。"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 9d3732d538f3ed9ecb87247f378a3736692025ca
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 建立應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 範本](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

「Azure 應用程式閘道」是專用的虛擬設備，會以服務形式提供應用程式傳遞控制器 (ADC)，為您的應用程式提供各種第 7 層負載平衡功能。

## <a name="cli-versions"></a>CLI 版本

您可以使用下列其中一個命令列介面 (CLI) 版本來建立應用程式閘道：

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md)：適用於傳統和 Azure Resource Manager 部署模型的 Azure CLI
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md)：適用於資源管理員部署模型的新一代 CLI

## <a name="prerequisite-install-the-azure-cli-20"></a>必要條件：安裝 Azure CLI 2.0

若要執行本文的步驟，您需要[安裝適用於 macOS、Linux 和 Windows 的 Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。

> [!NOTE]
> 您需要 Azure 帳戶以建立應用程式閘道。 如果您沒有帳戶，請註冊 [免費試用](../active-directory/sign-up-organization.md)。

## <a name="scenario"></a>案例

在此案例中，您將了解如何使用 Azure 入口網站來建立應用程式閘道。

此案例將會：

* 建立含有兩個執行個體的中型應用程式閘道。
* 建立名為 AdatumAppGatewayVNET 且含有 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
* 建立名為 Appgatewaysubnet 且使用 10.0.0.0/28 做為其 CIDR 區塊的子網路。

> [!NOTE]
> 應用程式閘道的進一步設定 (包括自訂健康狀況探查、後端集區位址及其他規則) 會在建立應用程式閘道之後才進行設定，而不會在初始部署期間設定。

## <a name="before-you-begin"></a>開始之前

應用程式閘道需要有自己的子網路。 建立虛擬網路時，請確定為多個子網路保留足夠的位址空間。 將應用程式閘道部署到子網路之後，您只能將額外的應用程式閘道新增到該子網路。

## <a name="sign-in-to-azure"></a>登入 Azure

開啟 [Microsoft Azure 命令提示字元] 並登入：

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> 您也可以使用 `az login` 而不搭配會要求在 aka.ms/devicelogin 輸入代碼的裝置登入參數。

輸入上述命令後，您會收到程式碼。 在瀏覽器中移至 https://aka.ms/devicelogin 以繼續登入程序。

![顯示裝置登入的 Cmd][1]

在瀏覽器中，輸入收到的程式碼。 這會重新導向登入頁面。

![要輸入代碼的瀏覽器][2]

輸入代碼進行登入，然後關閉瀏覽器以繼續。

![已順利登入][3]

## <a name="create-the-resource-group"></a>建立資源群組

建立應用程式閘道之前，請建立一個資源群組以包含它。 使用下列命令：

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

為您的後端伺服器 IP 位址使用後端 IP 位址。 這些值可以是虛擬網路中的私人 IP、公用 IP 或後端伺服器的完整網域名稱。 下列範例會使用 HTTP 設定、連接埠和規則的其他設定來建立應用程式閘道：

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

上述範例顯示建立應用程式閘道期間不需要的數個屬性。 下列程式碼範例會使用必要資訊來建立應用程式閘道：

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> 如需建立期間要使用的參數清單，請執行下列命令︰`az network application-gateway create --help`。

此範例會建立一個具有接聽程式、後端集區、後端 HTTP 設定及規則之預設設定的基本應用程式閘道。 佈建成功之後，您可以依據您的部署需求修改這些設定。

如果在上述步驟中已使用後端集區定義 Web 應用程式，則負載平衡會立即開始。

## <a name="get-the-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱
建立閘道後，接著設定後端以供通訊使用。 當您使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，請使用 CNAME 記錄來指向應用程式閘道的公用端點。 如需詳細資訊，請參閱[使用 Azure DNS 為 Azure 服務提供自訂網域設定](../dns/dns-custom-domain.md)。

若要設定別名，請使用連結至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 使用應用程式閘道的 DNS 名稱所建立的 CNAME 記錄，可將兩個 Web 應用程式指向此 DNS 名稱。 請勿使用 A 記錄，因為重新啟動應用程式閘道時，VIP 可能會變更。


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

## <a name="delete-all-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請執行下列命令︰

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>後續步驟

若要了解如何自訂健康情況探查，請移至[使用入口網站建立應用程式閘道的自訂探查](application-gateway-create-probe-portal.md)。

若要了解如何設定 SSL 卸載，並將昂貴的 SSL 解密從您的 Web 伺服器移除，請參閱[使用 Azure Resource Manager 設定適用於 SSL 卸載的應用程式閘道](application-gateway-ssl-arm.md)。

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
