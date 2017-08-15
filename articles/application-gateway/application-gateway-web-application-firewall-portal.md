---
title: "使用 Web 應用程式防火牆建立或更新 Azure 應用程式閘道 | Microsoft Docs"
description: "了解如何使用入口網站以 Web 應用程式防火牆建立應用程式閘道"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 650f26d19615d27a94f3947aad7b7904b6c1fabc
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>使用入口網站以 Web 應用程式防火牆建立應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

了解如何建立已啟用 Web 應用程式防火牆的應用程式閘道。

Azure 應用程式閘道中的 Web 應用程式防火牆 (WAF) 可保護 Web 應用程式，不致遭受常見的 Web 型攻擊，例如 SQL 插入式攻擊、跨網站指令碼攻擊和工作階段攔截。 Web 應用程式可防止許多 OWASP 前 10 大常見 Web 弱點。

## <a name="scenarios"></a>案例

本文有兩個案例︰

在第一個案例中，您會了解如何[使用 Web 應用程式防火牆來建立應用程式閘道](#create-an-application-gateway-with-web-application-firewall)

在第二個案例中，您會了解如何[對現有應用程式閘道新增 Web 應用程式防火牆](#add-web-application-firewall-to-an-existing-application-gateway)。

![案例範例][scenario]

> [!NOTE]
> 其他應用程式閘道組態 (包括自訂健康狀況探查、後端集區位址及其他規則) 會在設定應用程式閘道設定之後才進行設定，而不會在初始部署期間設定。

## <a name="before-you-begin"></a>開始之前

「Azure 應用程式閘道」需要有自己的子網路。 建立虛擬網路時，請確定您保留足夠的位址空間，以便擁有多個子網路。 將應用程式閘道部署到子網路之後，就只能將額外的應用程式閘道新增到該子網路。

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a> 對現有應用程式閘道新增 Web 應用程式防火牆

此範例會更新現有應用程式閘道，以便在防止模式中支援 Web 應用程式防火牆。

1. 在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 按一下 [所有資源] 刀鋒視窗中的應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選...] 方塊中輸入名稱， 輕鬆地存取 DNS 區域。

   ![建立應用程式閘道][1]

1. 按一下 [Web 應用程式防火牆]，然後更新應用程式閘道設定。 完成時，按一下 [儲存] 

    用來更新現有應用程式閘道以支援 Web 應用程式防火牆的設定如下：

   | **設定** | **值** | **詳細資料**
   |---|---|---|
   |**升級至 WAF 層**| 已檢查 | 這會將應用程式閘道層設為 WAF 層。|
   |**防火牆狀態**| 已啟用 | 此設定會在 WAF 上啟用防火牆。|
   |**防火牆模式** | 預防 | 此設定可指定 Web 應用程式防火牆處理惡意流量的方式。 **偵測**模式只會記錄事件，**防止**模式則會記錄事件並停止惡意流量。|
   |**規則集**|3.0|此設定會決定用來保護後端集區成員的[核心規則集](application-gateway-web-application-firewall-overview.md#core-rule-sets)。|
   |**設定已停用的規則**|視情況而異|若要防止可能發生誤判，此設定可讓您停用某些[規則與規則群組](application-gateway-crs-rulegroups-rules.md)。|

    >[!NOTE]
    > 將現有的應用程式閘道升級至 WAF SKU 時，SKU 大小會變更為 [中型]。 這可以在設定完成之後重新設定。

    ![顯示基本設定的刀鋒視窗][2-1]

    > [!NOTE]
    > 若要檢視 Web 應用程式防火牆記錄檔，必須啟用診斷功能並選取 ApplicationGatewayFirewallLog。 若要進行測試，可以選擇執行個體計數 1。 請務必了解 SLA 不涵蓋任何低於兩個執行個體的執行個體計數，因此不建議使用。 如果使用 Web 應用程式防火牆，便無法使用小型閘道。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>使用 Web 應用程式防火牆來建立應用程式閘道

此案例將會：

* 建立含有兩個執行個體的中型 Web 應用程式防火牆應用程式閘道。
* 建立名為 AdatumAppGatewayVNET 且含有 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
* 建立名為 Appgatewaysubnet 且使用 10.0.0.0/28 做為其 CIDR 區塊的子網路。
* 為 SSL 卸載設定憑證。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 如果您沒有帳戶，可以註冊[免費試用一個月](https://azure.microsoft.com/free)
1. 在入口網站的 [我的最愛] 窗格中，按一下 [新增]
1. 在 [新增] 刀鋒視窗中，按一下 [網路]。 在 [網路] 刀鋒視窗中，按一下 [應用程式閘道]，如下圖中所示：
1. 瀏覽至 Azure 入口網站，按一下 [新增]  >  [網路]  >  [應用程式閘道]

    ![建立應用程式閘道][1]

1. 在顯示的 [基本] 刀鋒視窗中，輸入下列值，然後按一下 [確定]：

   | **設定** | **值** | **詳細資料**
   |---|---|---|
   |**名稱**|AdatumAppGateway|應用程式閘道的名稱|
   |**層級**|WAF|可用的值為「標準」或 WAF。 若要深入了解 WAF，請瀏覽 [Web 應用程式防火牆](application-gateway-web-application-firewall-overview.md)。|
   |**SKU 大小**|中型|當選擇「標準」層級時，選項為 [小型]、[中型] 和 [大型]。 當選擇 WAF 層級時，選項只有 [中型] 和 [大型]。|
   |**執行個體計數**|2|高可用性的應用程式閘道執行個體數目。 執行個體計數 1 應僅用於測試目的。|
   |**訂用帳戶**|[您的訂用帳戶]|選取要在其中建立應用程式閘道的訂用帳戶。|
   |**資源群組**|**建立新項目：**AdatumAppGatewayRG|建立資源群組。 資源群組名稱在您選取的訂用帳戶中必須是唯一的。 若要深入了解資源群組，請閱讀 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) 概觀。|
   |**位置**|美國西部||

   ![顯示基本設定的刀鋒視窗][2-2]

1. 在 [虛擬網路] 底下顯示的 [設定] 刀鋒視窗中，按一下 [選擇虛擬網路]。 此步驟會開啟進入 [選擇虛擬網路] 刀鋒視窗。  按一下 [建立新項目] 以開啟 [建立虛擬網路] 刀鋒視窗。

   ![選擇虛擬網路][2]

1. 在 [建立虛擬網路] 刀鋒視窗中輸入下列值，然後按一下 [確定]。 此步驟會關閉 [建立虛擬網路] 和 [選擇虛擬網路] 刀鋒視窗。 這會使用所選的子網路，填入 [設定] 刀鋒視窗的 [子網路] 欄位。

   |**設定** | **值** | **詳細資料** |
   |---|---|---|
   |**名稱**|AdatumAppGatewayVNET|應用程式閘道的名稱|
   |**位址空間**|10.0.0.0/16| 此值是虛擬網路的位址空間|
   |**子網路名稱**|AppGatewaySubnet|應用程式閘道的子網路名稱|
   |**子網路位址範圍**|10.0.0.0/28 | 這個子網路允許在虛擬網路中有更多其他的子網路，以供後端集區成員使用|

1. 在 [前端 IP 設定] 底下的 [設定] 刀鋒視窗中，選擇 [公用] 作為 [IP 位址類型]

1. 在 [公用 IP 位址] 底下的 [設定] 刀鋒視窗中，按一下 [選擇公用 IP 位址]。此步驟會開啟 [選擇公用 IP 位址] 刀鋒視窗，然後按一下 [建立新項目]。

   ![選擇公用 IP][3]

1. 在 [建立公用 IP 位址] 刀鋒視窗中，接受預設值，並按一下 [確定]。 此步驟會關閉 [選擇公用 IP 位址] 刀鋒視窗、[建立公用 IP 位址] 刀鋒視窗，並使用所選的公用 IP 位址填入 [公用 IP 位址]。

1. 在 [接聽程式設定] 底下的 [設定] 刀鋒視窗中，按一下 [通訊協定] 底下的 [HTTP]。 若要使用 **https**，必須提供憑證。 需要有憑證的私密金鑰，因此必須提供憑證的 .pfx 匯出及檔案的密碼。

1. 設定 **WAF** 特定設定。

   |**設定** | **值** | **詳細資料** |
   |---|---|---|
   |**防火牆狀態**| 已啟用| 此設定會開啟或關閉 WAF。|
   |**防火牆模式** | 預防| 此設定會決定 WAF 對惡意流量採取的動作。 如果選擇 **偵測** ，只會記錄流量。  如果選擇 [防止]，則會記錄並停止流量，且產生「403 未經授權」的回應。|


1. 檢閱 [摘要] 頁面，然後按一下 [確定] 。  現在應用程式閘道已排入佇列並建立。

1. 建立應用程式閘道之後，請在入口網站中瀏覽至該閘道，以繼續設定應用程式閘道組態。

    ![應用程式閘道資源檢視][10]

這些步驟會建立一個具有接聽程式、後端集區、後端 http 設定及規則之預設設定的基本應用程式閘道。 佈建成功之後，您可以依據您的部署需求修改這些設定。

> [!NOTE]
> 基於保護的目的，使用基本 Web 應用程式防火牆設定建立的應用程式閘道，是使用 CRS 3.0 所設定。

## <a name="next-steps"></a>後續步驟

接下來，您可以了解如何使用 Azure DNS 或其他 DNS 提供者，為[公用 IP 位址](../dns/dns-custom-domain.md#public-ip-address)設定自訂網域別名。

請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)，以了解如何設定診斷記錄，以及如何記錄 Web 應用程式防火牆偵測到或防止的事件

參閱 [建立自訂健康狀態探查](application-gateway-create-probe-portal.md)

參閱 [設定 SSL 卸載](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

