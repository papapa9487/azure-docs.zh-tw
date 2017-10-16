---
title: "開始使用 Azure 建議程式 | Microsoft Docs"
description: "開始使用 Azure 建議程式。"
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-advisor"></a>開始使用 Azure 建議程式

了解如何透過 Azure 入口網站存取 Advisor、取得建議、實作建議、搜尋建議，以及重新整理建議。

## <a name="get-advisor-recommendations"></a>取得建議程式的建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側窗格中，按一下 [更多服務]。

3. 在服務功能表窗格中，於 [監視和管理] 底下，按一下 [Azure Advisor]。  
 隨即會顯示 Advisor 儀表板。

   ![使用 Azure 入口網站存取 Azure 建議程式](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. 在 Advisor 儀表板上，選取您想要接收建議的訂用帳戶。  
[建議程式] 儀表板會顯示所選訂用帳戶的個人化建議。 

5. 若要取得特定類別的建議，請按下列其中一個索引標籤：[高可用性]、[安全性]、[效能] 或 [成本]。
 
> [!NOTE]
> 若要存取 Advisor 建議，您必須先向 Advisor「註冊您的訂用帳戶」。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，您能以訂用帳戶、資源群組或特定資源的 [擁有者]、[參與者] 或 [讀取者] 身分存取 Advisor 建議。

  ![Azure 建議程式儀表板](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>取得 Advisor 的建議詳細資料，並實作解決方案

Advisor 中的 [建議] 刀鋒視窗會提供其他有關建議的資訊。 

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後啟動 [Azure Advisor](https://aka.ms/azureadvisordashboard)。

2. 在 [Advisor 建議] 儀表板上，按一下 [取得建議]。

3. 在建議清單中，按一下您想要詳細檢閱的建議。  
這會顯示 [建議] 刀鋒視窗。

4. 在 [建議] 刀鋒視窗上，檢閱您可執行以解決潛在問題或利用成本節約機會的動作相關資訊。 
  
  ![Advisor [建議] 刀鋒視窗](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>搜尋建議程式的建議

您可以搜尋特定訂用帳戶或資源群組的建議。 您也可以依照狀態搜尋建議。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後啟動 [Azure Advisor](https://aka.ms/azureadvisordashboard)。

2. 透過篩選訂用帳戶、資源群組和建議狀態 ([使用中] 或 [已延遲]) 來搜尋建議。

3. 若要顯示以您的搜尋篩選條件為基礎的 Advisor 建議清單，請按一下 [取得建議]。

  ![Advisor 搜尋篩選條件](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>延遲或關閉 Advisor 的建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後啟動 [Azure Advisor](https://aka.ms/azureadvisordashboard)。

2. 按一下 [取得建議]，然後按一下建議清單中的建議。

3. 在 [建議] 刀鋒視窗上，按一下 [延遲]。  

   ![建議程式建議動作範例](./media/advisor-get-started/advisor-snooze.png)

4. 指定延遲時間週期，或選取 [永不] 來關閉建議。


## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor，請參閱︰
* [Azure 建議程式簡介](advisor-overview.md)
* [建議程式高可用性建議](advisor-high-availability-recommendations.md)
* [建議程式安全性建議](advisor-security-recommendations.md)
-  [建議程式效能建議](advisor-performance-recommendations.md)
* [建議程式成本建議](advisor-performance-recommendations.md)
