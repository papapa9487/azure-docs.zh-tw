---
title: "在 Azure 虛擬機器中追蹤變更 | Microsoft Docs"
description: "使用「變更追蹤」來追蹤虛擬機器上檔案和登錄中的變更"
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ba92390b684573818ec9d69fd8c7be6d064316c7
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="track-changes-in-your-azure-virtual-machines"></a>在 Azure 虛擬機器中追蹤變更

透過啟用變更追蹤，您就可以追蹤虛擬機器上針對檔案和 Windows 登錄機碼所做的變更。 . 識別組態變更可協助您找出操作問題。

您可以直接從 Azure 虛擬機器啟用變更追蹤。

如果您沒有 Azure 虛擬機器，則可以使用 [Windows 快速入門](../virtual-machines/windows/quick-create-portal.md)或 [Linux 快速入門](../virtual-machines/linux/quick-create-portal.md)來建立一個

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>為 Azure 虛擬機器啟用變更追蹤

1. 在畫面左邊，選取 [虛擬機器]。
1. 從清單中選取虛擬機器。
1. 在虛擬機器畫面上，按一下 [作業] 區段中的 [變更追蹤]。 [啟用更新管理] 畫面隨即開啟。

   ![變更追蹤啟動 VM](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)

系統會執行驗證來判斷此虛擬機器是否已啟用變更追蹤。 如果變更追蹤並未啟用，則會顯示橫幅讓您選擇啟用此解決方案。

   ![變更追蹤啟動設定橫幅](./media/automation-vm-change-tracking/change-onboard-banner.png)

按一下橫幅以啟用解決方案。 如果您沒有下列項目，則系統會自動新增它們：

* [Log Analytics](../log-analytics/log-analytics-overview.md) 工作區
* [自動化](../automation/automation-offering-get-started.md)帳戶

選取 Log Analytics 工作區以儲存變更追蹤的資料記錄，並選取自動化帳戶以追蹤變更，然後按一下 [啟用]。

狀態列會通知您解決方案已啟用。 此程序可能需要 15 分鐘的時間。

## <a name="configure-change-tracking"></a>設定變更追蹤

啟用變更追蹤之後，[變更追蹤] 畫面隨即顯示。 按一下 [編輯設定] 來選擇要追蹤的檔案和登錄機碼。

![變更追蹤編輯設定](./media/automation-vm-change-tracking/change-edit-settings.png)

[工作區設定] 畫面隨即開啟。 在適當的索引標籤上按一下 [新增] 以新增要追蹤的 Windows 登錄機碼、Windows 檔案或 Linux 檔案。

## <a name="add-a-windows-registry-key"></a>新增 Windows 登錄機碼

1. 在 [工作區設定] 畫面的 [Windows 登錄] 索引標籤上，按一下 [新增]。 [為變更追蹤新增 Windows 登錄] 畫面隨即開啟。

   ![變更追蹤新增登錄](./media/automation-vm-change-tracking/change-add-registry.png)

1. 在 [已啟用] 底下，選取 [True]。
1. 在 [項目名稱] 欄位中新增易記名稱。
1. 在 [群組] 欄位中輸入群組名稱 (選擇性)。
1. 在 [Windows 登錄機碼] 欄位中，新增您要追蹤的登錄機碼名稱。
1. 按一下 [儲存] 。

## <a name="add-a-windows-file"></a>新增 Windows 檔案

1. 在 [工作區設定] 畫面的 [Windows 檔案] 索引標籤上，按一下 [新增]。[為變更追蹤新增 Windows 檔案] 畫面隨即開啟。

   ![變更追蹤新增 Windows 檔案](./media/automation-vm-change-tracking/change-add-win-file.png)

1. 在 [已啟用] 底下，選取 [True]。
1. 在 [項目名稱] 欄位中新增易記名稱。
1. 在 [群組] 欄位中輸入群組名稱 (選擇性)。
1. 在 [輸入路徑] 欄位中，新增您要追蹤之檔案的完整路徑和檔案名稱。
1. 按一下 [儲存] 。

## <a name="add-a-linux-file"></a>新增 Linux 檔案

1. 在 [工作區設定] 畫面的 [Linux 檔案] 索引標籤上，按一下 [新增]。[為變更追蹤新增 Linux 檔案] 畫面隨即開啟。

   ![變更追蹤新增 Linux 檔案](./media/automation-vm-change-tracking/change-add-linux-file.png)

1. 在 [已啟用] 底下，選取 [True]。
1. 在 [項目名稱] 欄位中新增易記名稱。
1. 在 [群組] 欄位中輸入群組名稱 (選擇性)。
1. 在 [輸入路徑] 欄位中，新增您要追蹤之檔案的完整路徑和檔案名稱。
1. 在 [路徑類型] 欄位中，選取 [檔案] 或 [目錄]。
1. 在 [遞迴] 底下，選取 [開啟] 以針對指定的路徑和位於該路徑下的所有檔案及路徑進行變更追蹤。 若要只追蹤選取的路徑或檔案，請選取 [關閉]。
1. 在 [Sudo] 底下，選取 [開啟] 以追蹤需要 `sudo` 命令以進行存取的檔案。 否則，請選取 [關閉]。
1. 按一下 [儲存] 。

## <a name="view-changes"></a>檢視變更

在 [變更追蹤] 畫面上，您可以看到虛擬機器於一段時間內在各種類別中的變更。

   ![變更追蹤新增檢視變更](./media/automation-vm-change-tracking/change-view-changes.png)

您可以選取要檢視的變更類別和時間範圍。 在畫面頂端，您會看到一段時間內變更的圖形化檢視。
在畫面底部，您會看到最近變更的清單。

## <a name="view-change-tracking-log-data"></a>檢視變更追蹤記錄資料

變更追蹤所產生的記錄資料會傳送到 Log Analytics。 若要透過執行查詢來搜尋記錄，請按一下 [變更追蹤] 畫面頂端的 [Log Analytics]。

   ![變更追蹤 Log Analytics](./media/automation-vm-change-tracking/change-log-analytics.png)

若要深入了解在 Log Analytics 中執行搜尋記錄檔，請參閱 [Log Analytics](../log-analytics/log-analytics-overview.md)。

## <a name="next-steps"></a>後續步驟

* 若要深入了解變更追蹤，請參閱[變更追蹤](../log-analytics/log-analytics-change-tracking.md)
* 若要深入了解管理虛擬機器的更新，請參閱[更新管理](../operations-management-suite/oms-solution-update-management.md)。

