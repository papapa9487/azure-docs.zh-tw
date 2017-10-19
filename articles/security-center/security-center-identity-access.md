---
title: "在 Azure 資訊安全中心監視身分識別和存取 | Microsoft Docs"
description: "了解如何在 Azure 資訊安全中心使用身分識別和存取功能，來監視使用者的存取活動和身分識別相關問題。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 842045fbcb5b4f661cc006a207f4087370a7b4ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>在 Azure 資訊安全中心監視身分識別和存取
這篇文章可協助您使用 Azure 資訊安全中心來監視使用者的身分識別和存取活動。

## <a name="why-monitor-identity-and-access"></a>為何要監視身分識別和存取？
身分識別應該是您的企業的控制台，保護您的身分識別應該是您的第一要務。 在過去，組織周圍有周邊，這些周邊是其中一個主要防禦邊界。 現今有越來越多資料和應用程式移至雲端，身分識別成為新的周邊。

監視您的身分識別活動，您就能夠在事件發生前採取主動式動作，或採取回應式動作以停止攻擊。 [身分識別和存取] 儀表板會提供您身分識別狀態的概觀，包括：

* 登入嘗試的失敗次數。 
* 這些嘗試期間所使用的使用者帳戶。
* 已鎖定的帳戶。
* 已變更或重設密碼的帳戶。 
* 目前已登入的帳戶數目。

## <a name="monitor-identity-and-access-activities"></a>監視身分識別和存取活動
若要查看與身分識別和存取相關的目前活動，您需要存取 [身分識別和存取] 儀表板。

1. 開啟 [資訊安全中心] 儀表板。

2. 在左窗格的 [預防] 之下，選取 [身分識別和存取]。 如果您有多個工作區，則會出現工作區選取器。

    ![工作區選取](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > 如果最右側的資料行顯示 [升級計劃]，則此工作區是使用免費訂用帳戶。 升級為「標準」訂用帳戶以使用此功能。 如果最右側的資料行顯示 [需要更新]，則更新 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 以使用這項功能。 如需有關定價方案的詳細資訊，請閱讀資訊安全中心定價。 
    > 
3. 如果您有多個工作區要調查，您可以根據 [惡意 IP] 資料行排定調查的優先順序。 它會顯示此工作區中目前的失敗登入嘗試次數。 選取您要使用的工作區，[身分識別和存取] 儀表板隨即出現。

    ![身分識別和存取](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. 此儀表板中可用的資訊可立即協助您識別潛在的可疑活動。 儀表板分成三個主要區域︰

    a. **身分識別狀態**。 摘要說明在此工作區中進行的身分識別相關活動。

    b. **失敗登入**。 協助您快速識別失敗登入嘗試的主要原因。 顯示登入嘗試失敗的前 10 名帳戶清單。

    c. **不同時間的登入**。 協助您快速識別不同時間的登入次數。 它會顯示最高排名電腦帳戶登入嘗試的清單。
    
不論您選擇哪個圖格，顯示的儀表板都是根據記錄搜尋查詢。 唯一的差別是查詢類型和結果。 您仍可選取一個項目 (例如電腦)，按一下它即可查看相關資料。 

## <a name="see-also"></a>另請參閱
在這篇文章中，您已了解如何在資訊安全中心監視身分識別和存取。 如要深入了解資訊安全中心，請參閱下列文章：

* [在 Azure 資訊安全中心管理和回應安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何在資訊安全中心管理警示，以及回應安全性事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [了解 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同類型的安全性警示。
* 《[Azure 資訊安全中心疑難排解指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)》。 了解如何針對資訊安全中心的常見問題進行疑難排解。 
* [Azure 資訊安全中心常見問題集](security-center-faq.md)。 尋找有關使用資訊安全中心常見問題的答案。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。

