---
title: "Azure Time Series Insights 中的資料存取原則 | Microsoft Docs"
description: "在本教學課程中，您會了解如何在 Time Series Insights 中管理資料存取原則"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.openlocfilehash: 6a0f04d79ac5487a347e28445c1a6677d5b8b16a
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2017
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>使用 Azure 入口網站授與 Time Series Insights 環境的資料存取權

Time Series Insights 環境有兩種獨立的存取原則︰

* 管理存取原則
* 資料存取原則

這兩種原則可將特定環境的各種權限授與給 Azure Active Directory 主體 (使用者和應用程式)。 主體 (使用者和應用程式) 必須屬於與含有此環境之訂用帳戶相關聯的 Active Directory (或「Azure 租用戶」)。

管理存取原則可授與環境設定相關權限，例如
*   建立和刪除環境、事件來源、參考資料集，以及
*   管理資料存取原則。

資料存取原則可授與下列權限：發出資料查詢、在環境中操作參考資料，以及共用與環境相關聯的已儲存查詢和檢視方塊。

這兩種原則能夠清楚區隔環境管理存取權與環境內資料存取權。 比方說，可以設定環境，以便移除環境擁有者/建立者的資料存取權。 獲准讀取環境中資料的使用者與服務，可能無法存取環境的組態。

## <a name="grant-data-access"></a>授與資料存取
下列步驟示範如何授與使用者主體的資料存取權︰

1.  登入 [Azure 入口網站](https://portal.azure.com)。
2.  在搜尋視窗中輸入「Time Series」。
3.  按一下 [Time Series Environment]
4.  從清單中選取 Time Series Insights 環境。

  ![管理 Time Series Insights 來源 - 環境](media/data-access/getstarted-grant-data-access1.png)

4.  選取 [資料存取原則]，然後按一下 [新增]

  ![管理 Time Series Insights 來源 - 新增](media/data-access/getstarted-grant-data-access2.png)

5.  按一下 [選取使用者]。
6.  依照電子郵件搜尋並選取使用者。
7.  在 [選取使用者] 刀鋒視窗中按一下 [選取]。

  ![管理 Time Series Insights 來源 - 選取使用者](media/data-access/getstarted-grant-data-access3.png)

8.  按一下 [選取角色]。
9.  如果您要允許使用者變更參考資料，並與其他環境使用者共用已儲存的查詢和觀點，請選取 [參與者]。 否則選取 [讀取者]，允許使用者查詢環境中的資料並且在環境中儲存個人 (非共用) 查詢。
10. 在 [選取角色] 刀鋒視窗中按一下 [確定]。

  ![管理 Time Series Insights 來源 - 選取角色](media/data-access/getstarted-grant-data-access4.png)

11. 在 [選取使用者角色] 刀鋒視窗中按一下 [確定]。
12. 您應該會看到：

  ![管理 Time Series Insights 來源 - 結果](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>後續步驟

* [建立事件來源](time-series-insights-add-event-source.md)
* [將事件傳送](time-series-insights-send-events.md)到事件來源
* 在 [Time Series Insights 入口網站](https://insights.timeseries.azure.com)中檢視您的環境
