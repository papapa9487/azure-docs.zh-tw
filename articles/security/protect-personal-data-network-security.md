---
title: "使用 Azure 網路安全性功能來保護個人資料 | Microsoft Docs"
description: "使用 Azure 網路安全性功能來保護個人資料"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: d61b29f1327f57bc32b2c53de3fe58e53fcf3cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>使用網路安全性功能來保護個人資料：Azure 應用程式閘道和網路安全性群組

本文提供的資訊和程序可協助您使用 Azure 應用程式閘道和網路安全性群組來保護個人資料。

在多層式安全性策略中用來保護個人資料隱私權的重要元素，就是防禦 SQL 插入或跨網站指令碼等常見的弱點惡意探索。 讓您的 Azure 虛擬網路避開不必要的網路流量，有助於防止潛在的敏感性資料外洩，而 Microsoft Azure 提供工具來協助您的資料抵禦攻擊者。

## <a name="scenario"></a>案例

總部設於美國的大型郵輪公司將擴展其營運，以提供地中海、亞得里亞海、波羅的海和不列顛群島的行程。 為了促成這些工作，它收購了以義大利、德國、丹麥和英國為據點的數個較小型郵輪公司。

該公司使用 Microsoft Azure 在雲端儲存公司資料，並在虛擬機器上執行可處理及存取此資料的應用程式。 此資料包含其全球客戶群的名稱、地址、電話號碼和信用卡資訊等個人識別資訊。 它也會包含傳統人力資源資訊，例如地址、電話號碼、稅務識別碼，以及有關各地公司員工的其他資訊。 此郵輪公司也會維護獎勵和忠誠度方案會員的大型資料庫，其中包含的個人資訊可追蹤與目前和過去客戶的關係。

公司員工會從公司的遠端辦公室存取網路，而位於全球的旅遊代理商會存取某些公司資源並使用 Azure VM 中裝載的 Web 架構應用程式來與它互動。

## <a name="problem-statement"></a>問題陳述

該公司必須保護客戶和員工的個人資料隱私權，防止攻擊者利用軟體弱點來執行惡意程式碼，而惡意程式碼可能會公開公司雲端應用程式所儲存或使用的個人資料。

## <a name="company-goal"></a>公司目標

該公司的目標在於確保未經授權的人員無法利用一般弱點來存取公司的 Azure 虛擬網路，以及位於其中的應用程式和資料。 

## <a name="solutions"></a>解決方案

Microsoft Azure 提供安全性機制來協助防止不必要的流量進入 Azure 虛擬網路。 輸入和輸出流量的控制傳統上是由防火牆執行。 在 Azure 中，您可以使用應用程式閘道搭配 Web 應用程式防火牆和網路安全性群組 (NSG)，以作為簡單的分散式防火牆。 這些工具可讓您偵測及封鎖不必要的網路流量。

### <a name="application-gatewayweb-application-firewall"></a>應用程式閘道/Web 應用程式防火牆

[Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)的 [Web 應用程式防火牆](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF) 元件可保護 Web 應用程式，而 Web 應用程式已逐漸成為利用常見已知弱點的惡意攻擊目標。 集中式 WAF 可以防禦 Web 攻擊並簡化安全性管理，而不需要變更任何應用程式。

Azure WAF 可處理各種攻擊類別，包括 SQL 插入、跨網站指令碼、HTTP 通訊協定違規和異常、Bot、編目程式、掃描程式、常見應用程式錯誤設定、HTTP 阻絕服務，以及其他常見的攻擊，例如命令插入、HTTP 要求、走私、HTTP 回應分割及遠端檔案包含攻擊。 

您可以建立具有 WAF 的應用程式閘道，或將 WAF 新增至現有的應用程式閘道。 在上述任何情況下，Azure 應用程式閘道都需要有自己的子網路。

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>如何建立具有 WAF 的應用程式閘道？ 

若要建立已啟用 WAF 的新應用程式閘道，請執行下列動作：

1. 登入 Azure 入口網站，並且在入口網站的 [我的最愛] 窗格中，按一下 [新增]

2. 在 [新增] 刀鋒視窗中，按一下 [網路]。

3. 按一下 [應用程式閘道]。

4. 瀏覽至 Azure 入口網站，按一下 [新增]\> [網路] \> [應用程式閘道]。

   ![建立應用程式閘道](media/protect-netsec/app-gateway-01.png)

5. 在出現的 [基本概念] 刀鋒視窗中，輸入下列欄位的值：名稱、層 (標準或 WAF)、SKU 大小 (小、中或大)、執行個體計數 (2 代表高可用性)、訂用帳戶、資源群組及位置。

6. 在 [虛擬網路] 底下顯示的 [設定] 刀鋒視窗中，按一下 [選擇虛擬網路]。 此步驟會開啟 [選擇虛擬網路] 刀鋒視窗。

7. 按一下 [建立新項目] 以開啟 [建立虛擬網路] 刀鋒視窗。

8. 輸入下列值：名稱、位址空間、子網路名稱、子網路位址範圍。 按一下 [確定] 。

9. 在 [前端 IP 組態] 底下的 [設定] 刀鋒視窗中，選擇 IP 位址類型。

10. 依序按一下 [選擇公用 IP 位址] 和 [新建]。

11. 接受預設值，然後按一下 [確定]。

12. 在 [接聽程式組態] 底下的 [設定] 刀鋒視窗中，選擇使用 [通訊協定] 底下的 HTTP 或 HTTPS。 若要使用 HTTPS，需要有憑證。

13. 進行 WAF 特定設定：[防火牆狀態]\(已啟用) 和 [防火牆模式] \(**防護**)。 如果您選擇 [偵測] 模式，則只會記錄流量。

14. 檢閱 [摘要] 頁面，然後按一下 [確定]。 現在應用程式閘道已排入佇列並建立。

建立應用程式閘道之後，您可以在入口網站中瀏覽至該閘道，以繼續設定應用程式閘道的組態。

![建立應用程式閘道](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>如何將 WAF 新增至現有的應用程式？

若要更新現有的應用程式閘道，以在防護模式中支援 WAF，請執行下列作業：

1. 在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。

2. 按一下 [所有資源] 刀鋒視窗中的應用程式閘道。 
>[!NOTE]
注意：如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選...] 方塊中輸入名稱， 輕鬆地存取 DNS 區域。
3. 按一下 [Web 應用程式防火牆] 並更新應用程式閘道設定：[升級為 WAF 層] \(已核取)、[防火牆狀態] \(已啟用)、     [防火牆模式] \(防護)。 您也需要設定規則集，以及設定已停用的規則。

有關如何建立具有 WAF 的新應用程式閘道以及如何將 WAF 新增至現有應用程式閘道的詳細資訊，請參閱[使用入口網站建立具有 Web 應用程式防火牆的應用程式閘道](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)。

### <a name="network-security-groups"></a>網路安全性群組

[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) 包含安全性規則的清單，可允許或拒絕已連線至 [Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/) (VNet) 之資源的網路流量。 NSG 可與子網路或個別 VM 建立關聯。 當 NSG 與子網路相關聯時，系統會將規則套用至已連線至子網路的所有資源。 建立 NSG 與 VM 或 NIC 的關聯也可以進一步限制流量。

NSG 包含四個屬性：名稱、地區、資源群組和規則。
>[!Note]
雖然 NSG 存在於資源群組中，它可以與任何資源群組中的資源相關聯，只要資源是與 NSG 相同的 Azure 區域的一部分。

NSG 規則包含 9 個屬性：名稱、通訊協定 (TCP、UDP 或 \*，其中包括 ICMP 以及 UDP 和 TCP)、來源連接埠範圍、目的地連接埠範圍、來源位址前置詞、目的地位址前置詞、方向 (輸入或輸出)、優先順序 (介於 100 與 4096 之間) 和存取類型 (允許或拒絕)。 所有 NSG 都包含一組預設規則，其可由您所建立的規則刪除或覆寫。

#### <a name="how-do-i-implement-nsgs"></a>如何實作 NSG？

實作 NSG 需要規劃，而且您需要顧及數個設計考量。 這些包括每個訂用帳戶的 NSG 數目和每個 NSG的規則數之限制；VNet 和子網路設計、特殊規則、ICMP 流量、隔離存取層與子網路、負載平衡器等等。

如需規劃及實作 NSG 的詳細指引以及範例部署案例，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)。

#### <a name="how-do-i-create-rules-in-an-nsg"></a>如何在 NSG 中建立規則？

若要在現有 NSG 中建立輸入規則，請執行下列作業：

1. 依序按一下 [瀏覽] 和 [網路安全性群組]。

2. 在 NSG 清單中按一下 [NSG-FrontEnd]，然後按一下 [輸入安全性規則]。

3. 在 [輸入安全性規則] 清單中，按一下 [新增]。

4. 在下列欄位中輸入值：名稱、優先順序、來源、通訊協定、來源範圍、目的地、目的地連接埠範圍和動作。

幾秒之後，新規則會出現在 NSG 中。

![網路安全性規則](media/protect-netsec/inbound-security.png)

有關如何在子網路中建立 NSN、建立規則，並且讓 NSG 與前端和後端子網路產生關聯的詳細指示，請參閱[使用 Azure 入口網站建立網路安全性群組](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)。

## <a name="next-steps"></a>後續步驟

[Azure 網路安全性](https://azure.microsoft.com/blog/azure-network-security/)

[Azure 網路安全性最佳做法](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[取得網路安全性群組的相關資訊](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Web 應用程式防火牆 (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
