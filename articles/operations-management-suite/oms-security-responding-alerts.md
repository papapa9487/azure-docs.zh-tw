---
title: "在 Operations Management Suite 安全性和稽核解決方案內監視及回應安全性警示 | Microsoft Docs"
description: "這份文件可協助您使用 OMS 安全性和稽核中可用的 [威脅情報] 選項來監視及回應安全性警示。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 7d45a32b-1341-4bb5-a436-1f42a8a2590a
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2017
ms.author: yurid
ms.openlocfilehash: df82afab2c38431e134146143524edc080ee38f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>在 Operations Management Suite 安全性和稽核解決方案內監視及回應安全性警示
這份文件可協助您使用 OMS 安全性和稽核中可用的威脅情報選項來監視及回應安全性警示。

## <a name="what-is-oms"></a>何謂 OMS？
Microsoft Operations Management Suite (OMS) 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。 如需 OMS 的詳細資訊，請閱讀 [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx)一文。

## <a name="threat-intelligence"></a>威脅情報
在使用者可廣泛存取網路且使用各種裝置連接到公司資料的企業環境中，請確定您可以主動監視資源，並快速地回應安全性事件。 從安全性生命週期觀點來看，這點特別重要，因為有些網路安全性威脅可能不會引發可透過傳統安全性技術控制項識別的警示或可疑活動。 

使用 OMS 安全性和稽核中可用的 [威脅情報]  選項，IT 系統管理員可以識別對環境的安全性威脅 (例如，識別特定的電腦是否屬於 [殭屍網路](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection))。 如果攻擊者偷偷地安裝惡意程式碼，暗中將此電腦連接到命令和控制項，則電腦可能會成為殭屍網路中的節點。 它也可以識別來自地下通訊通道 (例如 [暗網](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents)) 的潛在威脅。 

為了建置此威脅情報，OMS 安全性和稽核解決方案會使用來自 Microsoft 內多個來源的資料。 OMS 安全性和稽核將會利用這項資料來識別您環境的潛在威脅。

[威脅情報] 窗格是由三個主要選項所組成︰

* 具有輸出惡意流量的伺服器
* 偵測到的威脅類型
* 威脅情報對應

> [!NOTE]
> 如需所有這些選項的概觀，請參閱[開始使用 Operations Management Suite 安全性和稽核解決方案](oms-security-getting-started.md)。
> 
> 

### <a name="responding-to-security-alerts"></a>回應安全性警示
[安全性事件回應](https://technet.microsoft.com/library/cc512623.aspx) 程序的其中一個步驟是識別危害系統的嚴重性。 在這個階段，您應該執行下列工作︰

* 判斷攻擊本質
* 判斷攻擊源點
* 判斷攻擊意圖。 在組織中特別指示還是隨機指示攻擊取得特定資訊？
* 識別已遭入侵的系統
* 識別已存取的檔案，並判斷這些檔案的敏感度

您可以利用 OMS 安全性和稽核解決方案中的**威脅情報**資訊，協助進行這些工作。 請遵循下列步驟來存取這些**威脅情報**選項：

1. 在 [Microsoft Operations Management Suite] 主儀表板內按一下 [安全性和稽核] 圖格。
   
    ![安全性和稽核](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. 在 [安全性和稽核] 儀表板中，您會在右側看到 [威脅情報] 選項，如下所示︰
   
    ![威脅情報](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

這三個圖格會提供目前威脅的概觀。 在 [具有輸出惡意流量的伺服器]  中，可以找出是否有任何您監視的電腦 (網路內部或外部) 正在將惡意流量傳送到網際網路。 

[偵測到的威脅類型]  圖格會顯示目前偵測到的威脅摘要，如果您按一下此圖格，會看到這些威脅的詳細資訊，如下所示︰

![偵測到的威脅類型](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

您可以按一下每個威脅，以擷取其詳細資訊。 下列範例顯示殭屍網路的詳細資訊：

![威脅的詳細資料](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

如本節開頭所述，此資訊在事件回應案例期間可能會很有幫助。 在需要找出攻擊來源、遭入侵系統和時間表的鑑識調查期間，這項資訊也十分重要。 在這份報告中，您可以輕鬆地識別攻擊的一些重要詳細資料，例如︰攻擊來源、遭入侵的本機 IP，以及連接的目前工作階段狀態。 

**威脅情報對應** 將協助您識別全球各地目前有惡意流量的位置。 這個對應中的橙色 (內送) 和紅色 (外寄) 箭號識別流量方向，如果您按一下其中一個箭號，則會顯示威脅類型和流量方向，如下所示︰

![威脅情報對應](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [!NOTE]
> 您可以觀看 Microsoft Ignite 所提供的簡報[使用 Operations Management Suite 以引導式調查減輕資料中心的安全性威脅](https://myignite.microsoft.com/videos/5000)，查看如何在事件回應程序期間使用這項功能的示範。
> 

### <a name="responding-to-distinct-malicious-ip-accessed"></a>回應存取的相異惡意 IP
在某些情況下，您可能會發現已從一部受監視的電腦存取潛在惡意 IP：

![威脅情報對應](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig6.PNG)

會透過 OMS 安全性產生此警示以及其他相同類別中的警示，方法是運用 [Microsoft 威脅情報](https://youtu.be/O4WtxgUrDc8)。 威脅情報資料是由 Microsoft 向主要威脅情報提供者所收集及購買。 這項資料會經常更新，且適用於快速移動威脅。 本質上，在[調查](https://blogs.technet.microsoft.com/msoms/2016/12/08/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/)安全性警示時，應將它與其他安全性資訊的來源進行結合。 

## <a name="customize-alerts-received-via-e-mail"></a>自訂透過電子郵件接收的警示

您可以自訂在 OMS 安全性觸發安全性警示時，組織內的哪些使用者會收到通知。 此選項可在 OMS 儀表板的 [概觀 / 設定] 下找到：

![電子郵件](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig7.png)

## <a name="see-also"></a>另請參閱
在這份文件中，您了解到如何使用 OMS 安全性和稽核解決方案中的 [威脅情報]  選項來回應安全性警示。 若要深入了解 OMS 安全性，請參閱下列文章：

* [Operations Management Suite (OMS) 概觀](operations-management-suite-overview.md)
* [開始使用 Operations Management Suite 安全性和稽核解決方案](oms-security-getting-started.md)
* [在 Operations Management Suite 安全性和稽核解決方案內監視資源](oms-security-monitoring-resources.md)

