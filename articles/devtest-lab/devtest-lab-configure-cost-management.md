---
title: "檢視 Azure DevTest Labs 中的每月估計實驗室成本趨勢 | Microsoft Docs"
description: "了解 Azure DevTest Labs 每月估計成本趨勢圖表。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: b3ad1ead522908d4b41b7cca98d20ac91664998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>檢視 Azure DevTest Labs 中的每月估計實驗室成本趨勢
研測實驗室的成本管理功能可協助您追蹤實驗室成本。 本文會示範如何使用「每月估計成本趨勢」  圖表，來檢視目前日曆月份的到目前為止的估計成本，以及目前日曆月份的預計月底成本。 在本文中，您將學習如何在 Azure 入口網站中檢視每月估計成本趨勢圖表。

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>檢視每月估計成本趨勢圖表
若要檢視「每月估計成本趨勢」圖表，請遵循下列步驟︰ 

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。   
4. 在實驗室的刀鋒視窗上，選取 [成本設定] 。
5. 在實驗室的 [成本設定] 刀鋒視窗中，選取 [實驗室成本趨勢]。
6. 下列螢幕擷取畫面顯示成本圖表範例。 
   
    ![成本圖表](./media/devtest-lab-configure-cost-management/graph.png)

**估計成本** 值是到目前行事曆月份為止累計的預估成本。 **預計成本** 是目前行事曆月份整個月的估計成本，是以前五天的實驗室成本來計算。

成本金額會無條件進位到下一個整數。 例如： 

* 5.01 會無條件進位到 6 
* 5.50 會無條件進位到 6
* 5.99 會無條件進位到 6

如圖表上方所述，您在圖表中看到的成本是使用[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)優惠費率的*估計*成本。
此外，以下「未」  併入成本計算︰

* 目前不支援 CSP 和 Dreamspark 訂用帳戶，因為 Azure DevTest Labs 使用 [Azure 計費 API](../billing/billing-usage-rate-card-overview.md) 來計算實驗室成本，而 Azure 計費 API 並不支援 CSP 或 Dreamspark 訂用帳戶。
* 您的優惠費率。 目前，我們無法使用您與 Microsoft 或 Microsoft 合作夥伴協議的優惠費率 (顯示於您的訂用帳戶下方)。 我們將使用隨用隨付費率。
* 您的稅率
* 您的折扣
* 您的帳單貨幣。 目前，實驗室成本只會以美元貨幣顯示。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關部落格文章
* [讓 DevTest Labs 成本不失控的另外兩項須知](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [為何要設定成本臨界值？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>後續步驟
以下是接下來要嘗試的一些事項：

* [定義實驗室原則](devtest-lab-set-lab-policy.md) - 了解如何設定用來管理您實驗室及其 VM 使用方式的各種原則。 
* [建立自訂映像](devtest-lab-create-template.md) - 當您建立 VM 時，您要指定一個基本映像，它可以是自訂映像或 Marketplace 映像。 本文會示範如何從 VHD 檔案建立自訂的映像。
* [設定 Marketplace 映像](devtest-lab-configure-marketplace-images.md) - DevTest Labs 支援根據 Azure Marketplace 映像建立 VM。 本文會示範在實驗室中建立 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。
* [在實驗室中建立 VM](devtest-lab-add-vm-with-artifacts.md) - 示範如何從基本映像 (自訂或 Marketplace) 建立 VM，以及如何使用 VM 中的構件。

