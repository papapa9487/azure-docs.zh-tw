---
title: "使用清查收集來管理 Azure VM | Microsoft Docs"
description: "使用清查集合來管理 VM"
services: automation
keywords: "清查、自動化、變更、追蹤"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>使用清查收集來管理 Azure 虛擬機器

您可以從機器的資源頁面啟用 Azure 虛擬機器的清查追蹤。 此方法提供以瀏覽器為基礎的使用者介面，讓您設定清查收集。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。
如果您沒有 Azure 虛擬機器，在開始之前，請先建立[虛擬機器](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>從虛擬機器資源頁面啟用清查收集

1. 在左邊畫面中，選取 [虛擬機器]。
1. 從清單中選取虛擬機器。
1. 從 [作業] 下方的資源功能表中，選取 [清查 (預覽)]。
1. 頁面頂端會出現橫幅，指出此解決方案未啟用。 按一下橫幅以啟用解決方案。
1. 選取 Log Analytics 工作區來儲存資料記錄。 如果該區域沒有工作區可供您使用，系統會提示您建立預設工作區和自動化帳戶。 按一下 [啟用]，開始將您的電腦上架。

   ![檢視上架選項](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. 狀態列會通知正在啟用解決方案。 此程序可能需要 15 分鐘的時間。 在此期間，您可以關閉刀鋒視窗，或保持開啟，啟用解決方案後，此刀鋒視窗會報告。 您可以從通知窗格監視部署狀態。

   ![上架之後立即檢視清查解決方案](./media/automation-vm-inventory/inventory-onboarded.png)

1. 部署完成時，狀態列就會消失。 此時，系統仍在收集清查資料，可能還無法看到資料。 完整的資料收集可能需要 24 小時。

## <a name="configure-your-inventory-settings"></a>進行清查設定

根據預設會設定收集軟體、Windows 服務及 Linux 精靈。 若要收集 Windows 登錄和檔案清查，請進行清查收集設定。

1. 從 [清查 (預覽)] 檢視中，選取頁面頂端的 [編輯設定]按鈕。
2. 若要新增收集設定，請使用 [Windows 登錄]、[Windows 檔案] 和 [Linux 檔案] 索引標籤，以瀏覽至您想要新增的設定分類。 按一下頁面頂端的 [新增]。
3. 若要檢視每個設定屬性的詳細資料和描述，請瀏覽[清查文件頁面](https://aka.ms/configinventorydocs)。

## <a name="disconnecting-your-virtual-machine-from-management"></a>讓虛擬機器脫離管理

若要從清查管理中移除您的電腦：

1. 從 Azure 入口網站的左側功能表，按一下 [Log Analytics]，然後選取您在虛擬機器上架時使用的工作區。
1. 在 Log Analytics 頁面上，從資源功能表選取 [工作區資料來源] 類別下的 [虛擬機器]。 
1. 從清單中選取您要中斷連線的虛擬機器。 在 [OMS 連線] 資料行中，「這個工作區」文字旁邊會出現綠色核取記號。 在下一頁的頂端按一下 [中斷連線]，在確認對話方塊中按一下[是]，讓機器脫離管理。

## <a name="next-steps"></a>後續步驟

* 若要了解在虛擬機器上管理檔案和登錄設定的變更，請參閱[變更追蹤](../log-analytics/log-analytics-change-tracking.md)。
* 若要了解在虛擬機器上管理 Windows 和套件更新，請參閱[更新管理](../operations-management-suite/oms-solution-update-management.md)。

