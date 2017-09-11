---
title: "Azure 資訊安全中心的安全性監視 | Microsoft Docs"
description: "本文可協助您開始使用「Azure 資訊安全中心」的監視功能。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 4752199803f1fab04608e6a8618e3294e85fb38a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="security-health-monitoring-in-azure-security-center"></a>Azure 資訊安全中心的安全性健康情況監視
本文可協助您使用 Azure 資訊安全中心內的監視功能，監視是否符合原則。

## <a name="what-is-security-health-monitoring"></a>什麼是安全性健康情況監視？
我們通常都將監視想像成監看及等候事件發生，以便對該情況作出反應。 安全性監視是指擁有一個主動式策略來稽核您的資源，以識別出不符合組織標準或最佳做法的系統。

## <a name="monitoring-security-health"></a>監視安全性健全狀況
在您為訂用帳戶的資源啟用[安全性原則](security-center-policies.md)之後，資訊安全中心會分析您資源的安全性狀態，以找出潛在的弱點。 您可以立即取得網路組態的相關資訊。 視您已安裝代理程式的 VM 和電腦數目而定，可能需要一個小時以上的時間才能收集有關 VM 和電腦的組態資訊，例如安全性更新狀態和作業系統組態。 您可以在 [防護] 刀鋒視窗中，檢視資源的安全性狀態及任何問題。 您也可以在 [建議] 圖格上檢視這些問題的清單。

如需如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。

在 [防護] 區段之下，您可以監視資源的安全性狀態。 在下列範例中，您可以看到每個資源的圖格 ([計算]、[網路]、[儲存體和資料] 及 [應用程式]) 都有已識別的問題總數。

![資源安全性健康情況圖格](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>監視計算
當您按一下 [計算] 圖格，您會看到三個索引標籤：

- **概觀**︰監視和建議。
- **VM 和電腦**︰列出所有虛擬機器、電腦及其目前的安全性狀態。
- **雲端服務**︰資訊安全中心監視的所有 Web 和背景工作角色清單。

![遺失的系統更新 (依虛擬機器)](./media/security-center-monitoring/security-center-monitoring-fig1-sep2017.png)

在您可有多個區段的每個索引標籤中，以及在每個區段中，您可以選取一個個別的選項，以查看有關解決該特定問題之建議步驟的更多詳細資訊。 

#### <a name="monitoring-recommendations"></a>監視建議
本節顯示已初始化進行自動佈建的虛擬機器和電腦總數，以及其目前的狀態。 當您按一下此項目時，[VM 代理程式遺失或沒有回應] 隨即開啟。 

![遺失的系統更新 (依虛擬機器)](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)


#### <a name="recommendations"></a>建議
本節提供 Azure 資訊安全中心所監視之[每部虛擬機器和電腦的建議](security-center-virtual-machine-recommendations.md)。 第一個資料行會列出建議。 第二個資料行則顯示受該項建議影響的虛擬機器和電腦總數。 第三個資料行顯示問題的嚴重性，如下列螢幕擷取畫面所示：

![虛擬機器建議](./media/security-center-monitoring/security-center-monitoring-fig2-sep2017.png)

> [!NOTE]
> 只有至少擁有一個公用端點的虛擬機器會顯示在 [網路拓撲] 清單的 [網路健康情況] 中。
>

每個建議在經過點選之後，都會有一組可供執行的動作。 如果，如果您按一下 [遺漏系統更新]，隨即出現遺漏修補程式的虛擬機器和電腦，以及遺漏更新的嚴重性，如下列螢幕擷取畫面中所示：

![虛擬機器遺失的系統更新](./media/security-center-monitoring/security-center-monitoring-fig9-sep2017.png)

[遺漏系統更新] 有圖形格式的重大更新摘要 (一個適用於 Windows，一個適用於 Linux)。 第二部分有包含下列資訊的表格：

* **名稱**：遺漏更新的名稱。
* **VM 和電腦數目**：遺漏此更新的 VM 和電腦總數。
* **狀態**：建議的目前狀態：
  * **未處理**：建議尚未處理。
  * **進行中**：正在將建議套用到資源，且您不需要採取任何動作。
  * **已解決**︰建議動作已完成  (若問題已解決，該項目會呈現暗灰色)。
* **嚴重性**：描述該特定建議的嚴重性：
  * **高**：某個有意義的資源 (應用程式、虛擬機器或網路安全性群組) 有弱點存在，並且需要注意。
  * **中**：需要執行非關鍵性步驟或其他步驟才能完成程序或消除弱點。
  * **低**：應該處理但不需要立即注意的弱點。 (預設不會顯示嚴重性低的建議，但是如果您要檢視它們，則可以篩選嚴重性低的建議)。

若要檢視建議的詳細資料，請按一下清單中遺漏更新的名稱。 

![特定虛擬機器遺失的系統更新](./media/security-center-monitoring/security-center-monitoring-fig4-sep2017.png)

> [!NOTE]
> 這裡的安全性建議與 [建議] 選項中的建議相同。 如需如何處理建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。 這不只適用於虛擬機器和電腦，也適用於 [資源健康情況] 圖格中所有可用的資源。
>

#### <a name="vms--computers-section"></a>VM 和電腦區段
虛擬機器和電腦區段提供所有虛擬機器和電腦的建議概觀。 每個資料行代表一組建議，如下列螢幕擷取畫面所示：

![所有虛擬機器和建議的概觀](./media/security-center-monitoring/security-center-monitoring-fig5-sep2017.png)

這份清單中有四種類型的圖示，如以下清單所說明：

![icon1](./media/security-center-monitoring/security-center-monitoring-icon1.png) 非 Azure 電腦。

![icon2](./media/security-center-monitoring/security-center-monitoring-icon2.png) Azure Resource Manager VM。

![icon3](./media/security-center-monitoring/security-center-monitoring-icon3.png) Azure 傳統 VM。

![icon4](./media/security-center-monitoring/security-center-monitoring-icon4.png) 僅只從已檢視訂用帳戶中的工作區識別的 VM。 這包括來自向此訂用帳戶中的工作區回報之其他訂用帳戶的 VM，以及使用 SCOM 直接代理程式安裝的 VM (沒有資源識別碼)。

每個建議底下出現的圖示，可協助您快速識別需要關注的虛擬機器和電腦，以及建議的類型。 您也可以使用 [篩選] 選項，選取您會在這個畫面上看到的選項。

![Filter](./media/security-center-monitoring/security-center-monitoring-fig6-sep2017.png)

在上述範例中，有一個虛擬機器具有與端點保護有關的重要建議。 若要取得虛擬機器的詳細資訊，請對它按一下：

![虛擬機器安全性詳細資料](./media/security-center-monitoring/security-center-monitoring-fig7-sep2017.png)

您可在此查看虛擬機器或電腦的安全性詳細資料。 您可以在底部看到建議的動作和每個問題的嚴重性。

#### <a name="cloud-services-section"></a>雲端服務區段
對於雲端服務，若作業系統版本過時，便會建立建議，如下列螢幕擷取畫面所示︰

![雲端服務的健康情況狀態](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

在您具有建議的情況下 (這不是前一個範例的案例)，您需要依照建議步驟來更新作業系統版本。 有可用的更新時，您會收到警示 (紅色或橙色 - 取決於問題的嚴重性)。 當您按一下 WebRole1 (使用自動部署至 IIS 的 Web 應用程式執行 Windows Server) 或 WorkerRole1 (使用自動部署至 IIS 的 Web 應用程式執行 Windows Server) 資料列中的這個警示，您可看到這項建議的相關詳細資料，如下列螢幕擷取畫面所示︰

![雲端服務詳細資料](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

若要查看有關這項建議的更多規範說明，請按一下 [描述] 資料行之下的 [更新OS 版本]。 

![雲端服務建議](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>監視虛擬網路
按一下 [網路] 圖格時，隨即開啟包含更多詳細資料的 [網路] 刀鋒視窗，如下列螢幕擷取畫面所示：

![網路刀鋒視窗](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>網路功能的建議
和虛擬機器的資源健康情況資訊一樣，您會在上半部看到問題的摘要清單，在下半部看到受監督的網路清單。

網路狀態明細區段會列出潛在的安全性問題，並提供 [建議](security-center-network-recommendations.md)。 可能的問題包括：

* 未安裝新一代防火牆 (NGFW)
* 未啟用子網路上的網路安全性群組
* 未啟用虛擬機器上的網路安全性群組
* 限制透過公用外部端點的外部存取
* 狀況良好的網際網路面向端點

當您按一下建議時，您會看到有關建議的詳細資料，如下列範例所示：

![網路中的建議詳細資料](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

在此範例中，[設定子網路的遺失網路安全性群組] 有缺少網路安全性群組保護的子網路和虛擬機器清單。 如果您按一下要對其套用網路安全性群組的子網路，您會看到 [選擇網路安全性群組]。 您可以在這裡為子網路選取最適當的網路安全性群組，也可以建立新的網路安全性群組。

#### <a name="internet-facing-endpoints-section"></a>網際網路面向端點區段
在 [網際網路面向端點] 區段中，您可以看到目前使用網際網路面向端點所設定的虛擬機器和其目前的狀態。

![使用網際網路面向端點所設定的虛擬機器和狀態](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

此資料表有代表虛擬機器的端點名稱、網際網路面向的 IP 位址，以及網路安全性群組和 NGFW 目前的嚴重性狀態。 此資料表是依嚴重性排序︰

* 紅色 (在頂端)：高優先順序，應立即處理
* 橘色︰中等優先順序，應儘速處理
* 綠色 (最後一個)︰健康狀態

#### <a name="networking-topology-section"></a>網路拓撲區段
[網路拓撲] 區段具有資源的階層式檢視，如下列螢幕擷取畫面所示：

![網路拓撲區段中資源的階層式檢視](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

此資料表是依嚴重性排序 (虛擬機器和子網路)︰

* 紅色 (在頂端)：高優先順序，應立即處理
* 橘色︰中等優先順序，應儘速處理
* 綠色 (最後一個)︰健康狀態

在此拓撲檢視中，第一層有[虛擬網路](../virtual-network/virtual-networks-overview.md)、[虛擬網路閘道](/vpn-gateway/vpn-gateway-site-to-site-create.md)和[虛擬網路 (傳統)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 第二層有子網路，第三層有屬於這些子網路的虛擬機器。 右側資料行有這些資源的網路安全性群組的目前狀態，如以下範例所示：

![網路拓撲區段中網路安全性群組的狀態](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

此刀鋒視窗的下半部有此虛擬機器的建議 (類似上面所述)。 您可以按一下建議以深入了解或套用所需的安全性控制或組態。

### <a name="monitor-storage--data"></a>監視儲存體和資料

當您按一下 [防護] 區段中的 [儲存體和資料] 時，隨即開啟包含 SQL 和儲存體建議的 [資料資源]。 它也具有資料庫的一般健全狀況狀態 [建議](security-center-sql-service-recommendations.md) 。 如需儲存體加密的詳細資訊，請閱讀[在 Azure 資訊安全中心啟用 Azure 儲存體帳戶的加密](security-center-enable-encryption-for-storage-account.md)。

![資料資源](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

在 [SQL 建議] 底下，您可以按一下任何建議，取得解決問題所需之進一步動作的更多詳細資料。 下列範例顯示已展開的 **SQL Database 上的資料庫稽核和威脅偵測**建議。

![有關 SQL 建議的詳細資料](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

[在 SQL 資料庫上啟用稽核和威脅偵測] 具有下列資訊：

* SQL 資料庫的清單
* 其所在的伺服器
* 此設定是否繼承自伺服器，或此設定在此資料庫中是否為唯一的相關資訊
* 目前的狀態
* 問題的嚴重性

當您在資料庫上按一下以解決這項建議時，便會開啟 [稽核與威脅的偵測]，如下列畫面所示。

![稽核與威脅偵測](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

若要啟用稽核，請選取 [稽核] 選項底下的 [開啟]。

### <a name="monitor-applications"></a>監視應用程式

如果您的 Azure 工作負載有應用程式位於具有公開 Web 連接埠 (TCP 連接埠 80 和 443) 的[虛擬機器 (透過 Azure Resource Manager 所建立)](../azure-resource-manager/resource-manager-deployment-model.md) 中，資訊安全中心即可監視這些應用程式以找出潛在的安全性問題，並建議補救步驟。 當您按一下 [應用程式] 圖格時，便會開啟 [應用程式]，其 [應用程式建議] 區段中會有一系列的建議。 它也會顯示每個主機、IP/網域的應用程式明細，以及是否已安裝 WAF 解決方案：

![應用程式安全性健全狀況](./media/security-center-monitoring/security-center-monitoring-fig8-sep2017.png)

就像您對其他建議的做法一樣，您也可以按一下建議來查看有關問題的更多詳細資訊及其補救方式。 下圖所示範例是識別為不安全的 Web 應用程式之應用程式。 當您選取被視為不安全的應用程式時，有下列選項可用：

![詳細資料](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

這裡會有此應用程式的所有建議清單。 當您按一下 [新增 Web 應用程式防火牆] 建議時，將會開啟 [新增 Web 應用程式防火牆]，其中包含讓您安裝合作夥伴 Web 應用程式防火牆 (WAF) 的選項，如下列螢幕擷取畫面所示。

![新增 Web 應用程式防火牆對話方塊](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>另請參閱
在本文中，您已來了解如何使用「Azure 資訊安全中心」的監視功能。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md)：了解如何在 Azure 資訊安全中心設定安全性設定。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)：尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與相容性的部落格文章。

