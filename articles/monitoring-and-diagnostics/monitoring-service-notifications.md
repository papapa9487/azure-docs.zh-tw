---
title: "什麼是服務健康狀態通知 | Microsoft Docs"
description: "藉由服務健康狀態通知，您可以檢視由 Microsoft Azure 發佈的服務健康狀態訊息。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: d85281c02b792921f12cc62e6d60bef3e7c13b3f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="service-health-notifications"></a>服務健康狀態通知
## <a name="overview"></a>概觀

本文將說明如何使用 Azure 入口網站檢視服務健康狀態通知。

藉由服務健康狀態通知，您可以檢視由 Azure 團隊發佈的服務健康狀態訊息，而這些訊息可能正影響著您訂用帳戶下的資源。 這些通知是活動記錄事件的子類別，在活動記錄刀鋒視窗上也找的到。 根據不同類別，服務健康狀態通知可以是告知性質或是可操作的性質。

服務健康狀態通知有五種類別：  

- **需要採取動作：**有時我們會注意到您的帳戶中有異常狀況。 可能需要您的協助以解決問題。 我們會傳送通知給您，其中會詳述您需要採取的動作，或是附上 Azure 工程或支援的詳細連絡方式。  
- **協助復原︰**事件已發生，且工程師確認您仍受到影響。 工程人員將需要直接與您一起將服務還原。  
- **附帶事件 (Incident)︰**受到服務影響的事件 (event) 目前正在影響您訂用帳戶下一個或多個資源。  
- **維護︰**此通知是告知您會有計劃性維護活動，可能會影響您訂用帳戶下一個或多個資源。  
- **資訊：**有時我們會傳送通知給您，告知您可能有最佳化方法可協助您改善資源使用率。  
- **安全性︰**緊急的安全性相關資訊，關係到 Azure 上正在執行的解決方案。

每個服務健康狀態通知皆會傳達關於且影響您資源的詳細資料。 詳細資料包括：

屬性名稱 | 說明
-------- | -----------
通道 | 為下列其中一個值：Admin、Operation
correlationId | 通常是字串格式的 GUID。 屬於相同 uber 動作的事件通常會共用同一個 correlationId。
eventDataId | 事件的唯一識別碼
eventName | 事件的標題
層級 | 事件的層級。 下列其中一個值：重大、錯誤、警告、資訊和詳細資訊
resourceProviderName | 受影響資源的資源提供者的名稱。
resourceType| 受影響資源的資源類型
子狀態 | 通常包含對應 REST 呼叫的 HTTP 狀態碼，但也可以包含其他描述子狀態的字串，常見的值包括：確定 (HTTP 狀態碼︰200)，已建立 (HTTP 狀態碼︰201)、接受 (HTTP 狀態碼︰202)、沒有內容 (HTTP 狀態碼︰204)、不正確的要求 (HTTP 狀態碼︰400)、找不到 (HTTP 狀態碼︰404)，衝突 (HTTP 狀態碼︰409)、內部伺服器錯誤 (HTTP 狀態碼︰500)、服務無法使用 (HTTP 狀態碼︰503)、閘道逾時 (HTTP 狀態碼︰504)。
eventTimestamp | 處理與事件對應之要求的Azure 服務產生事件時的時間戳記。
submissionTimestamp |   當事件變成可供查詢時的時間戳記。
subscriptionId | 記錄此事件的 Azure 訂用帳戶
status | 字串，描述作業的狀態。 常見的值包括︰Started、In Progress、Succeeded、Failed、Active、Resolved。
operationName | 作業名稱。
category | "ServiceHealth"
resourceId | 受影響資源的資源識別碼。
Properties.title | 此通訊的當地語系化標題。 預設語言為英文。
Properties.communication | 與 HTML 標記通訊的詳細資料 (已當地語系化)。 預設語言為英文。
Properties.incidentType | 可能的值：AssistedRecovery、ActionRequired、Information、Incident、Maintenance、Security
Properties.trackingId | 識別與此事件 (event) 相關聯的附帶事件 (Incident)。 可用此屬性讓與附帶事件 (Incident) 有關的事件 (event) 相關聯。
Properties.impactedServices | 逸出的 JSON blob，描述受到附帶事件 (Incident) 影響的服務和區域。 Services 清單 (每一份都有 ServiceName) 和 ImpactedRegions 清單 (每一份都有 RegionName)。
Properties.defaultLanguageTitle | 英文的通訊
Properties.defaultLanguageContent | 英文的通訊，如 html 標記或純文字
Properties.stage | AssistedRecovery、ActionRequired、Information、Incident、Security 的可能值：Active、Resolved。 Maintenance 的可能值︰Active、Planned、InProgress、Canceled、Rescheduled、Resolved、Complete
Properties.communicationId | 與此事件相關聯的通訊。


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 入口網站中檢視您的服務健康狀態通知
1.  在[入口網站](https://portal.azure.com)中，瀏覽至 [監視] 服務

    ![監視](./media/monitoring-service-notifications/home-monitor.png)
2.  按一下 [監視] 選項來開啟 [監視] 刀鋒視窗。 此刀鋒視窗會將您所有的監視設定和資料結合成一個彙總檢視。 它會先開啟 [活動記錄檔]  區段。

3.  此時按一下 [服務通知] 區段

    ![監視](./media/monitoring-service-notifications/service-health-summary.png)
4.  按一下任何明細項目以檢視其他詳細資訊

5. 按一下 [+ 加入活動記錄警示] 作業以接收通知，確保您會在未來收到此類型的服務通知。 若要深入了解服務通知上的警示設定，[請按一下這裡](monitoring-activity-log-alerts-on-service-notifications.md)

## <a name="next-steps"></a>後續步驟：
每當[發佈服務健康狀態通知時，即接收警示通知](monitoring-activity-log-alerts-on-service-notifications.md)  
深入了解[活動記錄警示](monitoring-activity-log-alerts.md)
