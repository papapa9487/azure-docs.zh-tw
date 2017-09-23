---
title: "使用 Web 應用程式防火牆建立或更新應用程式閘道 | Microsoft Docs"
description: "了解如何使用入口網站以 Web 應用程式防火牆建立應用程式閘道"
services: application-gateway
documentationcenter: na
author: davidmu1
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
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: e747d4777bdf515e8b7bd54f44ff48b8539ed765
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>使用入口網站以 Web 應用程式防火牆建立應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

了解如何建立已啟用 Web 應用程式防火牆 (WAF) 的應用程式閘道。

Azure 應用程式閘道中的 WAF 可保護 Web 應用程式，免於遭受常見的 Web 型攻擊，例如 SQL 插入式攻擊、跨網站指令碼攻擊和工作階段攔截。 WAF 可防止許多 OWASP 前 10 大常見 Web 弱點。

## <a name="scenarios"></a>案例

本文提供兩個案例。 在第一個案例中，您會了解如何[使用 WAF 來建立應用程式閘道](#create-an-application-gateway-with-web-application-firewall)。 在第二個案例中，您會了解如何[對現有應用程式閘道新增 WAF](#add-web-application-firewall-to-an-existing-application-gateway)。

![案例範例][scenario]

> [!NOTE]
> 您可以將自訂健康情況探查、後端集區位址，以及其他規則新增至應用程式閘道。 設定應用程式閘道之後，且並非在初始部署期間，會設定這些應用程式。

## <a name="before-you-begin"></a>開始之前

 應用程式閘道需要有自己的子網路。 建立虛擬網路時，請確定您保留足夠的位址空間，以便擁有多個子網路。 將應用程式閘道部署到子網路之後，就只能將額外的應用程式閘道新增到該子網路。

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>對現有應用程式閘道新增 Web 應用程式防火牆

此範例會更新現有應用程式閘道，以便在**防止**模式中支援 WAF。

1. 在 Azure 入口網站的 [我的最愛] 窗格中，選取 [所有資源]。 在 [所有資源] 刀鋒視窗中，選取現有的應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入名稱，輕易地存取 DNS 區域。

   ![現有應用程式閘道選取項目][1]

2. 選取 [Web 應用程式防火牆]，然後更新應用程式閘道設定。 當更新完成時，選取 [儲存]。 

3. 使用下列設定來更新現有應用程式閘道以支援 WAF：

   | **設定** | **值** | **詳細資料**
   |---|---|---|
   |**升級至 WAF 層**| 已檢查 | 這個選項會將應用程式閘道層設為 WAF 層。|
   |**防火牆狀態**| 已啟用 | 此設定會在 WAF 上啟用防火牆。|
   |**防火牆模式** | 預防 | 此設定可指定 WAF 處理惡意流量的方式。 **偵測**模式只會記錄事件。 **防止**模式則會記錄事件並停止惡意流量。|
   |**規則集**|3.0|此設定會決定用來保護後端集區成員的[核心規則集](application-gateway-web-application-firewall-overview.md#core-rule-sets)。|
   |**設定已停用的規則**|視情況而異|若要防止可能發生誤判，您可以使用此設定來停用某些[規則與規則群組](application-gateway-crs-rulegroups-rules.md)。|

    >[!NOTE]
    > 將現有的應用程式閘道升級至 WAF SKU 時，SKU 大小會變更為 [中型]。 設定完成之後，您可以重新設定此設定。

    ![基本設定][2-1]

    > [!NOTE]
    > 若要檢視 WAF 記錄，請啟用診斷並且選取 **ApplicationGatewayFirewallLog**。 僅針對測試目的，選擇執行個體計數 **1**。 不建議 **2** 以下的執行個體計數，因為 SLA 未涵蓋。 當您使用 WAF 時，小型閘道無法使用。

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>建立具有 Web 應用程式防火牆的應用程式閘道

此案例將會：

* 建立含有兩個執行個體的中型 WAF 應用程式閘道。
* 建立名為 AdatumAppGatewayVNET 且含有 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
* 建立名為 Appgatewaysubnet 且使用 10.0.0.0/28 做為其 CIDR 區塊的子網路。
* 為 SSL 卸載設定憑證。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 如果您沒有帳戶，可以註冊[免費試用一個月](https://azure.microsoft.com/free)。

2. 在入口網站的 [我的最愛] 窗格中，選取 [新增]。

3. 在 [新增] 刀鋒視窗中，選取 [網路]。 在 [網路] 刀鋒視窗中，選取 [應用程式閘道]，如下圖中所示：

    ![應用程式閘道建立][1]

4. 在顯示的 [基本] 刀鋒視窗中，輸入下列值，然後選取 [確定]：

   | **設定** | **值** | **詳細資料**
   |---|---|---|
   |**名稱**|AdatumAppGateway|應用程式閘道的名稱。|
   |**層級**|WAF|可用的值為「標準」或 WAF。 若要深入了解 WAF，請參閱 [Web 應用程式防火牆](application-gateway-web-application-firewall-overview.md)。|
   |**SKU 大小**|中型|標準層的選項為 [小型]、[中型] 和 [大型]。 WAF 層的選項只有 [中型] 和 [大型]。|
   |**執行個體計數**|2|高可用性的應用程式閘道執行個體數目。 僅針對測試目的，使用執行個體計數 1。|
   |**訂用帳戶**|[您的訂用帳戶]|選取要用來建立應用程式閘道的訂用帳戶。|
   |**資源群組**|**建立新項目：**AdatumAppGatewayRG|建立資源群組。 資源群組名稱在您選取的訂用帳戶中必須是唯一的。 若要深入了解資源群組，請閱讀 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) 概觀。|
   |**位置**|美國西部||

   ![基本設定][2-2]

5. 在 [虛擬網路] 底下顯示的 [設定] 刀鋒視窗中，選取 [選擇虛擬網路]。 在 [選擇虛擬網路] 刀鋒視窗中，選取 [建立新項目]。

   ![虛擬網路選擇][2]

6. 在 [建立虛擬網路] 刀鋒視窗中輸入下列值，然後選取 [確定]。 這會使用所選的子網路，填入 [設定] 刀鋒視窗的 [子網路] 欄位。

   |**設定** | **值** | **詳細資料** |
   |---|---|---|
   |**名稱**|AdatumAppGatewayVNET|應用程式閘道的名稱。|
   |**位址空間**|10.0.0.0/16| 此值是虛擬網路的位址空間。|
   |**子網路名稱**|AppGatewaySubnet|應用程式閘道的子網路名稱。|
   |**子網路位址範圍**|10.0.0.0/28 | 這個子網路允許在虛擬網路中有更多其他的子網路，以供後端集區成員使用。|

7. 在 [前端 IP 設定] 底下的 [設定] 刀鋒視窗中，選取 [公用] 作為 [IP 位址類型]。

8. 在 [公用 IP 位址] 底下的 [設定] 刀鋒視窗中，選取 [選擇公用 IP 位址]。 在 [選擇公用 IP 位址] 刀鋒視窗中﹐選取 [建立新項目]。

   ![公用 IP 位址選擇][3]

9. 在 [建立公用 IP 位址] 刀鋒視窗中，接受預設值，然後選取 [確定]。 會以您所選擇的公用 IP 位址填入 [公用 IP 位址] 欄位。

10. 在 [接聽程式設定] 底下的 [設定] 刀鋒視窗中，選取 [通訊協定] 底下的 [HTTP]。 需要憑證才能使用 **HTTPS**。 需要憑證的私密金鑰 。 提供憑證的 .pfx 匯出，然後輸入檔案的密碼。

11. 設定 **WAF** 的特定設定。

   |**設定** | **值** | **詳細資料** |
   |---|---|---|
   |**防火牆狀態**| 已啟用| 此設定會開啟或關閉 WAF。|
   |**防火牆模式** | 預防| 此設定會決定 WAF 對惡意流量採取的動作。 **偵測**模式只會記錄流量。 **防止**模式會記錄流量，並且停止 403 未授權回應的流量。|


12. 檢閱 [摘要] 分頁，然後選取 [確定]。 現在應用程式閘道已排入佇列並建立。

13. 建立應用程式閘道之後，請在入口網站中移至該閘道，以繼續設定應用程式閘道。

    ![應用程式閘道資源檢視][10]

這些步驟會建立一個具有接聽程式、後端集區、後端 HTTP 設定及規則之預設設定的基本應用程式閘道。 佈建成功完成之後，您可以依據您的部署需求修改這些設定。

> [!NOTE]
> 基於保護的目的，使用基本 WAF 設定建立的應用程式閘道，是使用 CRS 3.0 來設定。

## <a name="next-steps"></a>後續步驟

若要為[公用 IP 位址](../dns/dns-custom-domain.md#public-ip-address)設定自訂網域別名，您可以使用 Azure DNS 或其他 DNS 提供者。

若要設定診斷記錄，以記錄 WAF 偵測到或防止的事件，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

若要建立自訂健康情況探查，請參閱[建立自訂健康情況探查](application-gateway-create-probe-portal.md)。

若要設定 SSL 卸載，並且將昂貴的 SSL 訂用帳戶從您的 Web 伺服器移除，請參閱[設定 SSL 卸載](application-gateway-ssl-portal.md)。

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

