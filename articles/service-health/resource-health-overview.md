---
title: "Azure 資源健康狀態概觀 | Microsoft Docs"
description: "Azure 資源健康狀態的概觀"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 040d58a81a9b41fe660e4276d698bf884f90bb6c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-health-overview"></a>Azure 資源健康狀態概觀
 
當 Azure 問題影響到您的資源健康狀態時，協助進行診斷並取得支援。 它會通知您資源的目前及過去的健康狀態，並協助您解決問題。 資源健康狀態會在您需要解決 Azure 服務問題時提供技術支援。

[Azure 狀態](https://status.azure.com)會通知您影響大規模客戶的服務問題，而資源健康狀態會提供資源健康情況的個人化儀表板。 資源健康狀態會顯示您的資源過去因 Azure 服務問題而無法使用的所有時間。 這可讓您更輕鬆了解是否違反 SLA。 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>何謂資源，以及資源健康狀態如何決定資源健康與否？
資源是 Azure 服務透過 Azure Resource Manager 所提供之資源類型的執行個體，例如︰虛擬機器、Web 應用程式或 SQL Database。

資源健康狀態依賴不同的 Azure 服務所發出的訊號，來評估資源健康與否。 如果資源健康情況不良，資源健康狀態會分析其他資訊，來判斷問題的來源。 它也會識別 Microsoft 為修正這個問題所採取的動作，或您可以採取哪些動作來解決問題的原因。 

檢閱 [Azure 資源健康狀態](resource-health-checks-resource-types.md)中的資源類型及健康情況檢查完整清單，以了解健康情況評估方式的其他詳細資料。

## <a name="health-status-provided-by-resource-health"></a>資源健康狀態所提供的健全狀態
資源的健康情況為下列其中一個狀態︰

### <a name="available"></a>可用
服務偵測不到影響資源健康情況的任何事件。 如果資源在過去 24 小時內從未規劃的停機時間復原，您會看到**最近復原**通知。

![資源健康狀態可用的虛擬機器](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>無法使用
服務偵測到的進行中平台或非平台事件會影響資源的健康情況。

#### <a name="platform-events"></a>平台事件
這些事件會由 Azure 基礎結構的多個元件觸發，並包含排程的動作 (例如規劃的維護) 和非預期的事件 (例如未規劃的主機重新開機)。

資源健康狀態會提供關於事件、復原程序的其他詳細資料，並讓您即使在沒有有效的 Microsoft 支援合約時，也能連絡支援人員。

![由於平台事件而資源健康狀態無法使用的虛擬機器](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>非平台事件
這些事件會由使用者所採取的動作觸發，例如停止虛擬機器或觸達 Redis 快取的連線數目上限。

![由於非平台事件而資源健康狀態無法使用的虛擬機器](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>不明
此健全狀態表示資源健康狀態超過 10 分鐘未收到此資源的相關資訊。 雖然此狀態並非資源狀態的明確指示，卻是疑難排解程序中的重要資料點︰
* 如果資源如預期般執行，幾分鐘後資源的狀態會更新為「可用」。
* 如果您遇到資源的問題，不明的健全狀態可能會建議資源是受到平台的事件影響。

![資源健康狀態未知的虛擬機器](./media/resource-health-overview/Unknown.png)

## <a name="report-an-incorrect-status"></a>報告不正確的狀態
如果您在任何時間點認為目前的健全狀態不正確，您可以按一下 [報告不正確的健全狀態] 來告知我們。 在您受到 Azure 問題影響的情況下，建議您從 [資源健康狀態] 刀鋒視窗連絡支援人員。 

![資源健康狀態報告不正確的狀態](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>歷程記錄資訊
您可以按一下 [資源健康狀態] 刀鋒視窗中的 [檢視歷程記錄]，來存取多達 14 天的歷程記錄健康情況資料。 

![資源健康狀態報表歷程記錄](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>開始使用
若要將一個資源的資源健康狀態開啟
1.  登入 Azure 入口網站。
2.  瀏覽至您的資源。
3.  在位於左側的 [資源] 功能表，按一下 [資源健康狀態]。

![從 [資源] 刀鋒視窗將資源健康狀態開啟](./media/resource-health-overview/from-resource-blade.png)

您也可以存取資源健康狀態，方法為按一下 [更多服務]，然後在 [篩選] 文字方塊中輸入**資源健康狀態**，將 [說明 + 支援] 刀鋒視窗開啟。 最後按一下 [**資源健康狀態**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth) 。

![從 [更多服務] 將資源健康狀態開啟](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>後續步驟

若要深入了解資源健康狀態，請參閱下列資源：
-  [Azure 資源健康狀態中的資源類型和健康情況檢查](resource-health-checks-resource-types.md)
-  [關於 Azure 資源健康狀態的常見問題集](resource-health-faq.md)




