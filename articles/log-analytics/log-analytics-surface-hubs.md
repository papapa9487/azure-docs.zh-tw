---
title: "使用 Azure Log Analytics 監視 Surface Hub | Microsoft Docs"
description: "使用 Surface Hub 解決方案來追蹤您的 Surface Hub 健康狀態，並了解其使用狀況。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6ecd0d09589fec85c1633f528afc1165c346b7f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>使用 Log Analytics 監視 Surface Hub 來追蹤其健全狀況

![Surface Hub 符號](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

本文說明如何使用 Log Analytics 中的 Surface Hub 解決方案來監視 Microsoft Operations Management Suite (OMS) 搭配使用的 Microsoft Surface Hub 裝置。 Log Analytics 可幫助您追蹤您的 Surface Hub 健康狀態，以及了解其使用狀況。

每個 Surface Hub 都已安裝 Microsoft Monitoring Agent。 透過此代理程式，您可以將資料從您的 Surface Hub 傳送至 OMS。 系統會從您的 Surface Hub 讀取記錄檔，然後傳送給 OMS 服務。 問題伺服器處於離線狀態、無法同步處理行事曆、裝置帳戶無法登入 Skype，諸如此類的問題會顯示在 OMS 的 Surface Hub 儀表板中。 使用儀表板中的資料，您可以識別未執行或發生其他問題的裝置，然後可能會套用修正程式來處理偵測到的問題。

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案
請使用下列資訊來安裝和設定方案。 為了管理從 Microsoft Operations Management Suite (OMS) 管理您的 Surface Hub，您將需要下列各項︰

* [OMS](http://www.microsoft.com/oms) 的有效訂用帳戶。
* 可支援您要監視之裝置數目的 [OMS 訂用帳戶](https://azure.microsoft.com/pricing/details/log-analytics/)層級。 OMS 的價格取決於已註冊多少裝置裝置以及要處理多少資料。 規劃使用 Surface Hub 時請將這一點列入考慮。

接下來，您要將 OMS 訂用帳戶新增至現有的 Microsoft Azure 訂用帳戶，或直接透過 OMS 入口網站建立新的工作區。 使用哪一種方法的詳細指示請參閱[開始使用 Log Analytics](log-analytics-get-started.md)。 OMS 訂用帳戶設定完畢後，有兩種方式可以註冊您的 Surface Hub 裝置︰

* 自動透過 Intune 註冊
* 在 Surface Hub 裝置上手動透過 [設定] 註冊

## <a name="set-up-monitoring"></a>設定監視
您可以使用 OMS 中的 Log Analytics 監視 Surface Hub 的健康狀態和活動。 您可以在 OMS 中使用 Intune 註冊 Surface Hub，或使用 Surface Hub 上的 [設定] 在本機註冊。

## <a name="connect-surface-hubs-to-oms-through-intune"></a>透過 Intune 將 Surface Hub 連接至 OMS
您會需要即將管理 Surface Hub 的 OMS 工作區的工作區識別碼和工作區金鑰。 您可以從 OMS 入口網站取得這些資訊。

Intune 是一項 Microsoft 產品，可讓您集中管理套用到一或多個裝置的 OMS 組態設定。 請依照下列步驟透過 Intune 設定您的裝置：

1. 登入 Intune。
2. 瀏覽至 [設定]  >  [連接的來源]。
3. 以 Surface Hub 範本為基礎建立或編輯原則。
4. 瀏覽至原則的 OMS (Azure Operational Insights) 區段，將 [工作區識別碼] 和 [工作區金鑰] 新增至原則。
5. 儲存原則。
6. 將原則與適當的裝置群組關聯。

   ![Intune 原則](./media/log-analytics-surface-hubs/intune.png)

Intune 接著將 OMS 設定與目標群組中的裝置同步處理，將它們註冊到 OMS 工作區。

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>使用設定應用程式將 Surface Hub 連接至 OMS
您會需要即將管理 Surface Hub 的 OMS 工作區的工作區識別碼和工作區金鑰。 您可以從 OMS 入口網站取得這些資訊。

如果您不使用 Intune 來管理您的環境，可以透過每個 Surface Hub 上的 [設定] 以手動方式註冊裝置︰

1. 從您的 Surface Hub 開啟 [設定]。
2. 出現提示時，輸入裝置的系統管理員認證。
3. 按一下 [這個裝置]，再按一下 [監視] 下的 [設定 OMS 設定]。
4. 選取 [啟用監視]。
5. 在 [OMS 設定] 對話方塊中，輸入**工作區識別碼**和**工作區金鑰**。  
   ![設定](./media/log-analytics-surface-hubs/settings.png)
6. 按一下 [確定] 完成設定。

會出現告訴您 OMS 設定已成功套用至裝置的確認訊息。 接下來會出現訊息指出代理程式已成功連接至 OMS 服務。 裝置接著會開始將資料傳送至 OMS，您可以在其中檢視並處理它。

## <a name="monitor-surface-hubs"></a>監視 Surface Hub
使用 OMS 監視 Surface Hub 和監視任何已註冊的裝置極為類似。

1. 登入 OMS 入口網站。
2. 瀏覽至 Surface Hub 解決方案組件的儀表板。
3. 會顯示裝置的健康狀態。

   ![Surface Hub 的儀表板](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

您可以根據現有或自訂的記錄檔搜尋來建立[警示](log-analytics-alerts.md)。 使用 OMS 從 existing or custom log searches 收集來的資料，您可以依您為裝置定義的條件搜尋問題和警示。

## <a name="next-steps"></a>後續步驟
* 使用 [Log Analytics 中的記錄檔搜尋](log-analytics-log-searches.md)來檢視詳細的 VMware 資料。
* 建立[警示](log-analytics-alerts.md)在 Surface Hub 發生問題時通知您。
