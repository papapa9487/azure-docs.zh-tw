---
title: "了解預付型方案訂用帳戶的 Azure 保留執行個體使用量 |Microsoft Docs"
description: "了解如何讀取您的使用量，以了解預付型方案的保留執行個體應用"
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 6982d6177ef5c94436a28cd68beb9feb5cc343e5
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>了解預付型方案的保留執行個體使用量

從[保留頁面](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade )使用 ReservationId，以及從 [Azure 帳戶入口網站](https://account.azure.com) \(英文\) 的使用量檔案，了解保留執行個體的使用情況。


>[!NOTE]
>本文不適用於 EA 客戶。 如果您是 EA 客戶，請參閱[了解 Enterprise 註冊之保留執行個體的使用方式](billing-understand-reserved-instance-usage-ea.md) 本文也假設保留會套用至單一訂用帳戶。 如果此保留套用至多個訂用帳戶，保留的權益可能會延伸到多個使用量 CSV 檔案。 

針對下一節，假設您在美國東部地區執行 Standard_DS1_v2 Windows 虛擬機器，且保留資訊看起來像下面的表格：

| 欄位 | 值 |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|數量 |1|
|SKU | Standard_DS1_v2|
|區域 | eastus |

## <a name="reservation-application"></a>保留應用程式

已涵蓋虛擬機器的硬體部分，因為部署的虛擬機器符合保留屬性。 若要查看保留執行個體未涵蓋哪些 Windows 軟體，請前往 [Azure 保留執行個體的 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md)。

### <a name="statement-section-of-csv"></a>CSV 的說明區段
此 CSV 此區段會顯示保留的整體使用狀況。 將包含「Reservation-」的篩選套用至 [計量子類別]：![直接說明保留](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Reservation-Base VM 行有保留涵蓋的小時總數。 這一行為 $0.00，因為保留執行個體可以涵蓋它。 Reservation-Windows Svr (1 核心) 行涵蓋了 Windows 軟體成本。

### <a name="daily-usage-section-of-csv"></a>CSV 的每日使用量區段
篩選其他資訊並輸入您的保留識別碼。 以下螢幕擷取畫面顯示和此保留相關的欄位。 

![每日使用量費用](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. [其他資訊] 欄位中的 ReservationId 是用來將權益套用至虛擬機器的保留。
2. ConsumptionMeter 是虛擬機器的 MeterId。
3. Reservation-Base VM 計量子類別行在說明區段的行中代表 $0 成本。 執行此虛擬機器的成本是已由保留支付。
4. 這是保留的計量識別碼。 此計量的成本為 $0。 任何符合保留執行個體資格的虛擬機器在 CSV 中都有此 MeterId，用來說明成本。 
5. Standard_DS1_v2 是一種 vCPU 虛擬機器，且是在沒有 Azure Hybrid Benefit 的情況下部署的虛擬機器。 因此，這個計量涵蓋 Windows 軟體的額外費用。 請參閱 [Azure 保留執行個體的 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md)， 以尋找與 D 系列 1 核心 VM 相對應的計量。 如果使用 Azure Hybrid Benefit，就不會產生此額外費用。 

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。