---
title: "了解 Enterprise 適用之 Azure 保留執行個體的使用方式 | Microsoft Docs"
description: "了解如何讀取您的使用方式，以了解您 Enterprise 註冊之保留執行個體的應用程式。"
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
ms.openlocfilehash: 7ef601033b36ee968cb766d40a0a6b05afa9a1a4
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>了解 Enterprise 註冊之保留執行個體的使用方式
從[保留頁面](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade )使用 ReservationId，以及從 [EA入口網站](https://ea.azure.com) \(英文\) 使用使用方式檔案，了解保留執行個體的使用情況。 您也可以在 [EA 入口網站 ](https://ea.azure.com) \(英文\) 的使用情況摘要區段中查看保留使用情況。

>[!NOTE]
>如果在預付型方案計費內容中購買保留，請查看[了解您預付型方案訂用帳戶的保留執行個體使用情況](billing-understand-reserved-instance-usage.md)。

針對下一節，假設您在美國東部地區執行 Standard_D1_v2 Windows VM，且保留資訊看起來像下面的表格：

| 欄位 | 值 |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|數量 |1|
|SKU | 標準_D1|
|區域 | eastus |

## <a name="reservation-application"></a>保留應用程式

已涵蓋 VM 的硬體部分，因為部署的 VM 符合保留屬性。 若要查看保留的執行個體未涵蓋哪些 Windows 軟體，請前往 Azure 保留的 VM 執行個體軟體成本，前往 [Azure 保留 VM 執行個體 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md)。


### <a name="reservation-usage-in-csv"></a>保留使用情況 csv 檔案
您可以從 EA 入口網站下載 EA 使用情況 csv 檔案。 在下載的 csv 檔案中，篩選其他資訊並輸入您的保留 ID。 以下螢幕擷取畫面顯示和此保留相關的欄位：

![保留的執行個體的 EA csv 檔案](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. [其他資訊] 欄位中的 ReservationId 代表用來將權益套用至 VM 的保留。
2. ConsumptionMeter 是 VM 的 MeterId。
3. 這是成本為 $0 的 ReservationMeter，因為執行中 VM 的成本已經由保留支付。 
4. Standard_D1 是一種 vCPU VM，且是在沒有 Azure Hybrid Benefit 的情況下部署的 VM。 因此，這個計量涵蓋 Windows 軟體的額外費用。 請參閱 [Azure 保留 VM 執行個體 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md) 以尋找與 D 系列 1 核心 VM 相對應的計量。 如果使用 Azure Hybrid Benefit，將不會產生此額外費用。

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>EA 入口網站中使用情況摘要頁面中的保留使用情況

EA 入口網站的使用情況摘要區段 (![EA 使用情況摘要](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)) 中也會顯示保留執行個體使用情況

1. 您不需要針對 VM 的硬體元件付費，因為保留執行個體中已涵蓋該費用。 
2. 但您需要針對 Windows 軟體付費，因為沒有使用 Azure Hybrid Benefit。 

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。