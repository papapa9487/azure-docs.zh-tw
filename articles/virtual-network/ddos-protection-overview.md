---
title: "Azure DDoS Protection Standard 概觀 | Microsoft Docs"
description: "深入了解 Azure DDoS Protection 服務。"
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 0130823b6a6f5a4883ad640c9bcefc89b82b2cd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection Standard 概觀

>[!IMPORTANT]
>Azure DDoS Protection Standard 目前為預覽狀態。 支援 DDoS Protection Standard 的 Azure 資源數量有限，並在幾個精選的區域提供支援。 您必須在有限的預覽期間[註冊此服務](http://aka.ms/ddosprotection)，才能為您的訂用帳戶啟用 DDoS Protection Standard。 註冊時，Azure DDoS 小組會與您連絡，引導您完成啟用程序。 DDoS Protection Standard 適用於美國東部、美國西部和美國中部等美國地區。 預覽版期間，使用該服務無須付費。

分散式阻絕服務 (DDoS) 攻擊是將應用程式移至雲端的客戶所面臨的最大可用性和安全性顧慮之一。 DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定可透過網際網路公開觸達的任何端點。

結合應用程式設計最佳做法的 Azure DDoS Protection 可提供這些攻擊的防禦。 提供以下兩個服務層： 

- **Azure DDoS Protection 基本** - 已在 Azure 平台中自動啟用，不需額外付費。 常見網路層級攻擊的永遠可用流量監視和即時安全防護功能，可提供 Microsoft 的線上服務所使用的相同防禦。  Azure 全域網路的整體規模可用於分散和減少跨區域攻擊流量。 
- **Azure DDoS Protection Standard** - 提供特別針對虛擬網路資源調整的額外安全防護功能。 它很容易啟用，而且不需要變更應用程式。 保護原則會透過專用流量監視和機器學習演算法進行調整，並套用至與虛擬網路資源相關聯的公用 IP，例如負載平衡器、應用程式閘道和 Service Fabric 執行個體。  在攻擊期間，可透過 Azure 監視器檢視取得即時遙測歷程記錄。 透過[應用程式閘道 Web 應用程式防火牆](https://azure.microsoft.com/services/application-gateway/)可以新增應用程式層保護。 

![Azure DDoS Protection Standard](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

在開發和測試案例中，歡迎您試用 DDoS Protection Standard 並利用下列資源來提供您的經驗意見反應：
- [Microsoft Azure 論壇上的 Azure DDoS Protection](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [MSDN 論壇上的 Azure DDoS Protection](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Stack Overflow 上的 Azure DDoS Protection](https://stackoverflow.com/tags/azure-ddos/info)

如需支援問題，您可以[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md)。

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS Protection Standard 可降低風險的 DDoS 攻擊類性

DDoS Protection Standard 可降低下列攻擊類型的風險：

- **巨流量攻擊** - 此攻擊的目的在於以大量看似合法的流量填滿網路層。 它包括 UDP 洪水攻擊、放大洪水攻擊和其他詐騙封包洪水攻擊。 DDoS Protection Standard 可降低這些潛在多 GB 攻擊的風險，方法是自動運用 Azure 的全域網路規模來快吸並清除這些攻擊。 
- **通訊協定攻擊** - 這些攻擊所呈現的目標是無法利用第 3 層和第 4 層通訊協定堆疊中的弱點來存取。 它包括 SYN 洪水攻擊、反映攻擊和其他通訊協定攻擊。 DDoS Protection Standard 可藉由與用戶端互動來區別惡意與合法流量並封鎖惡意流量，以降低這些攻擊的風險。 
- **應用程式層攻擊** - 這些攻擊會鎖定 Web 應用程式封包，以中斷主機之間的資料傳輸。 這包括 HTTP 通訊協定違規、SQL 插入、跨網站指令碼和其他第 7 圖攻擊。 使用應用程式閘道 WAF 搭配 DDoS Protection Standard，以提供這些攻擊的防禦。 

DDoS Protection Standard 可保護虛擬網路中的資源，包括與 VM 相關聯的公用 IP、內部負載平衡器，以及應用程式閘道。 與應用程式閘道 WAF SKU 搭配的 DDoS Protection Standard 可以提供完整的 L3 至 L7 安全防護功能。

## <a name="ddos-protection-standard-features"></a>DDoS Protection Standard 功能

![DDoS 功能](./media/ddos-protection-overview/ddos-overview-fig1.png)

DDoS Protection Standard 功能包括： 

- **原生平台整合：**DDoS Protection Standard 以原生方式整合到 Azure，包含透過 Azure 入口網站和 PowerShell 進行設定。 DDoS Protection Standard 了解您的資源和資源組態。
- **永遠可用流量監視：**您的應用程式流量模式受到全天候的監視，以尋找 DDoS 攻擊的指標。 超出保護原則時，就會執行安全防護功能。
- **現成的保護：**經過簡化的組態會在啟用 DDoS Protection Standard 後，立即保護虛擬網路上的所有資源。 不需要進行操作或使用者定義 - 一旦偵測到攻擊，DDoS Protection Standard 就會立即自動降低攻擊風險。
- **自適性調整：**智慧型流量剖析功能可了解不同時間的應用程式流量，並選取及更新最適合您服務的設定檔。 設定檔會隨著時間調整流量變更。
- **使用應用程式閘道的 L3 至 L7 保護：**應用程式閘道 WAF 功能可提供完整的堆疊 DDoS Protection。
- **廣泛的安全防護範圍：**可利用全域功能降低超過 60 種不同攻擊類型的風險，以抵禦最大的已知 DDoS 攻擊。 
- **攻擊計量：**透過 Azure 監視器可以存取每個攻擊的摘要計量。
- **攻擊警示：**警示可設定為在開始和停止攻擊時，並且在攻擊的持續時間內使用內建攻擊計量。 警示會整合到您的作業軟體，例如 OMS、Splunk、Azure 儲存體、電子郵件和 Azure 入口網站。
- **成本保證：**資料傳輸和應用程式相應放大服務會針對記載的 DDoS 攻擊計算點數。

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection Standard 安全防護功能

Microsoft 的 DDoS Protection 服務會監視實際流量使用率，且時常將它與 DDoS 原則中定義的閾值比較。 當超過該流量閾值時，就會自動起始 DDoS 安全防護功能。 當傳回流量低於閾值時，就會移除安全防護功能。

在安全防護期間，DDoS Protection 服務會重新導向送往受保護資源的流量，而且不會執行數個檢查。 這些檢查通常會執行下列功能：

- 確定封包符合網際網路規格，且格式正確無誤。
- 與用戶端互動，判斷它是否可能是詐騙封包 (例如： SYN Auth 或 SYN Cookie 或置放封包以供來源將它重新傳輸)。
- 速率限制封包 (如果沒有其他強制方法可以執行)。

DDoS Protection 會封鎖攻擊流量並將剩餘流量轉送至目的地。 在偵測到攻擊的幾分鐘內，系統會使用 Azure 監視器計量通知您。 藉由設定登入 DDoS Protection Standard 遙測，您可以將記錄寫入至可用的選項，以供日後分析。 適用於 DDoS Protection Standard 的 Azure 監視中的計量資料目前會保留 30 天。

不建議客戶模擬其自己的 DDoS 攻擊。 客戶可改為使用支援通道來要求由 Azure 網路執行 DDoS 攻擊的模擬。 工程師會連絡您，準備 DDoS 攻擊的詳細資料 (連接埠、通訊協定、目標 IP) 以及安排時間來排程測試。

## <a name="next-steps"></a>後續步驟

- 了解如何使用 [Azure PowerShell](ddos-protection-manage-ps.md) 或 [Azure 入口網站](ddos-protection-manage-portal.md)管理 DDoS Protection Standard。