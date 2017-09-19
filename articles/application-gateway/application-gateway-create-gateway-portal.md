---
title: "建立應用程式閘道 - Azure 入口網站 | Microsoft Docs"
description: "了解如何使用入口網站來建立「應用程式閘道」"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: d3c39cfe3159cd4059a81f966fb551175188278b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/01/2017

---
# <a name="create-an-application-gateway-with-the-portal"></a>使用入口網站建立應用程式閘道

[應用程式閘道](application-gateway-introduction.md)是專用的虛擬設備，會以服務形式提供應用程式傳遞控制器 (ADC)，為您的應用程式提供各種第 7 層負載平衡功能。 本文會引導您逐步進行使用 Azure 入口網站建立應用程式閘道，以及新增現有的伺服器作為後端成員。

## <a name="log-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，網址是 [http://portal.azure.com](http://portal.azure.com)

## <a name="create-application-gateway"></a>建立應用程式閘道

若要建立應用程式閘道，您需完成下列步驟。 應用程式閘道需要有自己的子網路。 建立虛擬網路時，請確定您保留足夠的位址空間，以便擁有多個子網路。 將應用程式閘道部署到子網路之後，只有其他應用程式閘道可以新增至其中。

1. 在入口網站的 [我的最愛] 窗格中，按一下 [新增]
1. 在 [新增] 刀鋒視窗中，按一下 [網路]。 在 [網路] 刀鋒視窗中，按一下 [應用程式閘道]，如下圖中所示：

    ![建立應用程式閘道][1]

1. 在顯示的 [基本] 刀鋒視窗中，輸入下列值，然後按一下 [確定]：

   | **設定** | **值** | **詳細資料**|
   |---|---|---|
   |**名稱**|AdatumAppGateway|應用程式閘道的名稱|
   |**層級**|標準|可用的值為「標準」或 WAF。 若要深入了解 WAF，請瀏覽 [Web 應用程式防火牆](application-gateway-web-application-firewall-overview.md)。|
   |**SKU 大小**|中型|當選擇「標準」層級時，選項為 [小型]、[中型] 和 [大型]。 當選擇 WAF 層級時，選項只有 [中型] 和 [大型]。|
   |**執行個體計數**|2|高可用性的應用程式閘道執行個體數目。 執行個體計數 1 應僅用於測試目的。|
   |**訂用帳戶**|[您的訂用帳戶]|選取要在其中建立應用程式閘道的訂用帳戶。|
   |**資源群組**|**建立新項目：**AdatumAppGatewayRG|建立資源群組。 資源群組名稱在您選取的訂用帳戶中必須是唯一的。 若要深入了解資源群組，請閱讀 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) 概觀。|
   |**位置**|美國西部||

1. 在 [虛擬網路] 底下顯示的 [設定] 刀鋒視窗中，按一下 [選擇虛擬網路]。 [選擇虛擬網路] 刀鋒視窗隨即開啟。  按一下 [建立新項目] 以開啟 [建立虛擬網路] 刀鋒視窗。

   ![選擇虛擬網路][2]

1. 在 [建立虛擬網路] 刀鋒視窗中輸入下列值，然後按一下 [確定]。 [建立虛擬網路] 和 [選擇虛擬網路] 刀鋒視窗會關閉。 這個步驟會使用所選的子網路，填入 [設定] 刀鋒視窗的 [子網路] 欄位。

   | **設定** | **值** | **詳細資料**|
   |---|---|---|
   |**名稱**|AdatumAppGatewayVNET|應用程式閘道的名稱|
   |**位址空間**|10.0.0.0/16|這是虛擬網路的位址空間|
   |**子網路名稱**|AppGatewaySubnet|應用程式閘道的子網路名稱|
   |**子網路位址範圍**|10.0.0.0/28|這個子網路允許在虛擬網路中有更多其他的子網路，以供後端集區成員使用|

1. 在 [前端 IP 設定] 底下的 [設定] 刀鋒視窗中，選擇 [公用] 作為 [IP 位址類型]

1. 在 [公用 IP 位址] 底下的 [設定] 刀鋒視窗中，按一下 [選擇公用 IP 位址]，[選擇公用 IP 位址] 刀鋒視窗隨即開啟，然後按一下 [建立新項目]。

   ![選擇公用 IP][3]

1. 在 [建立公用 IP 位址] 刀鋒視窗中，接受預設值，並按一下 [確定]。 刀鋒視窗會關閉，並且以選擇的公用 IP 位址填入 [公用 IP 位址]。

1. 在 [接聽程式設定] 底下的 [設定] 刀鋒視窗中，按一下 [通訊協定] 底下的 [HTTP]。 輸入要在 [連接埠] 欄位中使用的連接埠。

2. 按一下 [設定] 刀鋒視窗上的 [確定] 以繼續。

1. 檢閱 [摘要] 刀鋒視窗上的設定，然後按一下 [確定] 以開始建立應用程式閘道。 建立應用程式閘道是漫長的工作，而且需要一段時間才能完成。

## <a name="add-servers-to-backend-pools"></a>將伺服器新增到後端集區

建立應用程式閘道後，仍需將裝載要進行負載平衡之應用程式的系統新增至應用程式閘道。 這些伺服器的 IP 位址、FQDN 或 NIC 會新增至後端位址集區。

### <a name="ip-address-or-fqdn"></a>IP 位址或 FQDN

1. 建立應用程式閘道之後，在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 按一下 [所有資源] 刀鋒視窗中的 [AdatumAppGateway] 應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入 **AdatumAppGateway** 輕鬆地存取應用程式閘道。

1. 系統會顯示您建立的應用程式閘道。 按一下 [後端集區]，然後選取目前的後端集區 [appGatewayBackendPool]，[appGatewayBackendPool] 刀鋒視窗隨即開啟。

   ![應用程式閘道後端集區][4]

1. 按一下 [新增目標] 以新增 FQDN 值的 IP 位址。 選擇 [IP 位址或 FQDN] 當作 [類型]，並在欄位中輸入您的 IP 位址或 FQDN。 針對其他的後端集區成員重複這個步驟。 完成時，按一下 [儲存]。

### <a name="virtual-machine-and-nic"></a>虛擬機器和 NIC

您也可以將虛擬機器 NIC 新增為後端集區成員。 只有與應用程式閘道相同的虛擬網路內的虛擬機器可透過下拉式清單來取得。

1. 建立應用程式閘道之後，在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 按一下 [所有資源] 刀鋒視窗中的 [AdatumAppGateway] 應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入 **AdatumAppGateway** 輕鬆地存取應用程式閘道。

1. 系統會顯示您建立的應用程式閘道。 按一下 [後端集區]，然後選取目前的後端集區 [appGatewayBackendPool]，[appGatewayBackendPool] 刀鋒視窗隨即開啟。

   ![應用程式閘道後端集區][5]

1. 按一下 [新增目標] 以新增 FQDN 值的 IP 位址。 選擇 [虛擬機器] 當作 [類型]，並選取要使用的虛擬機器和 NIC。 完成時，按一下 [儲存]

   > [!NOTE]
   > 下拉式方塊中只提供位於應用程式閘道相同虛擬網路內的虛擬機器。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、應用程式閘道和所有相關資源。 若要這樣做，請選取應用程式閘道刀鋒視窗中的資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在此案例中，您部署應用程式閘道，並且將伺服器新增至後端。 後續步驟用於設定應用程式閘道，方法是修改設定，以及調整閘道中的規則。 瀏覽下列文章即可找到這些步驟：

參閱 [建立自訂健康狀態探查](application-gateway-create-probe-portal.md)

參閱 [設定 SSL 卸載](application-gateway-ssl-portal.md)

了解如何使用應用程式閘道的功能 - [Web 應用程式防火牆](application-gateway-webapplicationfirewall-overview.md)來保護您的應用程式。

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

