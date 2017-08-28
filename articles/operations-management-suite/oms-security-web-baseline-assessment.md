---
title: "Operations Management Suite 安全性和稽核解決方案基準中的 Web 基準評估 | Microsoft Docs"
description: "本文件說明如何針對合規性和安全性目的，在 OMS 安全性和稽核解決方案中使用 Web 基準評估，以執行所有受監視 Web 伺服器的基準評估。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.contentlocale: zh-tw
ms.lasthandoff: 08/19/2017

---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite 安全性和稽核解決方案中的 Web 基準評估
本文件協助您使用 OMS 安全性和稽核的 Web 基準評估功能，存取受監視資源的安全狀態。

## <a name="what-is-web-baseline-assessment"></a>什麼是 Web 基準評估？
OMS 安全性目前會提供作業系統的安全性基準評估。 它會每隔 24 小時掃描您伺服器的 OS 設定，並可讓您檢視可能有弱點的設定。 如需詳細資訊，請參閱 [Operations Management Suite 安全性和稽核解決方案中的基準評估](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline)。

Web 基準評估的目的是尋找可能有弱點的 Web 伺服器設定。 Web 基準組態的三個主要來源︰.NET、ASP.NET 和 IIS 組態。  如同作業系統基準評估，OMS 安全性將會每隔 24 小時掃描您的 Web 伺服器，並可讓您檢視其安全性狀態。  在 Internet Information Service (IIS) 中，可以高度自訂組態，以便覆寫各種網站和應用程式層級。 除了預設的根層級以外，掃描程式會檢查每個應用程式/網站層級的設定。 這可協助您找出可能遭受攻擊的設定並快速修復，以及我們對這些設定的建議。

>[!NOTE] 
>您可以在此[頁面](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0)中下載 OMS 安全性所用的常見組態識別碼和基準規則。


## <a name="web-security-baseline-assessment"></a>Web 安全性基準評估

透過 OMS 搜尋選項，以及 OMS 安全性和稽核儀表板即可存取此預覽功能。 請遵循下列步驟來執行適當的查詢︰

1. 在 [Microsoft Operations Management Suite] 主儀表板中，按一下 [安全性和稽核] 圖格。
2. 在 [安全性和稽核] 儀表板中，您可以查看 OS 基準檢視方塊旁邊的 Web 基準檢視方塊。
   
    ![OMS 安全性和稽核 Web 安全幸基準評估](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. 左窗格顯示相較於基準的 Web 伺服器數目、所有已評估伺服器上通過的平均規則百分比，以及已評估的伺服器清單。
4. 右窗格會列出未通過 Severity 和 RuleType 的唯一規則。 按一下任何右窗格規則，將會顯示該規則的詳細資料。 下圖顯示範例。 已評估的規則會列在 [規則設定] 之下。 AzId 欄位是 Microsoft 用於追蹤基準規則之每個規則的唯一識別碼。 使用者除了可以看到「預期結果」(Microsoft 的建議值) 以外，還可看到其他有關規則安全性影響的詳細資料。
    
    ![查詢](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. 您可以建立自己的查詢以檢閱結果。 

您可以使用的第一個查詢是 **Web 基準評估摘要**。 在 [從這裡開始搜尋] 欄位中，輸入此查詢︰*Type=SecurityBaselineSummary BaselineType=Web*。 輸出範例如下︰

![查詢結果](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>在此查詢中，每筆記錄都會指出在單一伺服器上的評估摘要。

當您在 [記錄搜尋] 中，您可以輸入不同的查詢，以取得有關 Web 基準評估的詳細資訊。 除了上述查詢，您也可以在此預覽版本中使用下列查詢：

**Web 基準規則評估**︰每筆記錄均代表單一伺服器上的單一 Web 基準規則評估。 其中包含未通過規則的所有資料、評估規則所在的 SitePath、「預期結果」和「實際結果」。

查詢：*Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![查詢結果 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**顯示特定伺服器的所有結果**︰此查詢會顯示如何查看特定伺服器的結果：查詢：*Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![查詢結果 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

您可以使用這些記錄/查詢來建立自己的儀表板、報告或警示。 以下是您可新增至儀表板的範例 UI 控制項。 您可以了解如何使用 [這裡](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/)的 OMS 檢視表設計工具將您的資料視覺化。 下列畫面是您進行此自訂後，圖格的外觀範例。

![儀表板](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>另請參閱
在本文件中，您已了解 OMS 安全性和稽核 Web 基準評估。 若要深入了解 OMS 安全性，請參閱下列文章：

* [Operations Management Suite (OMS) 概觀](operations-management-suite-overview.md)
* [在 Operations Management Suite 安全性和稽核內監視及回應安全性警示](oms-security-responding-alerts.md)
* [在 Operations Management Suite 安全性和稽核解決方案內監視資源](oms-security-monitoring-resources.md)


