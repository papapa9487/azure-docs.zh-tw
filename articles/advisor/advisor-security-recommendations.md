---
title: "Azure 建議程式安全性建議 | Microsoft Docs"
description: "使用 Azure 建議程式協助改善 Azure 部署的安全性。"
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 53be05593c3733ccb27979a3a026414013125779
ms.contentlocale: zh-tw
ms.lasthandoff: 03/31/2017

---
# <a name="advisor-security-recommendations"></a>建議程式安全性建議

Azure Advisor 可針對所有的 Azure 資源提供一致的合併建議檢視。 它與 Azure 資訊安全中心整合，可為您提供安全性建議。 您可以從 Advisor 儀表板上的 [安全性] 索引標籤取得安全性建議。

![Advisor [安全性] 按鈕](./media/advisor-security-recommendations/advisor-security-tab.png)

資訊安全中心利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。 它會定期分析 Azure 資源的安全性狀態。 當資訊安全中心識別潛在的安全性弱點時，它會建立建議。 這些建議會引導您完成設定所需控制項的程序。 

![Advisor [安全性] 索引標籤](./media/advisor-security-recommendations/advisor-security-recommendations.png)

如需安全性建議的詳細資訊，請參閱[管理 Azure 資訊安全中心的安全性建議](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)。

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>如何存取 Azure Advisor 中的安全性建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側窗格中，按一下 [更多服務]。

3. 在服務功能表窗格中，於 [監視和管理] 底下，按一下 [Azure Advisor]。  
 隨即會顯示 Advisor 儀表板。

4. 在 Advisor 儀表板上，按一下 [安全性] 索引標籤。

5. 選取您想要接收建議的訂用帳戶，然後按一下 [取得建議]。

> [!NOTE]
> 若要存取 Advisor 建議，您必須先向 Advisor「註冊您的訂用帳戶」。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，您能以訂用帳戶、資源群組或特定資源的 [擁有者]、[參與者] 或 [讀取者] 身分存取 Advisor 建議。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [建議程式簡介](advisor-overview.md)
* [開始使用建議程式](advisor-get-started.md)
* [建議程式成本建議](advisor-performance-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [建議程式高可用性建議](advisor-high-availability-recommendations.md)


 

