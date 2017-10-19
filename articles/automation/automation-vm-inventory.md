---
title: "使用清查收集來管理 Azure 虛擬機器 | Microsoft Docs"
description: "使用清查收集來管理虛擬機器"
services: automation
keywords: "清查、自動化、變更、追蹤"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.openlocfilehash: bfdd67ac1018f242e415da1a66d93f22bea8d054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>使用清查收集來管理 Azure 虛擬機器

您可以從虛擬機器的資源頁面啟用 Azure 虛擬機器的清查追蹤。 此方法提供以瀏覽器為基礎的使用者介面，讓您設定清查收集。

## <a name="before-you-begin"></a>開始之前
如果您沒有 Azure 訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。
如果您沒有 Azure 虛擬機器，請[建立虛擬機器](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>從虛擬機器資源頁面啟用清查收集

1. 在 Azure 入口網站的左側窗格中，選取 [虛擬機器]。
2. 在虛擬機器清單中，選取虛擬機器。
3. 在 [資源] 功能表上，於 [作業] 下選取 [清查 (預覽)]。  
    視窗頂端會出現橫幅，指出此解決方案未啟用。 
4. 若要啟用此方案，請選取橫幅。
5. 選取 Log Analytics 工作區來儲存資料記錄。  
    如果該區域沒有工作區可供您使用，系統會提示您建立預設工作區和自動化帳戶。 
6. 若要開始將您的電腦上架，請按一下 [啟用]。

   ![檢視上架選項](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    狀態列會通知正在啟用解決方案。 此程序可能需要 15 分鐘的時間。 在此期間，您可以關閉視窗，或可以保持開啟，以及它會在方案啟用時通知您。 您可以從通知窗格監視部署狀態。

   ![上架之後立即檢視清查解決方案](./media/automation-vm-inventory/inventory-onboarded.png)

部署完成時，狀態列就會消失。 系統仍在收集清查資料，可能還無法看到資料。 完整的資料收集可能需要 24 小時。

## <a name="configure-your-inventory-settings"></a>進行清查設定

根據預設會設定收集軟體、Windows 服務及 Linux 精靈。 若要收集 Windows 登錄和檔案清查，請進行清查收集設定。

1. 在 [清查 (預覽)] 檢視中，選取視窗頂端的 [編輯設定] 按鈕。
2. 若要新增收集設定，請透過選取 [Windows 登錄]、[Windows 檔案] 和 [Linux 檔案] 索引標籤，前往您想要新增的設定分類。 
3. 在視窗頂端選取 [新增]。
4. 若要檢視每個設定屬性的詳細資料和描述，請瀏覽[清查文件頁面](https://aka.ms/configinventorydocs)。

## <a name="disconnect-your-virtual-machine-from-management"></a>讓虛擬機器脫離管理

若要從清查管理中移除您虛擬機器：

1. 在 Azure 入口網站的左窗格中，選取 [Log Analytics]，然後選取您在將虛擬機器上架時使用的工作區。
2. 在 **Log Analytics** 視窗的 [資源] 功能表上，於 [工作區資料來源] 類別下選取 [虛擬機器]。 
3. 在清單中，選取您要中斷連線的虛擬機器。 虛擬機器在 [OMS 連線] 資料行中，**這個工作區**文字旁邊會出現綠色核取記號。 
4. 在下一個頁面的頂端，選取 [中斷連線]。
5. 在確認視窗中，選取 [是]。  
    這個動作會讓機器脫離管理。

## <a name="next-steps"></a>後續步驟

* 若要了解在虛擬機器上管理檔案和登錄設定的變更，請參閱[使用變更追蹤解決方案來追蹤環境中的軟體變更](../log-analytics/log-analytics-change-tracking.md)。
* 若要了解在虛擬機器上管理 Windows 和套件更新，請參閱 [OMS 中的更新管理方案](../operations-management-suite/oms-solution-update-management.md)。
