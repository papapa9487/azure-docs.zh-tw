---
title: "Azure Log Analytics 中的網路效能監視器方案 | Microsoft Docs"
description: "Azure Log Analytics 中的網路效能監視器可協助您即時監視網路的效能，以偵測和找出網路效能瓶頸。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.openlocfilehash: c6568e491429f6046ab164ab5eacd0ae5846e201
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="network-performance-monitor-solution-in-log-analytics"></a>Log Analytics 中的網路效能監視器解決方案

![網路效能監控符號](./media/log-analytics-network-performance-monitor/npm-symbol.png)

本文件說明如何設定和使用 Log Analytics 中的網路效能監視器方案，協助您即時監視網路的效能，以偵測和找出網路效能瓶頸。 使用網路效能監視器方案，可以監視兩個網路、子網路或伺服器之間的遺失和延遲。 網路效能監視器會偵測網路問題，例如流量黑洞、路由錯誤，以及傳統網路監視方法無法偵測的問題。 網路效能監視器會產生警示，並違反網路連結的臨界值時發生通知。 系統可以自動學習這些臨界值，您也可以將其設定為使用自訂警示規則。 網路效能監視器可確保及時偵測網路效能問題，並使問題的來源局限於特定網路區段或裝置。

您可以利用方案儀表板來偵測網路問題，其中顯示您的網路摘要資訊，包括最近的網路健康狀態事件、狀況不良的網路連結，以及正面臨高封包遺失和延遲的子網路連結。 您可以向下切入至網路連結，以檢視子網路的目前健康狀態及節點間的連結。 您也可以檢視網路、子網路和節點對節點層級的遺失和延遲歷史趨勢。 您可以藉由檢視封包遺失和延遲的歷史趨勢圖表來偵測暫時性網路問題，並找出拓撲圖上的網路瓶頸。 互動式拓撲圖可讓您將逐一躍點的網路路由視覺化，並判斷問題的來源。 如同任何其他方案，您可以將「記錄檔搜尋」用於各種分析需求，以根據網路效能監視器所收集的資料建立自訂報告。

此方案使用綜合交易做為主要機制來偵測網路錯誤。 因此，您可以使用它，而不需考慮特定網路裝置的廠商或模型。 其適用於內部部署、雲端 (IaaS) 和混合式環境。 此方案會自動探索網路拓撲和網路中的各種路由。

一般的網路監視產品著重於監視網路裝置 (路由器、交換器等) 健康狀態，但未深入解析兩點之間網路連線的實際品質，然而網路效能監視器卻辦得到。

### <a name="using-the-solution-standalone"></a>獨立使用此方案
如果您想要監視重要工作負載、網路、資料中心或公司網站之間的網路連線品質，則可以使用網路效能監視器方案本身來監視下列項目之間的連線健康狀態︰

* 使用公用或私人網路連接的多個資料中心或公司網站
* 執行企業營運應用程式的重要工作負載
* 公用雲端服務 (如 Microsoft Azure 或 Amazon Web Services (AWS)) 與內部部署網路，前提是您有可用的 IaaS (VM) 並已將閘道設定為允許內部部署網路與雲端網路之間的通訊
* 當您使用 ExpressRoute 時的 Azure 與內部部署網路

### <a name="using-the-solution-with-other-networking-tools"></a>使用此方案搭配其他網路工具
如果您想要監視企業營運應用程式，您可以使用網路效能監視器方案做為其他網路工具的配對方案。 低速網路可能會導致低速的應用程式，網路效能監視器可協助您調查由基礎網路問題所造成的應用程式效能問題。 因為此方案不需要存取任何網路裝置，所以應用程式管理員不需要依賴網路小組提供網路對應用程式有何影響的相關資訊。

此外，如果您已投資其他網路監視工具，此方案則可補足這些工具，因為大部分的傳統網路監視解決方案不會深入解析端對端網路效能計量，例如遺失和延遲。  網路效能監視器方案有助於填補該不足之處。

## <a name="installing-and-configuring-agents-for-the-solution"></a>安裝和設定方案的代理程式
使用[將 Windows 電腦連接到 Log Analytics](log-analytics-windows-agents.md) 和[將 Operations Manager 連接到Log Analytics](log-analytics-om-agents.md) 中的基本程序來安裝代理程式。

> [!NOTE]
> 您必須安裝至少 2 個代理程式，才能有足夠的資料來探索及監視網路資源。 否則，此方案會保持設定中狀態，直到您安裝及設定其他代理程式為止。
>
>

### <a name="where-to-install-the-agents"></a>安裝代理程式的位置
安裝代理程式之前，請考慮您的網路拓撲以及您想要監視的網路部分。 建議您針對想要監視的每個子網路安裝多個代理程式。 換句話說，針對您想要監視的每個子網路，選擇兩部或多部伺服器或 VM 並在其上安裝代理程式。

如果您不確定您的網路拓撲，請在具有重要工作負載且您想要監視網路效能的伺服器上安裝代理程式。 比方說，您可能想要追蹤 Web 伺服器與執行 SQL Server 的伺服器之間的網路連線。 在此範例中，您會在兩部伺服器上安裝代理程式。

代理程式會監視主機之間的網路連線 (連結) - 而不是主機本身。 所以，若要監視網路連結，您必須在該連結的兩個端點上安裝代理程式。

### <a name="configure-agents"></a>設定代理程式

如果您想要使用 ICMP 通訊協定進行綜合交易，您必須啟用下列防火牆規則，以便可靠地使用 ICMP：

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow
```


如果您要使用 TCP 通訊協定，必須開啟這些電腦的防火牆連接埠，確保代理程式可以通訊。 您必須使用系統管理權限下載並執行 [EnableRules.ps1 PowerShell 指令碼](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634)，而不需 PowerShell 視窗中的任何參數。

此指令碼會建立網路效能監視器所需的登錄機碼並建立 Windows 防火牆規則，以允許代理程式彼此建立 TCP 連線。 此指令碼建立的登錄機碼也會指定是否記錄偵錯記錄檔和記錄檔的路徑。 它也會定義用於通訊的代理程式 TCP 連接埠。 此指令碼會自動設定這些機碼的值，所以您不應手動變更這些機碼。

預設開啟的連接埠是 8084。 將 `portNumber` 參數提供給此指令碼，即可使用自訂連接埠。 不過，相同的連接埠應使用於指令碼執行所在的所有電腦上。

> [!NOTE]
> EnableRules.ps1 指令碼只能在指令碼執行所在的電腦上設定 Windows 防火牆規則。 如果您有網路防火牆，您應該確定它允許指向網路效能監視器所用 TCP 連接埠的流量。
>
>

## <a name="configuring-the-solution"></a>設定方案
請使用下列資訊來安裝和設定方案。

1. 網路效能監視器方案可從執行 Windows Server 2008 SP1 或更新版本或 Windows 7 SP1 或更新版本的電腦取得資料，這與 Microsoft Monitoring Agent (MMA) 的需求相同。 NPM 代理程式也可以在 Windows 桌面/用戶端作業系統 (Windows 10、Windows 8.1、Windows 8 和 Windows 7) 上執行。
    >[!NOTE]
    >Windows 伺服器作業系統的代理程式支援 TCP 和 ICMP 做為綜合交易的通訊協定。 不過，Windows 用戶端作業系統的代理程式僅支援 ICMP 做為綜合交易的通訊協定。

2. 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) 或使用[從方案庫新增 Log Analytics 方案](log-analytics-add-solutions.md)中所述的程序，將網路效能監視器方案新增至您的工作區。  
   ![網路效能監視器符號](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. 在 OMS 入口網站中，您會看到標題為 [網路效能監視器] 的新圖格以及「方案需要額外設定」訊息。 您必須將方案設定為根據代理程式探索到的子網路和節點新增網路。 按一下 [網路效能監視器] 開始設定預設網路。  
   ![方案需要額外設定](./media/log-analytics-network-performance-monitor/npm-config.png)

### <a name="configure-the-solution-with-a-default-network"></a>使用預設網路設定方案
在設定頁面上，您會看到名為 [預設] 的單一網路。 如果尚未定義任何網路，則所有自動探索的子網路都會放在預設網路中。

每當您建立網路時，您會新增其子網路，而該子網路會從預設網路中移除。 如果您刪除網路，其所有子網路都會自動回到預設網路。

換句話說，預設網路是所有未包含於使用者定義網路之子網路的容器。 您無法編輯或刪除預設網路。 它一定會保留在系統中。 不果，您可以視需要建立任意數量的網路。

在大部分情況下，組織中的子網路會安排在多個網路中，您應該建立一或多個網路，才能以邏輯方式將子網路分組。

### <a name="create-new-networks"></a>建立新網路
網路效能監視器中的網路是子網路的容器。 您可以使用您所要的任何名稱來建立網路，並將子網路新增到該網路。 例如，您可以建立名為 Building1 的網路，然後新增子網路，也可以建立名為 DMZ 的網路，然後將所有屬於非軍事區的子網路新增至此網路。

#### <a name="to-create-a-new-network"></a>若要建立新網路
1. 按一下 [新增網路]，然後輸入網路名稱和描述。
2. 選取一或多個子網路，然後按一下 [新增]。
3. 按一下 [儲存] 儲存組態。  
   ![新增網路](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>等候資料彙總
第一次儲存組態之後，解決方案就會開始收集代理程式安裝所在節點之間的網路封包遺失和延遲資訊。 此程序可能需要一些時間，有時候超過 30 分鐘。 在此狀態下，概觀頁面中的 [網路效能監視器] 圖格會顯示一則訊息，表示「資料彙總進行中」。

![資料彙總進行中](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

資料上傳後，您會看到更新後的 [網路效能監視器] 圖格顯示資料。

![網路效能監視器圖格](./media/log-analytics-network-performance-monitor/npm-tile.png)

按一下此圖格可檢視網路效能監視器儀表板。

![網路效能監視器儀表板](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>編輯子網路的監視設定
至少安裝一個代理程式的所有子網路都會列在設定頁面的 [子網路] 索引標籤上。

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>啟用或停用特定子網路的監視
1. 選取或清除 [子網路識別碼] 旁邊的方塊，然後確定已視情況選取或清除 [用於監視]。 您可以選取或清除多個子網路。 停用後，子網路就不受監視，因為代理程式將會更新為停止 ping 其他代理程式。
2. 從清單中選取特定子網路，並且在包含未受監視和受監視節點的清單之間移動所需的節點，即可選擇您想要監視之子網路的節點。
   您可以視需要新增子網路的自訂**描述**。
3. 按一下 [儲存] 儲存組態。  
   ![編輯子網路](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>選擇要監視的節點
所有已安裝代理程式的節點會都列在 [節點] 索引標籤中。

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>若要啟用或停用節點的監視
1. 選取或清除您要監視或停止監視的節點。
2. 視需要按一下 [用於監視]，或加以清除。
3. 按一下 [儲存] 。  
   ![啟用節點監視](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>設定監視規則
網路效能監視器會在違反臨界值時，產生有關一對節點、子網路或網路連結之間連線的健康狀態事件。 系統可以自動學習這些臨界值，您也可以將其設定為自訂警示規則。

系統會建立「預設規則」，並且在任何一對網路或子網路連結之間的遺失或延遲違反系統所了解的臨界值時建立健康狀態事件。 您可以選擇停用預設規則並建立自訂監視規則

#### <a name="to-create-custom-monitoring-rules"></a>若要建立自訂監視規則
1. 按一下 [監視] 索引標籤中的 [新增規則]，然後輸入規則名稱和描述。
2. 從清單中選取一對要監視的網路或子網路連結。
3. 先從網路下拉式清單中選取包含第一個感興趣子網路的網路，然後再從對應的子網路下拉式清單中選取子網路。
   如果您要監視網路連結中的所有子網路，請選取 [所有子網路]。 同樣地，選取其他感興趣的子網路。 而且，您可以按一下 [新增例外狀況]，從您所做的選取範圍中排除特定子網路連結的監視。
4. 在 ICMP 和 TCP 通訊協定之間選擇，供您用來執行綜合交易。
5. 如果您不想建立所選項目的健康狀態事件，則清除 [在此規則所涵蓋的連結上啟用健康狀態監視]。
6. 選擇監視條件。
   您可以輸入臨界值，以設定健康狀態事件產生的自訂臨界值。 只要條件的值高於針對所選網路/子網路配對選取的臨界值時，就會產生健康狀態事件。
7. 按一下 [儲存] 儲存組態。  
   ![建立自訂監視規則](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

儲存監視規則之後，您可以按一下**建立警示**，使用警示管理整合該規則。 警示規則會使用搜尋查詢自動建立，且其他必要參數會自動填滿。 使用警示規則，除了 NPM 內現有的警示，您還可以接收電子郵件型警示。 警示也會使用 runbook 觸發修復動作，或可以使用 webhook 與現有的服務管理解決方案整合。 您可以按一下**管理警示**以編輯警示設定。

### <a name="choose-the-right-protocol-icmp-or-tcp"></a>選擇正確的通訊協定：ICMP 或 TCP

網路效能監視器 (NPM) 使用綜合交易來計算網路效能度量，例如封包遺失和連結延遲。 若要進一步了解，請想像連接到網路連結一端的 NPM 代理程式。 這個 NPM 代理程式會傳送探查封包至連接到網路另一端的第二個 NPM 代理程式。 第二個代理程式會以回應封包回覆。 此程序會重複幾次。 藉由測量回應的數目及接收每個回應所花費的時間，第一個 NPM 代理程式就可評估連結延遲和封包遺失。

您在建立監視規則時所選擇的通訊協定，會決定這些封包的格式、大小和順序。 根據封包的通訊協定，中繼網路裝置 (路由器、交換器等等) 可能會以不同的方式處理這些封包。 因此，您的通訊協定選擇會影響結果的精確度。 另外，您的通訊協定選擇也會決定在您部署 NPM 解決方案後，是否必須採取任何手動步驟。

NPM 提供 ICMP 和 TCP 通訊協定選項，供您用來執行綜合交易。
如果您在建立綜合交易規則時選擇 ICMP，NPM 代理程式會使用 ICMP ECHO 訊息來計算網路延遲和封包遺失。 ICMP ECHO 使用的訊息與傳統 Ping 公用程式傳送的訊息相同。 當您使用 TCP 做為通訊協定時，NPM 代理程式會透過網路傳送 TCP SYN 封包。 接著 TCP 交握完成，然後移除使用 RST 封包的連線。

#### <a name="points-to-consider-before-choosing-the-protocol"></a>選擇通訊協定前應考慮的重點
選擇通訊協定之前，您應該考慮下列資訊：

##### <a name="discovering-multiple-network-routes"></a>探索多個網路路由
TCP 在探索多個路由時精確度較高，而且在每個子網路中需要的代理程式較少。 例如，只要有一或二個使用 TCP 的代理程式，就可以探索子網路之間的所有備援路徑。 不過，使用 ICMP 時，需要數個代理程式才能達到類似的結果。 如果使用 ICMP，假設您在兩個子網路間有 *N* 個路由，您在來源或目標子網路就需要超過 5*N* 個代理程式。

##### <a name="accuracy-of-results"></a>結果的精確度
路由器和交換器對於 ICMP ECHO 封包會指派比 TCP 封包較低的優先順序。 在某些情況下，當網路裝置處於大量負載狀態時，透過 TCP 取得的資料會更貼切地反映應用程式發生的遺失和延遲。 這是因為大部分的應用程式流量都是透過 TCP 傳送。 在這種情況下，ICMP 提供的結果精確度就不及 TCP。

##### <a name="firewall-configuration"></a>防火牆設定
TCP 通訊協定會要求 TCP 封包傳送至目的地連接埠。 NPM 代理程式使用的預設連接埠是 8084，不過您可以在設定代理程式時變更。 因此，您必須確定網路防火牆或 NSG 規則 (在 Azure 中) 允許該連接埠的流量。 您也必須確定安裝代理程式的電腦本機防火牆設定為允許這個連接埠的流量。

您可以使用 PowerShell 指令碼來設定執行 Windows 之電腦上的防火牆規則，不過您必須手動設定網路防火牆。

相反地，ICMP 運作不使用連接埠。 在大部分的企業案例中，會允許 ICMP 流量通過防火牆，讓您可以使用如 Ping 公用程式的網路診斷工具。 因此，如果您可以從另一部電腦 Ping 某部電腦，則您可以使用 ICMP 通訊協定，而不需要手動設定防火牆。

> [!NOTE]
> 如果您不確定要使用哪個通訊協定，可以選擇從 ICMP 開始。 如果您不滿意結果，在稍後隨時可以切換為 TCP。


#### <a name="how-to-switch-the-protocol"></a>如何切換通訊協定

如果您在部署期間選擇使用 ICMP，您可以隨時編輯預設監視規則來切換為 TCP。

##### <a name="to-edit-the-default-monitoring-rule"></a>編輯預設監視規則
1.  瀏覽至 [網路效能]  >  [監視]  >  [設定]  >  [監視]，然後按一下 [預設規則]。
2.  捲動至 [通訊協定] 區段，然後選取您要使用的通訊協定。
3.  按一下 [儲存] 來套用設定。

即使預設規則正在使用特定的通訊協定，您也能以其他通訊協定建立新規則。 您甚至可以建立混合規則，其中某些規則使用 ICMP，而其他使用 TCP。




## <a name="data-collection-details"></a>資料收集詳細資料
當選取 TCP 時，網路效能監視器會使用 TCP SYN-SYNACK-ACK 交握封包，當選取 ICMP 做為收集中斷和延遲資訊的通訊協定時，會使用 ICMP ECHO ICMP ECHO REPLY。 追蹤路由也用來取得拓撲資訊。

下表顯示網路效能監視器的資料收集方法，以及有關如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP 交握/ICMP ECHO 訊息會每 5 秒進行一次，而資料會每 3 分鐘傳送一次 |

此方案會使用綜合交易來評估網路的健全狀況。 OMS 代理程式已安裝在網路交換 TCP 封包或 ICMP Echo (取決於已選取要監視的通訊協定) 各種點。 在過程中，代理程式會了解來回行程時間和封包遺失 (如果有的話)。 此外，每個代理程式也會定期執行其他代理程式的路徑追蹤，以找出網路中必須測試的所有各種路由。 使用這項資料，代理程式就能夠推論網路延遲和封包遺失數字。 測試會每 5 秒重複一次，而代理程式會先彙總三分鐘的資料，再將資料上傳至 Log Analytics 服務。

> [!NOTE]
> 雖然代理程式會經常彼此通訊，但是在進行測試時不會產生大量網路流量。 代理程式只依賴 TCP SYN-SYNACK-ACK 交握封包來判斷遺失和延遲 - 不會交換任何資料封包。 在此過程中，代理程式只會在需要時彼此通訊，而且代理程式通訊拓撲已最佳化以減少網路流量。
>
>

## <a name="using-the-solution"></a>使用解決方案
本節說明所有儀表板函式及其使用方式。

### <a name="solution-overview-tile"></a>解決方案概觀圖格
啟用網路效能監視器方案之後，[OMS 概觀] 頁面上的方案圖格會提供網路健康狀態的快速概觀。 它會顯示一個環圈圖，其中顯示狀況良好和狀況不良的子網路連結數目。 當您按一下圖格時，方案儀表板隨即開啟。

![網路效能監視器圖格](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>網路效能監視器方案儀表板
[網路摘要] 刀鋒視窗會顯示網路摘要以及其相對大小。 其後面接續的圖格會顯示系統中網路連結、子網路連結和路徑的總數 (路徑包含代理程式的兩部主機 IP 位址和它們之間的所有躍點)。

[頂端網路健康狀態事件] 刀鋒視窗會列出系統中最近的健康狀態事件和警示，以及事件變成作用中的時間。 每當網路或子網路連結的封包遺失或延遲超過臨界值時，就會產生健康狀態事件或警示。

[頂端狀況不良的網路連結] 刀鋒視窗會顯示狀況不良的網路連結清單。 這些是當時有一或多個負面健康狀態事件的網路連結。

[遺失最多的頂端子網路連結] 和 [延遲最多的子網路連結] 刀鋒視窗分別顯示依封包遺失的頂端子網路連結和依延遲的頂端子網路連結。 特定網路連結上可能會有高度延遲或某些數量的封包遺失。 這類連結會顯示在前十大清單中，但未標示為狀況不良。

[常用查詢] 刀鋒視窗包含一組搜尋查詢，可直接擷取原始的網路監視資料。 您可以使用這些查詢做為起點，建立自己的查詢以供自訂報告。

![網路效能監視器儀表板](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>深入鑽研
您可以按一下方案儀表板上的各種連結，更深入鑽研任何感興趣的區域。 例如，當您看到儀表板上出現警示或狀況不良的網路連結時，您可以按一下它，進一步調查。 接著顯示的頁面會列出特定網路連結的所有子網路連結。 您將可看到每個子網路連結的遺失、延遲和健康狀態，並快速找出哪些子網路連結造成此問題。 您可以接著按一下 [檢視節點連結]，查看狀況不良子網路連結的所有節點連結。 然後，您可以看到個別的節點間連結並尋找狀況不良的節點連結。

您可以按一下 [檢視拓撲]，檢視來源與目的地節點之間路由的逐一躍點拓撲。 狀況不良的路由或躍點會顯示為紅色，以便您快速識別特定網路部分的問題。

![向下鑽研資料](./media/log-analytics-network-performance-monitor/npm-drill.png)

### <a name="network-state-recorder"></a>網路狀態錄製器

每個檢視會在特定時間點顯示您網路健全狀況的快照集。 根據預設，會顯示最新狀態。 在頁面頂端的列會顯示狀態正在顯示的時間點。 您可以按一下 [動作] 上的列，選擇回到過去並檢視您網路健全狀況的快照集。 您也可以在檢視最新狀態時，選擇啟用或停用任何頁面的自動重新整理。

![網路狀態](./media/log-analytics-network-performance-monitor/network-state.png)

#### <a name="trend-charts"></a>趨勢圖
在您向下鑽研的每個層級，您可以看到網路連結的遺失和延遲趨勢。 趨勢圖也可以用於子網路和節點連結。 您可以使用圖形頂端的時間控制項，變更要繪製之圖表的時間間隔。

趨勢圖會顯示網路連結效能的歷程記錄觀點。 某些網路問題的本質是暫時的，很難只透過查看網路的目前狀態來攔截。 這是因為問題可能會在有人注意前迅速浮現而後消失，只會在稍後的時間點再度出現。 應用程式管理員也可能難以發現這類暫時性問題，因為即使所有的應用程式元件都看似執行順暢，當應用程式的回應時間莫名增加時，通常會出現這些問題。

您可以查看問題將顯示為網路延遲或封包遺失突增的趨勢圖，即可輕鬆地偵測這些種類的問題。

![趨勢圖](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>逐一躍點的拓撲圖
網路效能監視器會在互動式拓撲圖上顯示兩個節點之間路由的逐一躍點拓撲。 選取節點連結，然後按一下 [檢視拓撲]，即可檢視拓撲圖。 此外，按一下儀表板上的 [路徑] 圖格，也可以檢視拓撲圖。 當您按一下儀表板上的 [路徑] 時，您必須從左側面板中選取來源和目的地節點，然後按一下 [繪圖] 以繪製兩個節點之間的路由。

拓撲圖會顯示兩個節點之間有多少個路由，以及資料封包所採用的路徑。 在拓撲圖上，網路效能瓶頸會標示為紅色。 您可以查看拓撲圖上的紅色項目，找出錯誤的網路連線或錯誤的網路裝置。

當您按一下節點或將滑鼠移至拓撲圖上的該節點，您會看到節點的屬性 (如 FQDN 和 IP 位址)。 按一下躍點以查看其 IP 位址。 您可以使用可摺疊動作窗格中的篩選器來選擇篩選特定的路徑。 而且，您也可以使用動作窗格中的滑桿隱藏中繼躍點，來簡化網路拓樸。 您可以使用滑鼠滾輪來放大或縮小拓撲圖。

請注意，圖中顯示的拓撲是第 3 層拓撲，不包含第 2 層裝置和連線。

![逐一躍點的拓撲圖](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>錯誤局部化
網路效能監視器不需連接到網路裝置，就能夠找出網路瓶頸。 網路效能監視器會根據它從網路蒐集來的資料，在網路圖上套用進階演算法，對最可能是問題來源的網路部分進行可能性預估。

這個方法很適合用來判斷無法存取躍點時的網路瓶頸，因為它不需要從網路裝置 (例如路由器或交換器) 蒐集任何資料。 當兩個節點之間的躍點不在您的管理控制時，這個方法也很有用。 例如，躍點可能是 ISP 路由器。

### <a name="log-analytics-search"></a>Log Analytics 搜尋
以圖形方式透過網路效能監視器儀表板和向下鑽研頁面公開的所有資料，原本也可以在 Log Analytics 搜尋中取得。 您可以使用搜尋查詢語言來查詢資料，並藉由將資料匯出到 Excel 或 PowerBI 來建立自訂報告。 儀表板中的 [常用查詢] 刀鋒視窗有一些實用的查詢，您可以這些查詢做為起點來建立自己的查詢和報告。

![搜尋查詢](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>調查健康狀態警示的根本原因
既然您已深入了解網路效能監視器，讓我們看看簡單的健康狀態事件根本原因調查。

1. 在 [概觀] 頁面上，觀察 [網路效能監視器] 圖格，您可以快速概略了解您的網路健康狀態。 請注意，在 6 個受監視的子網路連結中，有 2 個狀況不良。 這值得調查一下。 按一下此圖格以檢視方案儀表板。  
   ![網路效能監視器圖格](./media/log-analytics-network-performance-monitor/npm-investigation01.png)
2. 在下列範例映像中，您會發現有網路連結狀況不佳的健全狀況事件。 您決定要調查此問題，按一下 **DMZ2-DMZ1** 網路連結，以找出問題的根源。  
   ![狀況不良的網路連結範例](./media/log-analytics-network-performance-monitor/npm-investigation02.png)
3. 向下鑽研頁面會顯示 **DMZ2-DMZ1** 網路連結中的所有子網路連結。 您會發現，這兩個子網路連結的延遲已超過臨界值，以致網路連結狀況不良。 您也可以查看這兩個子網路連結的延遲趨勢。 您可以使用圖形中的時間選取控制項，將焦點放在所需的時間範圍。 您可以看到一天當中達到延遲尖峰的時間。 您稍後可以在記錄檔中搜尋此時間，以調查問題。 按一下 [檢視節點連結] 進一步深入鑽研。  
   ![狀況不良的子網路連結範例](./media/log-analytics-network-performance-monitor/npm-investigation03.png)
4. 類似於前一頁，特定子網路連結的向下鑽研頁面會列出其構成的節點連結。 您可以在此執行類似上一個步驟中的動作。 按一下 [檢視拓撲] 可檢視 2 個節點之間的拓撲。  
   ![狀況不良的節點連結範例](./media/log-analytics-network-performance-monitor/npm-investigation04.png)
5. 2 個選定節點之間的所有路徑都會繪製於拓撲圖。 您可以在拓撲圖上呈現這兩個節點之間路由的逐一躍點拓撲。 拓撲圖會清楚顯示兩個節點之間有多少個路由，以及資料封包所採用的路徑。 網路效能瓶頸會標示為紅色。 您可以查看拓撲圖上的紅色項目，找出錯誤的網路連線或錯誤的網路裝置。  
   ![狀況不良的拓撲檢視範例](./media/log-analytics-network-performance-monitor/npm-investigation05.png)
6. 在 [動作] 窗格中可以檢閱每個路徑中的遺失、延遲和躍點數目。 使用捲軸來檢視這些狀況不良路徑的詳細資料。  使用篩選器選取具有狀況不良躍點的路徑，以便僅繪製選取路徑的拓撲。 您可以使用滑鼠滾輪來放大或縮小拓撲圖。

   在下面的圖像中，藉由查看紅色的路徑和躍點，即可清楚看到特定網路區段中問題區域的根本原因。 按一下拓撲圖中的節點，即可顯示該節點的屬性，包括 FQDN 和 IP 位址。 按一下躍點可顯示該躍點的 IP 位址。  
   ![狀況不良的拓撲 - 路徑詳細資料範例](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="provide-feedback"></a>提供意見反應

- **UserVoice** - 您可以張貼您對於希望我們改善之網路效能監視器功能的想法。 造訪 [UserVoice 頁面](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring)。
- **加入我們的社群** - 我們竭誠歡迎新客戶加入我們的社群。 加入其中，您就能夠提早存取新功能，並協助我們改善網路效能監視器。 如果您想加入，請填妥這份[快速問卷調查](https://aka.ms/npmcohort)。

## <a name="next-steps"></a>後續步驟
* [搜尋記錄檔](log-analytics-log-searches.md)以檢視詳細的網路效能資料記錄。
