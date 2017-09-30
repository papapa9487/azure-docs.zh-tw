---
title: "在 Azure 中的多個 IP 組態上進行負載平衡 | Microsoft Docs"
description: "在主要和次要 IP 組態間進行負載平衡。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8c0fc8d11a872b99fee2efa3a32a9e1ccce67f3c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="load-balancing-on-multiple-ip-configurations-using-the-azure-portal"></a>使用 Azure 入口網站在多個 IP 組態上進行負載平衡

> [!div class="op_single_selector"]
> * [入口網站](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

本文說明如何對次要網路介面 (NIC) 上的多個 IP 位址使用 Azure Load Balancer。在此案例中，我們有兩部執行 Windows 的 VM，每部各有一個主要和次要 NIC。 每個次要 NIC 都有兩個 IP 組態。 每部 VM 都裝載 contoso.com 和 fabrikam.com 兩個網站。每個網站繫結到次要 NIC 上的其中一個 IP 組態。 我們使用 Azure Load Balancer 來公開兩個前端 IP 位址，每個網站各使用其中一個，以將流量分散給網站的個別 IP 組態。 此案例會在兩個前端以及兩個後端集區 IP 位址使用相同的連接埠號碼。

![LB 案例影像](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

##<a name="prerequisites"></a>必要條件
此範例假設您有一個名為 contosofabrikam 的資源群組，其具有下列組態︰
 -  在名為 myAvailset 的同一個可用性設定組內，包含一個名為 myVNet 的虛擬網路，和兩個名稱分別為 VM1 和 VM2 的 VM。 
 - 每個 VM 各有主要 NIC 和次要 NIC。 主要 NIC 名為 VM1NIC1 和 VM2NIC1，次要 NIC 名為 VM1NIC2 和 VM2NIC2。 如需如何建立具有多個 NIC 之 VM 的詳細資訊，請參閱[使用 PowerShell 建立具有多個 NIC 的 VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)。

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>在多個 IP 組態上進行負載平衡的步驟

請完成下列步驟來達成本文所述案例︰

### <a name="step-1-configure-the-secondary-nics-for-each-vm"></a>步驟 1︰設定每個 VM 的次要 NIC

為虛擬網路中的每個 VM，新增次要 NIC 的 IP 組態，如下所示︰  

1. 透過瀏覽器瀏覽至 Azure 入口網站 http://portal.azure.com，並使用您的 Azure 帳戶登入。
2. 在畫面左側按一下 [資源群組] 圖示，然後按一下 VM 所位在的資源群組 (例如，contosofabrikam)。 此時會顯示 [資源群組] 刀鋒視窗，裡面列出了所有資源以及 VM 的網路介面。
3. 對每個 VM 的次要 NIC 新增 IP 組態，如下所示︰
    1. 選取要將 IP 組態新增至的網路介面。
    2. 在針對所選 NIC 出現的刀鋒視窗中，按一下 [IP 組態]。 然後按一下出現的刀鋒視窗中靠近上方的 [新增]。
    3. 在 [新增 IP 組態] 刀鋒視窗中，對 NIC 新增第二個 IP 組態，如下所示︰ 
        1. 輸入次要 IP 組態的名稱 (例如，將 VM1 和 VM2 的 IP 組態分別命名為 VM1NIC2-ipconfig2 和 VM2NIC2-ipconfig2)。
        2. 針對 [私人 IP 位址]，為 [配置] 選取 [靜態]。
        3. 按一下 [確定] 。
        4. 次要 NIC 的第二個 IP 組態在設定完成時，會顯示在給定 NIC 的 [IP 組態] 設定刀鋒視窗中。

### <a name="step-2-create-a-load-balancer"></a>步驟 2：建立負載平衡器

如下所示建立負載平衡器︰

1. 透過瀏覽器瀏覽至 Azure 入口網站 http://portal.azure.com，並使用您的 Azure 帳戶登入。
2. 在畫面左側按一下 [新增] > [網路] > [負載平衡器]。 接下來，按一下 [建立]。
3. 在 [建立負載平衡器] 刀鋒視窗中，輸入負載平衡器的名稱。 在此我們將其稱為 mylb。
4. 在 [公用 IP 位址] 底下建立稱為 **PublicIP1** 的新公用 IP。
5. 在 [資源群組] 底下，選取 VM 的現有資源群組 (例如，contosofabrikam)。 接著選取適當位置，然後按一下 [確定]。 負載平衡器接著會開始部署，並且會需要幾分鐘的時間才能順利完成部署。
6. 負載平衡器在部署後會顯示為資源群組中的資源。

### <a name="step-3-configure-the-frontend-ip-pool"></a>步驟 3︰設定前端 IP 集區

為每個網站 (Contoso 和 Fabrikam) 設定前端 IP 集區，如下所示︰

1. 在入口網站中，按一下 [更多服務]，在篩選方塊中輸入**公用 IP 位址**，然後按一下 [公用 IP 位址]。 按一下出現的刀鋒視窗中靠近上方的 [新增]。
2. 為這兩個網站 (contoso 和 fabrikam) 設定兩個公用 IP 位址 (PublicIP1 和 PublicIP2)，如下所示︰
    1. 輸入前端 IP 位址的名稱。
    2. 在 [資源群組] 中選取 VM 的現有資源群組 (例如，contosofabrikam)。
    3. 在 [位置] 中選取和 VM 相同的位置。
    4. 按一下 [確定] 。
    5. 兩個公用 IP 位址都建立好之後，便會同時顯示在 [公用 IP 位址] 刀鋒視窗中。
3. 在入口網站中，按一下 [更多服務]，在篩選方塊中輸入**負載平衡器**，然後按一下 [負載平衡器]。  
4. 選取要將前端 IP 集區新增到的負載平衡器 (mylb)。
5. 在 [設定] 底下選取 [前端集區]。 然後按一下出現的刀鋒視窗中靠近上方的 [新增]。
6. 輸入前端 IP 位址的名稱 (*farbikamfe* 或 *contosofe*)。
7. 按一下 [IP 位址]，然後在 [選擇公用 IP 位址] 刀鋒視窗上，選取前端的 IP 位址 (PublicIP1 或 PublicIP2)。
8. 請重複本節中的步驟 3 到 7，以建立第二個前端 IP 位址。
9. 當前端 IP 集區組態設定完成時，這兩個前端 IP 位址會顯示在負載平衡器的 [前端 IP 集區] 刀鋒視窗中。 
    
### <a name="step-4-configure-the-backend-pool"></a>步驟 4︰設定後端集區   
為每個網站 (Contoso 和 Fabrikam) 設定負載平衡器上的後端位址集區，如下所示︰
        
1. 在入口網站中，按一下 [更多服務]，在篩選方塊中輸入負載平衡器，然後按一下 [負載平衡器]。  
2. 選取要將後端集區新增到的負載平衡器 (mylb)。
3. 在 [設定] 底下選取 [後端集區]。 輸入後端集區的名稱 (例如，contosopool 或 fabrikampool)。 然後按一下出現的刀鋒視窗中靠近上方的 [新增] 按鈕。 
4. 在 [關聯到] 中，選取 [可用性設定組]。
5. 在 [可用性設定組] 中，選取 [myAvailset]。
6. 如下所示為這兩個 VM 新增目標網路 IP 組態 (請參閱圖 2)︰  
    1. 在 [目標虛擬機器] 中，選取要新增到後端集區的 VM (例如，VM1 或 VM2)。
    2. 在 [網路 IP 組態] 中，選取該 VM 的次要 NIC IP 組態 (例如，VM1NIC2-ipconfig2 或 VM2NIC2-ipconfig2)。
    ![LB 案例影像](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
            
        **圖 2**︰設定具有後端集區的負載平衡器  
7. 按一下 [確定] 。
8. 當後端集區組態設定完成時，這兩個後端位址集區會顯示在負載平衡器的 [後端集區] 刀鋒視窗中。

### <a name="step-5-configure-a-health-probe-for-your-load-balancer"></a>步驟 5︰設定負載平衡器的健康狀態探查
設定負載平衡器的健康狀態探查，如下所示︰
    1. 在入口網站中，按一下 [更多服務]，在篩選方塊中輸入負載平衡器，然後按一下 [負載平衡器]。  
    2. 選取要將後端集區新增到的負載平衡器。
    3. 在 [設定] 下，選取 [健康狀態探查]。 然後按一下出現的刀鋒視窗中靠近上方的 [新增]。
    4. 輸入健康狀態探查的名稱 (例如，HTTP)，然後按一下 [確定]。

### <a name="step-6-configure-load-balancing-rules"></a>步驟 6：設定負載平衡規則
為每個網站設定負載平衡規則 (HTTPc 和 HTTPf)，如下所示︰
    
1. 在 [設定] 下，選取 [健康狀態探查]。 然後按一下出現的刀鋒視窗中靠近上方的 [新增]。
2. 在 [名稱] 中，輸入負載平衡規則的名稱 (例如，對 Contoso 輸入 HTTPc，對 Fabrikam 則輸入 HTTPf)
3. 在 [前端 IP 位址] 中選取前端 IP 位址 (例如，Contosofe 或 Fabrikamfe)
4. 在 [連接埠] 和 [後端連接埠] 中，保留預設值 **80**。
5. 在 [浮動 IP (伺服器直接回傳)] 中，按一下 [啟用]。
6. 按一下 [確定] 。
7. 請重複本節中的步驟 1 到 6，以建立第二個負載平衡器規則。
8. 當負載平衡規則組態設定完成時，這兩個規則 (HTTPc 和 HTTPf) 會顯示在負載平衡器的 [負載平衡規則] 刀鋒視窗中。

### <a name="step-7-configure-dns-records"></a>步驟 7︰設定 DNS 記錄
最後，您必須將 DNS 資源記錄設定為指向負載平衡器的個別前端 IP 位址。 您可以在 Azure DNS 中裝載網域。 如需搭配使用 Azure DNS 與 Load Balancer 的詳細資訊，請參閱[使用 Azure DNS 搭配其他 Azure 服務](../dns/dns-for-azure-services.md)。

## <a name="next-steps"></a>後續步驟
- 若要深入了解如何在 Azure 中合併負載平衡服務，請參閱[在 Azure 中使用負載平衡服務](../traffic-manager/traffic-manager-load-balancing-azure.md)。
- 若要深入了解如何使用不同類型的記錄檔來管理負載平衡器和針對其問題進行疑難排解，請參閱 [Azure Load Balancer 的 Log Analytics](../load-balancer/load-balancer-monitor-log.md)。

