---
title: "Azure IoT 中樞高可用性和災害復原 | Microsoft Docs"
description: "描述 Azure 和 IoT 中樞功能，協助您建立具有災害復原功能的高可用性 Azure IoT 解決方案。"
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: ae320e58-aa20-45b9-abdc-fa4faae8e6dd
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: b3ca2ed90dd14350d3962a558aaac41f2e007bbd
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT 中樞高可用性和災害復原
作為 Azure 服務，IoT 中心提供高可用性 (HA)，在 Azure 區域層級使用備援，且解決方案不需任何額外的工作。 Microsoft Azure 平台也包含各種功能，以協助您建置具有災害復原 (DR) 功能或跨區域可用性的解決方案。 若要提供全域、跨區域的高可用性給裝置或使用者，請設計及準備解決方案以善用這些 Azure DR 功能。 [Azure 業務持續性技術指引](../resiliency/resiliency-technical-guidance.md) 一文描述業務持續性和 DR 的 Azure 內建功能。 [Azure 應用程式的災害復原與高可用性][Disaster recovery and high availability for Azure applications]一文針對 Azure 應用程式的策略提供架構指引以達到 HA 和 DR。

## <a name="azure-iot-hub-dr"></a>Azure IoT 中樞 DR
除了內部區域 HA，IoT 中樞會實作不需要使用者介入的災害復原容錯移轉機制。 IoT 中樞 DR 會自行起動，其復原時間目標 (RTO) 為 2 到 26 小時，以及下列復原點目標 (RPO)。

| 功能 | RPO |
| --- | --- |
| 登錄和通訊作業的服務可用性 |可能的 CName 遺失 |
| 身分識別登錄中的身分識別資料 |0 到 5 分鐘的資料遺失 |
| 裝置到雲端的訊息 |所有未讀取的訊息都會遺失 |
| 作業監視訊息 |所有未讀取的訊息都會遺失 |
| 雲端到裝置的訊息 |0 到 5 分鐘的資料遺失 |
| 雲端到裝置的意見反應佇列 |所有未讀取的訊息都會遺失 |

## <a name="regional-failover-with-iot-hub"></a>使用 IoT 中心的區域容錯移轉
IoT 解決方案中部署拓撲的完整處理方式不在本文討論範圍內。 本文討論為了實現高可用性和災害復原的目的，所建立的*區域容錯移轉*部署模型。

在區域容錯移轉模型中，解決方案後端主要是在某個資料中心位置執行，而次要 IoT 中樞和後端則會部署在另一個資料中心位置。 如果主要資料中心的 IoT 中樞遭遇服務中斷或從裝置到主要資料中心的網路連線中斷，每當無法觸達主要閘道時，裝置就會使用次要服務端點。 使用跨區域容錯移轉功能時，可改善解決方案的可用性，勝過單一區域的高可用性。

在較高層級，為了使用 IoT 中樞實作區域容錯移轉模型，您需要下列內容：

* **次要 IoT 中樞和裝置路由邏輯**：萬一主要區域的服務中斷，裝置必須開始與您的次要地區連接。 由於大部分服務狀態感知的本質，解決方案的系統管理員通常會觸發區域間的容錯移轉程序。 要讓新端點與裝置通訊，同時保有程序的控制權，最佳方式是讓它們定期檢查「指引」服務是否有目前作用中的端點。 這裡的指引服務可以是 Web 應用程式，其可藉由使用 DNS 重新導向技術複寫並保持連接 (例如使用 [Azure 流量管理員][Azure Traffic Manager])。
* **身分識別登錄複寫** - 為了成為可用狀態，次要 IoT 中樞必須包含能夠連接到解決方案的所有裝置身分識別。 解決方案應該保留裝置身分識別的異地複寫備份，並在切換裝置的作用中端點之前將其上傳至次要 IoT 中樞。 IoT 中樞的裝置身分識別匯出功能在此內容中很有用。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南 - 身分識別登錄][IoT Hub developer guide - identity registry]。
* **合併邏輯** - 當主要區域再次可供使用時，所有在次要站台中建立的狀態和資料都必須移轉回主要區域。 此狀態和資料大多與裝置身分識別和應用程式中繼資料相關，必須與主要 IoT 中樞合併，也可能要與主要區域中的其他應用程式專屬存放區合併。 若要簡化此步驟，您應該使用等冪作業。 等冪作業可以將副作用降到最低，不只包括來自最終一致的事件分佈的副作用，也包括來自事件的重複項目或失序傳遞的副作用。 此外，應用程式邏輯應該設計為能夠容忍潛在的不一致或「稍微」過期的狀態。 發生此狀況可能是因為系統需要額外的時間來根據復原點目標 (RPO)「療癒」。

## <a name="next-steps"></a>後續步驟
遵循下列連結以深入了解 Azure IoT 中樞：

* [開始使用 IoT 中心 (教學課程)][lnk-get-started]
* [何謂 Azure IoT 中樞？][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

