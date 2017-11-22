---
title: "設定安全性以存取和管理 Azure 時間序列深入解析 | Microsoft Docs"
description: "本文說明如何設定安全性和權限作為管理存取原則和資料存取原則，以保護 Azure 時間序列深入解析。"
services: time-series-insights
ms.service: time-series-insights
author: op-ravi
ms.author: omravi
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 22c8e4481f2ba4163a55cc1bbb6b33c10379a605
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>使用 Azure 入口網站授與 Time Series Insights 環境的資料存取權

Time Series Insights 環境有兩種獨立的存取原則︰

* 管理存取原則
* 資料存取原則

這兩種原則可將特定環境的各種權限授與給 Azure Active Directory 主體 (使用者和應用程式)。 主體 (使用者和應用程式) 必須屬於與含有此環境之訂用帳戶相關聯的 Active Directory (也稱為 Azure 租用戶)。

管理存取原則可授與環境設定相關權限，例如
*   建立和刪除環境、事件來源、參考資料集，以及
*   管理資料存取原則。

資料存取原則可授與下列權限：發出資料查詢、在環境中操作參考資料，以及共用與環境相關聯的已儲存查詢和檢視方塊。

這兩種原則能夠清楚區隔環境管理存取權與環境內資料存取權。 比方說，可以設定環境，以便移除環境擁有者/建立者的資料存取權。 此外，獲准讀取環境中資料的使用者與服務，可能無法存取環境的組態。

## <a name="grant-data-access"></a>授與資料存取
依照下列步驟來授與使用者主體的資料存取權︰

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 找到您的時間序列深入解析環境。 在**搜尋**方塊中輸入**時間序列深入解析**。 選取搜尋結果中的 [時間序列環境]。 

3. 從清單中選取 Time Series Insights 環境。
   
4. 選取 [資料存取原則]，然後選取 [+新增]。
  ![管時間序列深入解析來源 - 環境](media/data-access/getstarted-grant-data-access1.png)

5. 選取 [選取使用者]。  搜尋使用者名稱或電子郵件地址，以找出您想要新增的使用者。 按一下 [選取] 以確認選取項目。 

   ![管理 Time Series Insights 來源 - 新增](media/data-access/getstarted-grant-data-access2.png)

6. 選取 [選取角色]。 為使用者選擇適當的存取角色：
   - 如果您要允許使用者變更參考資料，並與其他環境使用者共用已儲存的查詢和觀點，請選取 [參與者]。 
   - 否則選取 [讀取者]，允許使用者查詢環境中的資料並且在環境中儲存個人 (非共用) 查詢。

   選取 [確定] 以確認角色選擇。

   ![管理 Time Series Insights 來源 - 選取使用者](media/data-access/getstarted-grant-data-access3.png)

8. 在 [選取使用者角色] 頁面中選取 [確定]。

   ![管理 Time Series Insights 來源 - 選取角色](media/data-access/getstarted-grant-data-access4.png)

9. [資料存取原則] 頁面會列出使用者和每個使用者的角色。

   ![管理 Time Series Insights 來源 - 結果](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>後續步驟
* 了解[如何將事件中樞的事件來源新增至 Azure 時間序列深入解析環境](time-series-insights-how-to-add-an-event-source-eventhub.md)。
* [將事件傳送](time-series-insights-send-events.md)到事件來源。
* 在[時間序列深入解析總管](https://insights.timeseries.azure.com)中檢視您的環境。
