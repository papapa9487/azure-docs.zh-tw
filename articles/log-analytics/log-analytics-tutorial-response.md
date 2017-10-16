---
title: "使用 Azure Log Analytics 警示來回應事件 | Microsoft Docs"
description: "本教學課程可幫助您了解 Log Analytics 中的警示，以識別您的 OMS 儲存機制中的重要資訊，並可主動通知您相關問題或叫用動作以嘗試更正問題。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: magoedte
ms.openlocfilehash: ccf0bd57b7f54b1d8d57b460df9bb381b29ee381
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>使用 Log Analytics 警示來回應事件
Log Analytics 中的警示會識別您的 Log Analytics 儲存機制中的重要資訊。  它們是由自動定期執行記錄搜尋的警示規則所建立，如果記錄搜尋的結果符合特定準則，則會建立一個警示記錄，而它可以設定為執行自動化的回應。  本教學課程是[建立和共用 Log Analytics 資料的儀表板](log-analytics-tutorial-dashboards.md)教學課程的延續。   

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立警示規則
> * 設定警示規則以傳送電子郵件通知

若要完成本教學課程中的範例，您目前必須有[連線至 Log Analytics 工作區](log-analytics-quick-collect-azurevm.md)的虛擬機器。  

## <a name="log-in-to-azure-portal"></a>登入 Azure 入口網站
登入 Azure 入口網站，網址為 [https://portal.azure.com](https://portal.azure.com)。 

## <a name="create-alerts"></a>建立警示

警示會由自動定期執行記錄搜尋的警示規則所建立。  建立警示的依據，可以是特定的效能計量、特定事件建立時、缺少某個事件或特定的時間間隔內建立了數個事件。  例如，當平均 CPU 使用量超過某閾值，或特定的 Windows 服務或 Linux 精靈未執行而產生事件時，均可利用警示來通知您。   如果記錄搜尋的結果符合特定準則，則會建立警示的記錄。 此規則接著可以自動執行一或多個動作，以主動通知警示或叫用另一個處理序。 

在下列範例中，我們建立一個計量度量警示規則，此規則將為查詢中值超過 90% 閾值的每個電腦物件建立警示。

1. 在 Azure 入口網站中，按一下左下角的 [更多服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 **Log Analytics**。
2. 選取 OMS 入口網站，在 [概觀] 頁面上選取 [記錄搜尋] 以啟動 OMS 入口網站。  
3. 從入口網站最上方選取 [我的最愛]，然後在右側 [儲存的搜尋] 窗格中選取 [Azure VM - 處理器使用率] 查詢。  
4. 按一下頁面頂端的 [警示] 以開啟 [新增警示規則] 畫面。  
5. 使用下列資訊設定警示規則：  
   a. 提供警示的 [名稱]，例如「VM 處理器使用率超過 >90」  
   b. 針對 [時段]，指定查詢的時間範圍，例如 *30*。  查詢只會傳回在此目前時間範圍內建立的記錄。  
   c. [警示頻率] 指定應執行查詢的頻率。  針對此範例請指定 *5* 分鐘，這會在我們指定的時段內發生。  
   d. 選取 [計量度量]，並在 [彙總值] 中輸入 *90*，在 [觸發警示的依據]  中輸入 *3*。  
   e. 在 [動作] 下方停用電子郵件通知。
6. 按一下 [儲存]  完成警示規則。 該警示規則會立即開始執行。<br><br> ![警示規則範例](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

在 Log Analytics 中由警示規則建立的警示記錄，Type 為 **Alert**，SourceSystem 為 **OMS**。<br><br> ![產生的警示事件範例](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>警示動作
您可以對警示執行進階動作，例如建立電子郵件通知、啟動[自動化 Runbook](../automation/automation-runbook-types.md)、使用 Webhook 在 ITSM 事件管理系統中建立事件記錄，或以 [IT Service Management Connector 解決方案](log-analytics-itsmc-overview.md)作為符合警示準則時的回應。   

電子郵件動作會將內含警示詳細資料的電子郵件，傳送給一或多位收件者。 您可以指定郵件的主旨，但其內容是由 Log Analytics 所編製的標準格式。  我們來更新稍早建立的警示規則，並將它設定為以電子郵件通知，而不是以記錄搜尋主動監視警示記錄。     

1. 在 OMS 入口網站中，於上方功能表選取 [設定]，然後選取 [警示]。
2. 從警示規則清單中，按一下稍早建立之警示旁的鉛筆圖示。
3. 在 [動作] 區段下方，啟用電子郵件通知。
4. 提供電子郵件的 [主旨]，例如「處理器使用率超過閾值 >90」。
5. 在 [收件者] 欄位中新增一或多個電子郵件收件者的地址。  如果您指定多個地址，則使用分號 (;) 分隔這些地址。
6. 按一下 [儲存]  完成警示規則。 該警示規則會立即開始執行。<br><br> ![使用電子郵件通知的警示規則](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已學會警示規則在以排定的間隔執行記錄搜尋和比對特定準則時，會如何主動識別和回應問題。  

點選以下連結以查看預先建立的 Log Analytics 指令碼範例。  

> [!div class="nextstepaction"]
> [Log Analytics 指令碼範例](powershell-samples.md)