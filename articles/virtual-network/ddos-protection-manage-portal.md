---
title: "使用 Azure 入口網站管理 Azure DDoS Protection Standard | Microsoft Docs"
description: "了解如何使用 Azure 監視器中的 Azure DDoS Protection Standard 遙測來降低攻擊風險。"
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
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 5c599b4cc867dbc9a081af3a081195b998f63954
ms.contentlocale: zh-tw
ms.lasthandoff: 09/26/2017

---

# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>使用 Azure 入口網站管理 Azure DDoS Protection Standard

>[!IMPORTANT]
>Azure DDoS Protection Standard (DDoS Protection) 目前為預覽狀態。 支援 DDoS Protection 的 Azure 資源數量有限，並在幾個精選的區域提供支援。 您必須在有限的預覽期間[註冊此服務](http://aka.ms/ddosprotection)，才能為您的訂用帳戶啟用 DDoS Protection。 註冊時，Azure DDoS 小組會與您連絡，引導您完成啟用程序。 DDoS Protection 適用於美國東部、美國西部和美國中部等美國地區。 預覽版期間，使用該服務無須付費。

本文說明如何使用 Azure 入口網站來啟用 DDoS Protection、停用 DDoS Protection，以及使用遙測來降低攻擊風險。 

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="enable-ddos-protection"></a>啟用 DDoS Protection

在新的或現有虛擬網路上啟用 DDoS Protection。

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>建立新的虛擬網路和並啟用 DDoS Protection

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。
2. 選取 [網絡]，然後選取 [虛擬網路]。
3. 輸入虛擬網路資訊。 在 DDoS Protection 之下，按一下 已啟用，然後按一下建立。

    ![建立虛擬網路](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

系統會出現一則警告，聲明啟用 DDoS Protection 會產生費用。 在預覽期間，DDoS Protection 不會產生任何費用。 在公開上市 (GA) 時會產生費用，而客戶會在開始收費和 GA 的前 30 天收到通知。

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>在現有虛擬網路上啟用 DDoS Protection 

1. 按一下 Azure 入口網站功能表中的 [虛擬網路]，然後選取您的虛擬網路。
2. 按一下 DDoS Protection，按一下 DDoS Protection 畫面上的 已啟用，然後按一下儲存。 

系統會出現一則警告，聲明啟用 DDoS Protection 會產生費用。 在預覽期間，DDoS Protection 不會產生任何費用。 在公開上市 (GA) 時會產生費用，而客戶會在開始收費和 GA 的前 30 天收到通知。

## <a name="disable-ddos-protection"></a>停用 DDoS Protection

1. 按一下 Azure 入口網站功能表中的 [虛擬網路]，然後選取您的虛擬網路。
2. 按一下 DDoS Protection，按一下 DDoS Protection 畫面上的 已停用，然後按一下儲存。

## <a name="configure-alerts-on-ddos-protection-metrics"></a>設定 DDoS Protection 計量的警示

運用 Azure 監視器的警示設定，您可以選取任何可用的 DDoS Protection 計量，以在攻擊期間警示何時有作用中的安全防護功能。 當條件符合時，您會透過指定的地址收到警示電子郵件。

1. 按一下 監視器，然後按一下計量。
2. 在 [計量] 畫面上，選取資源群組、[公用 IP 位址] 資源類型，以及您的 Azure 公用 IP。
3. 若要對計量設定電子郵件警示，請按一下 [按一下以新增警示]。 您可對任何計量建立電子郵件警示，但最明顯的計量是 [是否正遭受 DDoS 攻擊]。 這是布林值 1 或 0。 **1** 表示您正遭受攻擊。 **0** 表示並未遭受攻擊。
4. 若要在遭受攻擊時收到電子郵件，將設定 [是否正遭受 DDoS 攻擊] 和 [過去 5 分鐘大於零 (0) 的條件] 的計量。 您可以對其他計量設定類似的警示。

    ![設定計量](./media/ddos-protection-manage-portal/ddos-metrics.png)

    在偵測到攻擊的幾分鐘內，系統會使用 Azure 監視器計量通知您。

    ![攻擊警示](./media/ddos-protection-manage-portal/ddos-alert.png) 

您也可以深入了解如何[設定 Webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)和[邏輯應用程式](../logic-apps/logic-apps-what-are-logic-apps.md)，以便建立警示。

## <a name="configure-logging-on-ddos-protection-metrics"></a>設定 DDoS Protection 計量的記錄

1. 按一下 監視器，然後按一下診斷設定。
2. 在 [計量] 畫面上，選取資源群組、[公用 IP 位址] 資源類型，以及您的 Azure 公用 IP。
3. 按一下 [開啟診斷以收集下列資料]。

有三個選項可供記錄：

- **封存至儲存體帳戶** – 將記錄寫入至儲存體帳戶。
- **串流至事件中樞** – 可讓記錄接收者透過事件中樞挑選記錄。 這能夠與 Splunk 或其他 SIEM 系統整合。
- **傳送至 Log Analytics** – 將資料寫入至 Azure OMS Log Analytics 服務。

## <a name="use-ddos-protection-telemetry"></a>使用 DDoS Protection 遙測

攻擊的遙測可透過 Azure 監視器即時提供。 遙測只適用於公用 IP 位址在安全防護之下的期間。 在攻擊風險降低之前或之後，您都看不到遙測。

1. 按一下 監視器，然後按一下計量。 
2. 在 [計量] 畫面上，選取資源群組、[公用 IP 位址] 資源類型，以及您的 Azure 公用 IP。 畫面的左側會出現一系列可用的計量。 若選取這些計量，這些度量會在概觀畫面上的 Azure 監視器計量圖中繪製成圖形。 

計量名稱呈現不同的套件類型和位元組及封包，包含每個計量的標籤名稱基本結構，如下所示：

- **捨棄的標籤名稱 (例如：捨棄的輸入封包 DDoS)：** DDoS Protection 系統所捨棄/清除的封包數目。
- **轉送的標籤名稱 (例如：轉送的輸入封包 DDoS)：**DDoS 系統轉送到目的地 VIP 的封包數目 – 未篩選的流量。
- **沒有標籤名稱 (例如：輸入封包 DDoS)：**進入清除系統的封包總數 – 表示捨棄和轉總的封包總和。

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure 診斷記錄檔](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [使用 Log Analytics 分析來自 Azure 儲存體的記錄](../log-analytics/log-analytics-azure-storage.md)
- [開始使用事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
