---
title: "設定 Azure ExpressRoute 線路的網路效能監視器 (預覽) | Microsoft Docs"
description: "設定 Azure ExpressRoute 線路的路由 NPM。 (預覽)"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: cherylmc
ms.openlocfilehash: 9e459a42a9fd7caedfa255a7baf51273eef2265a
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="configure-network-performance-monitor-for-expressroute-preview"></a>設定 ExpressRoute 線路的網路效能監視器 (預覽)

「網路效能監視器」(NPM) 是一個雲端式網路監視解決方案，可監視 Azure 雲端部署與內部部署位置 (分公司等) 之間的連線。 NPM 是 Microsoft Operations Management Suite (OMS) 的一部分。 NPM 現在為 ExpressRoute 提供擴充功能，可讓您監視經由已設定為使用「私用對等互連」之 ExpressRoute 線路的網路效能。 設定 ExpressRoute 的 NPM 時，您可以偵測網路問題來予以識別並排除。

您可以：

* 監視各種 VNet 的遺失和延遲並設定警示

* 監視網路上的所有路徑 (包括備援路徑)

* 針對難以複寫的暫時性和時間點網路問題進行疑難排解

* 協助判斷導致效能衰退的特定網路區段

* 取得每一虛擬網路的輸送量 (如果您在每個 VNet 都安裝了代理程式)

* 查看先前時間點的 ExpressRoute 系統狀態

## <a name="regions"></a>支援的區域

您可以在世界各地監視 ExpressRoute 電路，方法是使用下列其中一個區域中裝載的工作區：

* 西歐 
* 美國東部 
* 東南亞 

## <a name="workflow"></a>工作流程

監視代理程式安裝在內部部署和 Azure 中的多部伺服器上。 這些代理程式會彼此互相通訊但不會傳送資料，而是會傳送 TCP 交握封包。 代理程式之間的通訊可讓 Azure 對應流量可採用的網路拓撲和路徑。

1. 在其中一個[支援的區域](#regions)中建立 NPM 工作區。
2. 安裝並設定軟體代理程式： 
    * 在內部部署伺服器和 Azure VM 上安裝監視代理程式。
    * 設定監視代理程式伺服器上的設定，以允許監視代理程式進行通訊。 (開啟防火牆連接埠等)。
3. 設定網路安全性群組 (NSG) 規則，以允許安裝在 Azure VM 上的監視代理程式與內部部署監視代理程式進行通訊。
4. 要求將您的 NPM 工作區加入白名單。
5. 設定監視功能：自動探索及管理要在 NPM 中顯示哪些網路。

如果您已經使用「網路效能監視器」來監視其他物件或服務，並且在其中一個支援的區域中已經有工作區，則可以略過步驟 1 和步驟 2，然後從步驟 3 開始進行設定。

## <a name="configure"></a>步驟 1：建立工作區

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋 **Marketplace** 中的服務清單以找出 [網路效能監視器]。 在傳回的結果中，按一下以開啟 [網路效能監視器] 頁面。

  ![入口網站](.\media\how-to-npm\3.png)<br><br>
2. 在 [網路效能監視器] 主頁面底部，按一下 [建立] 以開啟 [網路效能監視器 - 建立新的解決方案] 頁面。 按一下 [OMS 工作區 - 選取工作區] 以開啟 [工作區] 頁面。 按一下 [+ 建立新工作區] 以開啟 [工作區] 頁面。
3. 在 [OMS 工作區] 頁面上，選取 [新建]，然後設定下列設定：

  * OMS 工作區：輸入您工作區的名稱。
  * 訂用帳戶 - 如果您有多個訂用帳戶，請選擇要與新工作區建立關聯的帳戶。
  * 資源群組 - 建立資源群組，或使用現有的資源群組。
  * 位置 - 您必須選取[支援的區域](#regions)。
  * 定價層 - 選取 [免費]
  
  >[!NOTE]
  >ExpressRoute 電路可能位於全世界的任何位置，且不一定要與工作區位於相同的區域。
  >


  ![工作區](.\media\how-to-npm\4.png)<br><br>
4. 按一下 [確定] 以儲存並部署設定範本。 在範本驗證之後，按一下 [建立] 以部署工作區。
5. 部署工作區之後，瀏覽至您所建立的 [NetworkMonitoring (名稱)] 資源。 驗證設定，然後按一下 [解決方案需要其他設定]。

  ![其他設定](.\media\how-to-npm\5.png)
6. 在 [歡迎使用網路效能監視器] 頁面上，選取 [使用 TCP 進行綜合交易]，然後按一下 [提交]。 TCP 交易僅供用來建立和中斷連線。 不會透過這些 TCP 連線傳送任何資訊。

  ![用於綜合交易的 TCP](.\media\how-to-npm\6.png)

## <a name="agents"></a>步驟 2：安裝及設定代理程式

### <a name="download"></a>2.1：下載代理程式安裝檔案

1. 在您資源的 [網路效能監視器設定 - TCP 設定] 頁面上的 [安裝 OMS 代理程式] 區段中，按一下與您伺服器的處理器對應的代理程式，然後下載安裝檔案。

  >[!NOTE]
  >代理程式必須安裝於 Windows Server (2008 SP1 或更新版本) 上。 不支援使用 Windows 桌面版 OS 和 Linux OS 監視 ExpressRoute 線路。 
  >
  >
2. 接著，將 [工作區識別碼] 和 [主要金鑰] 複製到 [記事本]。
3. 在 [設定代理程式] 區段中，下載 Powershell 指令碼。 此 PowerShell 指令碼將協助您開啟 TCP 交易的相關防火牆連接埠。

  ![PowerShell 指令碼](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2：在每部監視伺服器上安裝監視代理程式

基於備援的因素，建議您在 ExpressRoute 連線的每一端 (亦即內部部署、Azure VNET) 安裝至少兩個代理程式。 使用下列步驟來安裝代理程式：

1. 執行**安裝程式**，以在您想要用於監視 ExpressRoute 的每部伺服器上安裝代理程式。 您用來進行監視的伺服器可以是 VM 或內部部署伺服器，而且必須能夠存取網際網路。 您必須至少在內部部署環境安裝一個代理程式，以及在 Azure 中您需要監視的每個網路區段安裝一個代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。
3. 閱讀 [授權條款] 頁面上的授權，然後按一下 [我接受]。
4. 在 [目的資料夾] 頁面上，變更或保留預設的安裝資料夾，然後按 [下一步]。
5. 在 [代理程式安裝選項] 頁面上，您可以選擇將代理程式連線到 Azure Log Analytics (OMS) 或 Operations Manager。 或者，如果您想要稍後設定代理程式，則可以將選項留白。 進行選擇後，按 [下一步]。

  * 如果您選擇連線到 [Azure Log Analytics (OMS)]，請貼上您在上一節複製到「記事本」中的 [工作區識別碼] 和 [工作區金鑰 (主要金鑰)]。 然後按 [下一步] 。

    ![識別碼和金鑰](.\media\how-to-npm\8.png)
  * 如果您選擇連線到 [Operations Manager]，請在 [管理群組設定] 頁面上，輸入 [管理群組名稱]、[管理伺服器]及 [管理伺服器連接埠]。 然後按 [下一步] 。

    ![Operations Manager](.\media\how-to-npm\9.png)
  * 在 [代理程式動作帳戶] 頁面上，選擇 [本機系統] 帳戶或 [網域或本機電腦] 帳戶。 然後按 [下一步] 。

    ![帳戶](.\media\how-to-npm\10.png)
6. 在 [安裝準備就緒] 頁面上，檢閱您的選擇，然後按一下 [安裝]。
7. 在 [設定成功完成] 頁面上，按一下 [完成]。
8. 完成時，[Microsoft Monitoring Agent] 會出現在 [控制台] 中。 您可以在該處檢閱您的設定，並確認代理程式是否已連線到 Operational Insights (OMS)。 當連線到 OMS 時，代理程式會顯示訊息︰**Microsoft Monitoring Agent 已成功連線到 Microsoft Operations Management Suite 服務**。

### <a name="proxy"></a>2.3：設定 Proxy 設定 (選擇性)

如果您要使用 Web Proxy 來存取網際網路，請使用下列步驟來設定 Microsoft Monitoring Agent 的 Proxy 設定。 針對每一部伺服器執行這些步驟。 如果您需要設定許多伺服器，使用指令碼將此程序自動化會比較容易。 如果是這種情況，請參閱[使用指令碼設定 Microsoft Monitoring Agent 的 Proxy 設定](../log-analytics/log-analytics-windows-agents.md#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script)。

使用 [控制台] 來設定 Microsoft Monitoring Agent 的 Proxy 設定：

1. 開啟 [控制台]。
2. 開啟 [ **Microsoft Monitoring Agent**].
3. 按一下 [ **Proxy 設定** ] 索引標籤。
4. 選取 [使用 Proxy 伺服器]，然後輸入 URL 和連接埠號碼 (如果需要)。 如果您的 Proxy 伺服器需要驗證，請輸入使用者名稱與密碼以存取 Proxy 伺服器。

  ![proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4：確認代理程式連線能力

您可以輕鬆確認代理程式是否正在進行通訊。

1. 在具有監視代理程式的伺服器上，開啟 [控制台]。
2. 開啟 [Microsoft Monitoring Agent]。
3. 按一下 [Azure Log Analytics (OMS)] 索引標籤。
4. 在 [狀態] 欄中，您應該會看到代理程式已成功連線到 Operations Management Suite 服務。

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5：開啟監視代理程式伺服器上的防火牆連接埠

若要使用 TCP 通訊協定，您必須開啟防火牆連接埠，以確保監視代理程式能夠進行通訊。

您可以執行 PowerShell 指令碼以建立「網路效能監視器」所需的登錄機碼，以及建立 Windows 防火牆規則，以允許監視代理程式彼此建立 TCP 連線。 此指令碼所建立的登錄機碼也會指定是否要記錄偵錯記錄檔，以及記錄檔的路徑。 它也會定義用於通訊的代理程式 TCP 連接埠。 此指令碼會自動設定這些機碼的值，所以您不應手動變更這些機碼。

連接埠 8084 是預設開啟的連接埠。 您可以為指令碼提供 'portNumber' 參數來使用自訂連接埠。 不過，如果您這麼做，就必須為執行指令碼的所有伺服器指定相同的連接埠。

>[!NOTE]
>'EnableRules' PowerShell 指令碼只能在指令碼執行所在的伺服器上設定 Windows 防火牆規則。 如果您有網路防火牆，您應該確定它允許指向網路效能監視器所用 TCP 連接埠的流量。
>
>

在代理程式伺服器上，以系統管理權限開啟 PowerShell 視窗。 執行 [EnableRules](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) PowerShell 指令碼 (您先前所下載)。 請勿使用任何參數。

  ![PowerShell 指令碼](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>步驟 3：設定網路安全性群組規則

針對在 Azure 中的監視代理程式伺服器，您必須設定網路安全性群組 (NSG) 規則，才能在 NPM 用來進行綜合交易的連接埠上允許 TCP 流量。 預設連接部是 8084。 這可允許安裝在 Azure VM 上的監視代理程式與內部部署監視代理程式進行通訊。

如需有關 NSG 的詳細資訊，請參閱[網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-portal.md)。

## <a name="whitelist"></a>步驟 4：要求將工作區加入白名單

>[!NOTE]
>繼續進行此步驟之前，請先確定您已安裝代理程式 (包括內部部署伺服器代理程式和 Azure 伺服器代理程式兩者)，並且已執行 PowerShell 指令碼。
>
>

您必須先要求將工作區加入白名單中，才能開始使用 NPM 的 ExpressRoute 監視功能。 [請按一下這裡以移至該頁面，並填寫要求表單](https://aka.ms/npmcohort)。 (提示：您可以在新視窗或索引標籤中開啟此連結)。 加入白名單的程序可能需要一或多個工作天的時間。 加入白名單完成之後，您會收到一封電子郵件。

## <a name="setupmonitor"></a>步驟 5：設定 NPM 來進行 ExpressRoute 監視

>[!WARNING]
>在將您的工作區加入白名單並收到確認電子郵件之前，請勿進行進一步的動作。
>
>

完成先前各節並確認您已加入白名單之後，即可設定監視功能。

1. 移至 [所有資源] 頁面並按一下已加入白名單的 NPM 作區，以瀏覽至 [網路效能監視器] 概觀圖格。

  ![NPM 工作區](.\media\how-to-npm\npm.png)
2. 按一下 [網路效能監視器] 概觀圖格以顯示儀表板。 此儀表板包含 ExpressRoute 頁面，當中顯示 ExpressRoute 處於「尚未設定的狀態」。 請按一下 [功能設定] 來開啟「網路效能監視器」設定頁面。

  ![功能設定](.\media\how-to-npm\npm2.png)
3. 在設定頁面上，瀏覽至位於左側面板上的 [ExpressRoute 對等互連] 索引標籤。 按一下 [立即探索]。

  ![探索](.\media\how-to-npm\13.png)
4. 探索完成時，您會看到獨特線路名稱和 VNet 名稱的規則。 這些規則一開始處於停用狀態。 請啟用這些規則，然後選取監視代理程式和臨界值。

  ![規則](.\media\how-to-npm\14.png)
5. 啟用規則並選取值和您要監視的代理程式之後，大約需要等候 30-60 分鐘，值才會開始填入且 [ExpressRoute 監視] 圖格才會變成可用。 看到監視圖格之後，即表示 NPM 正在監視您的 ExpressRoute 線路和連線資源。

  ![監視圖格](.\media\how-to-npm\15.png)

## <a name="explore"></a>步驟 6：檢視監視圖格

### <a name="dashboard"></a>網路效能監視器頁面

NPM 頁面包含 ExpressRoute 頁面，當中顯示 ExpressRoute 線路和對等互連的健康情況。

  ![儀表板](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>線路清單

若要查看所有受監視的 ExpressRoute 線路清單，請按一下 [ExpressRoute 線路] 圖格。 您可以選取某個線路，然後檢視其健全狀態，以及封包遺失、頻寬使用率和延遲的趨勢圖表。 這些圖表是互動式圖表。 您可以選取一個自訂的時間範圍來繪製圖表。 您可以在圖表上以滑鼠拖曳出一個區域，以放大並查看更細微的資料點。

  ![線路清單](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>遺失、延遲及輸送量的趨勢

頻寬、延遲及遺失圖表是互動式圖表。 您可以使用滑鼠控制項將這些圖表的任何部分放大。 您也可以按一下位於左上方 [動作] 按鈕底下的 [日期/時間]，以查看其他間隔的頻寬、延遲及遺失資料。

![趨勢](.\media\how-to-npm\16.png)

### <a name="peerings"></a>清單

按一下儀表板上的 [私用對等互連] 圖格，將會顯示透過私用對等互連連到虛擬網路的所有連線清單。 在這裡，您可以選取某個虛擬網路連線，然後檢視其健全狀態，以及封包遺失、頻寬使用率和延遲的趨勢圖表。

  ![線路清單](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>線路拓撲

若要檢視線路拓撲，請按一下 [拓撲] 圖格。 這會將您移至所選線路或對等互連的拓撲檢視。 拓撲圖表會提供網路上每個區段的延遲，而且每個第 3 層躍點都會由一個圖表節點來代表。 按一下躍點將會顯示關於該躍點的更多詳細資料。
您可以藉由移動 [篩選] 底下的滑動軸，將可見性層級提升到包含內部部署躍點。 將滑動軸向左或向右移動，將可增加或減少拓撲圖表中的躍點數目。 您可以看見每個區段的延遲，這可讓您更快速隔離網路上的高延遲區段。

![filters](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>電路的詳細拓撲檢視

此檢視會顯示 VNet 連線。
![詳細拓撲](.\media\how-to-npm\17.png)
