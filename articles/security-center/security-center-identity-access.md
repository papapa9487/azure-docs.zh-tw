---
title: "在 Azure 資訊安全中心監視身分識別和存取 | Microsoft Docs"
description: "本文件可協助您在 Azure 資訊安全中心使用身分識別和存取功能，來監視使用者的存取活動和身分識別相關問題。"
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
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>在 Azure 資訊安全中心監視身分識別和存取
本文件可協助您使用 Azure 資訊安全中心來監視使用者的身分識別和存取活動。

## <a name="why-monitor-identity-and-access"></a>為何要監視身分識別和存取？
身分識別應該是您的企業的控制台，保護您的身分識別應該是您的第一要務。 雖然過去組織周圍有一些周邊，而這些周邊是其中一個主要防禦界限，但時至今日有更多資料和更多應用程式移到雲端，身分識別便成為新的周邊。

監視您的身分識別活動，您就能夠在事件發生前採取主動式動作，或採取回應式動作以停止攻擊。 [身分識別和存取] 儀表板會提供您的身分識別狀態概觀，包括嘗試登入失敗的數目、在這些嘗試期間使用的使用者帳戶、已鎖定的帳戶、已變更或重設密碼的帳戶，以及目前登入的帳戶數目。

## <a name="how-to-monitor-identity-and-access-activities"></a>如何監視身分識別和存取活動？
依照下列步驟，將目前活動相關身分識別和存取視覺化，您需要存取 [身分識別和存取] 儀表板：

1.  開啟 [資訊安全中心] 儀表板。
2.  在左窗格的 [預防] 之下，按一下 [身分識別和存取]。 如果您有多個工作區，則會出現工作區選取器。

    ![工作區選取](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > 如果最後一個資料行顯示 [升級計畫]，是因為此工作區正在使用免費訂用帳戶，您必須升級至標準版才能使用此功能。 如果是顯示 [需要更新]，是因為您必須更新 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 才能使用這項功能。 如需有關定價方案的詳細資訊，請閱讀 Azure 資訊安全中心定價。 
    > 
3. 如果您要調查多個工作區，可以根據顯示此工作區中目前嘗試登入失敗次數的 [失敗登入] 資料行來設定調查優先順序。 選取您要使用的工作區，[身分識別和存取] 儀表板隨即出現。

    ![身分識別與存取](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. 此儀表板中可用的資訊可立即協助您識別潛在的可疑活動。 此儀表板分成三個主要區域︰
    * **身分識別狀態**：摘要說明在此工作區中進行的身分識別相關活動。
    * **失敗登入**：協助您快速識別嘗試登入失敗的主要原因，並顯示前十個最失敗之登入嘗試的帳戶清單。
    * **登入逾時**：協助您快速識別登入逾時量，並顯示排前幾名的電腦帳戶登入嘗試清單。
    
不論您是選擇哪個圖格，所出現的儀表板都會根據 [記錄搜尋][](https://docs.microsoft.com/azure/security-center/security-center-search) 查詢而定，唯一的差別是查詢類型和結果。 您仍可選取一個項目 (例如電腦)，按一下它即可查看相關資料。 

## <a name="see-also"></a>另請參閱
在本文件中，您已了解如何在 Azure 資訊安全中心監視身分識別和存取。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [管理及回應 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何在資訊安全中心管理警示，以及回應安全性事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [了解 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同類型的安全性警示。
* [Azure 資訊安全中心疑難排解指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何針對資訊安全中心的常見問題進行疑難排解。 
* [Azure 資訊安全中心常見問題集](security-center-faq.md)。 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。


