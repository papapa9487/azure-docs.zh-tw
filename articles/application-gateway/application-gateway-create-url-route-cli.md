---
title: "使用 URL 路由規則建立應用程式閘道 - Azure CLI 2.0 | Microsoft Docs"
description: "本頁面提供如何使用 URL 路由規則建立和設定應用程式閘道的指示。"
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
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c617006bcb122cd3191f5da3ff08191e3c55b04b
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>將 Azure CLI 2.0 與路徑型路由搭配使用以建立應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

使用 URL 路徑型路由，您可以根據 HTTP 要求的 URL 路徑來關聯路由。 它會檢查是否有路由連至針對「應用程式閘道」中的 URL 清單設定的後端伺服器集區，並將網路流量傳送至定義的集區。 URL 路徑型路由的常見用法是將不同內容類型的要求負載平衡至不同的後端伺服器集區。

Azure 應用程式閘道使用兩種規則類型：基本和 URL 路徑型規則。 基本規則類型會為後端集區提供循環配置資源服務。 路徑型規則，除了循環配置資源發佈以外，還可以使用要求 URL 的路徑模式來選擇適當的後端集區。

## <a name="scenario"></a>案例

在下列範例中，應用程式閘道會利用兩個後端伺服器集區來為 contoso.com 提供流量：預設伺服器集區和映像伺服器集區。

http://contoso.com/image* 的要求會路由至映像伺服器集區 (**imagesBackendPool**)。 如果路徑模式不符合，應用程式閘道會選取預設的伺服器集區 (**appGatewayBackendPool**)。

![URL 路由](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>登入 Azure

開啟 [Microsoft Azure 命令提示字元] 並登入：

```azurecli
az login -u "username"
```

> [!NOTE]
> 您也可以使用 `az login` 而不搭配會要求在 aka.ms/devicelogin 輸入代碼的裝置登入參數。

輸入上述命令後，您會收到代碼。 在瀏覽器中移至 https://aka.ms/devicelogin 以繼續登入程序。

![顯示裝置登入的 Cmd][1]

在瀏覽器中，輸入收到的程式碼。 這會重新導向登入頁面。

![要輸入代碼的瀏覽器][2]

輸入代碼進行登入，然後關閉瀏覽器以繼續。

![已順利登入][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>將路徑型規則新增至現有應用程式閘道

下列步驟示範如何將路徑型規則新增至現有的應用程式閘道。
### <a name="create-a-new-back-end-pool"></a>建立新的後端集區

為後端集區中負載平衡的網路流量，設定應用程式閘道設定 **imagesBackendPool**。 在此範例中，您會針對新的後端集區設定不同的後端集區設定。 每個後端集區都可以有它自己的設定。 路徑型規則會使用後端 HTTP 設定，將流量路由傳送至正確的後端集區成員。 這會決定將流量傳送至後端集區成員時使用的通訊協定和連接埠。 後端 HTTP 設定也可決定以 Cookie 為基礎的工作階段。  啟用時，Cookie 型工作階段親和性會如每個封包的先前要求將流量至相同的後端。

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>建立應用程式閘道的新前端連接埠

接聽程式會使用前端連接埠組態物件來定義應用程式閘道會接聽哪個連接埠以取得接聽程式上的流量。

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>建立新的接聽程式

這個步驟會針對用來接收連入網路流量的公用 IP 位址和連接埠設定接聽程式。 下列範例會採用先前設定的前端 IP 設定、前端連接埠設定，以及通訊協定 (http 或 https，有區分大小寫)，並設定接聽程式。 在此範例中，接聽程式會接聽稍早在此案例中建立之公用 IP 位址上連接埠 82 的 HTTP 流量。

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>建立 URL 路徑對應

這個步驟會設定應用程式閘道用來定義路徑間對應的相對 URL 路徑，而且會指派後端集區來處理傳入流量。

> [!IMPORTANT]
> 每個路徑都必須以 "/" 開頭，而且只有結尾允許使用星號。 有效範例包括 /xyz、/xyz* 或 /xyz/*。 傳送給路徑比對器的字串未在第一個 "?" 或 "#" 之後包含任何文字，而這些字元是不允許的。 

下列範例會為將流量路由傳送至後端 **imagesBackendPool** 的 /images/* 路徑建立簡單的規則。 此規則確保每一組 URL 的流量都路由傳送至後端。 例如，http://adatum.com/images/figure1.jpg 會傳送至 **imagesBackendPool**。 如果路徑不符合任何預先定義的路徑規則，規則路徑對應組態也會設定預設的後端位址集區。 例如，http://adatum.com/shoppingcart/test.html 會傳送至 **pool1**，因為它定義為不相符流量的預設集區。

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>後續步驟

如果您想要了解「安全通訊端層」(SSL) 卸載，請參閱[設定適用於 SSL 卸載的應用程式閘道](application-gateway-ssl-cli.md)。


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png

