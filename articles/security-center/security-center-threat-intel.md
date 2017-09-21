---
title: "Azure 資訊安全中心內的威脅情報 | Microsoft Docs"
description: "本文件可協助您使用 Azure 資訊安全中心內的威脅情報功能，來識別您 VM 和電腦中的潛在威脅。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Azure 資訊安全中心內的威脅情報
本文件可協助您使用 Azure 資訊安全中心威脅情報來解決安全性相關問題。

## <a name="what-is-threat-intelligence"></a>何謂威脅情報？
使用安全性中心可用的 [威脅情報] 選項，IT 系統管理員可以識別對環境的安全性威脅 (例如，識別特定的電腦是否屬於殭屍網路)。 如果攻擊者偷偷地安裝惡意程式碼，暗中將此電腦連接到命令和控制項，則電腦可能會成為殭屍網路中的節點。 它也可以識別來自地下通訊通道 (例如暗網) 的潛在威脅。

為了建置此威脅情報，安全性中心會使用來自 Microsoft 內多個來源的資料。 安全性中心將會利用這項資料來識別您環境的潛在威脅。 [威脅情報] 窗格是由三個主要選項所組成︰

- 偵測到的威脅類型
- 威脅來源
- 威脅情報對應


## <a name="when-should-i-use-threat-intelligence"></a>何時應該使用威脅情報？
[安全性事件回應程序](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response)的其中一個步驟是識別危害系統的嚴重性。 在這個階段中，您應該執行下列工作︰

- 判斷攻擊本質
- 判斷攻擊源點
- 判斷攻擊意圖。 在組織中特別指示還是隨機指示攻擊取得特定資訊？
- 識別已遭入侵的系統
- 識別已受存取的檔案，並判斷您可以在資訊安全中心利用威脅情報資訊的檔案機密性，從而協助進行這些工作。 

## <a name="how-to-access-the-threat-intelligence"></a>如何存取威脅情報？
若要以視覺化方式檢視目前環境的威脅情報，您必須先選取您資訊所在的工作區。 如果您沒有多個工作區，就不會看到工作區選取器，它會直接跳至 [威脅情報] 儀表板。 請遵循下列步驟來存取 [威脅情報] 儀表板：

1.  開啟 [資訊安全中心] 儀表板。
2.  在左窗格中，按一下 [偵測] 下的 [威脅情報]。 [威脅情報] 儀表板隨即出現。

    ![威脅情報](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > 如果最後一個資料行顯示 [升級計畫]，是因為此工作區正在使用免費訂用帳戶，您必須升級至標準版才能使用此功能。 如果是顯示 [需要更新]，是因為您必須更新 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 才能使用這項功能。 如需有關定價方案的詳細資訊，請閱讀 Azure 資訊安全中心定價。 
    > 
3. 如果您要調查多個工作區，可以根據顯示此工作區中惡意 IP 目前數目的 [惡意 IP] 資料行來設定調查優先權。 選取您需要使用的工作區，[威脅情報] 儀表板隨即出現。

    ![威脅情報](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. 此儀表板會分成四個圖格︰
    * **威脅類型**：會摘要說明選取工作區中偵測到的威脅類型。
    * **來源國家/地區**：會根據流量的來源位置來彙總流量。
    * **威脅位置**：協助您識別在世界各地與您環境通訊的目前位置。 這個對應中的橙色 (內送) 和紅色 (外寄) 箭號會識別流量方向，如果您按一下其中一個箭號，就會顯示威脅類型和流量方向。
    * **威脅詳細資料**：會顯示您在對應中選取的威脅詳細資料。

不論您是選擇哪個選項圖格，所出現的儀表板都會以 [記錄搜尋][](https://docs.microsoft.com/azure/security-center/security-center-search) 查詢作為基礎，唯一的差別是查詢類型和結果。

### <a name="threat-types"></a>威脅類型
按一下 [威脅類型]圖格後，[記錄搜尋] 儀表板隨即出現，您在左側會看到篩選條件選項，在右側則會看到查詢結果。

![威脅情報搜尋](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

查詢結果會依名稱向彙總顯示威脅，您可以使用左側窗格來選取您需要篩選的屬性，例如，如果您只需要查看連線到電腦的最新威脅，就要在 **SESSIONSTATE** 中選取 [已連線]，然後按一下 [套用] 按鈕。

![工作階段狀態](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

針對 Azure VM，只有流經代理程式的網路資料會顯示在威脅情報儀表板中。 威脅情報也會使用下列資料類型：

- CEF 資料 (Type=CommonSecurityLog)
- WireData (Type= WireData)
- IIS 記錄 (Type=W3CIISLog)
- Windows 防火牆 (Type=WindowsFirewall)
- DNS 事件 (Type=DnsEvents)


## <a name="see-also"></a>另請參閱
在本文件中，您已學會如何使用資訊安全中心的威脅情報，可協助您識別可疑的活動。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [管理及回應 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何在資訊安全中心管理警示，以及回應安全性事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [了解 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同類型的安全性警示。
* [Azure 資訊安全中心疑難排解指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何針對資訊安全中心的常見問題進行疑難排解。 
* [Azure 資訊安全中心常見問題集](security-center-faq.md)。 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。


