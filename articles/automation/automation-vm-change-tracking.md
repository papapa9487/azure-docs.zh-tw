---
title: "在 Azure 虛擬機器中追蹤變更 | Microsoft Docs"
description: "使用變更追蹤來追蹤虛擬機器上檔案和登錄中的變更。"
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
ms.openlocfilehash: 5c6e8390ec8533fc7ab281c212e47a6982b30f1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>在 Azure 虛擬機器中追蹤變更

透過啟用變更追蹤，您就可以追蹤虛擬機器上針對檔案和 Windows 登錄機碼所做的變更。 識別組態變更可協助您找出操作問題。

您可以直接從 Azure 虛擬機器啟用變更追蹤。

如果您沒有 Azure 虛擬機器，您可以依照 [Windows 快速入門](../virtual-machines/windows/quick-create-portal.md)或 [Linux 快速入門](../virtual-machines/linux/quick-create-portal.md)文章中的指示建立。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>為 Azure 虛擬機器啟用變更追蹤

1. 在 Azure 入口網站的左側窗格中，選取 [虛擬機器]。
2. 在清單中選取虛擬機器。
3. 在虛擬機器視窗中，在 [作業] 下選取 [變更追蹤]。 

   ![變更追蹤上架 VM](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    [啟用更新管理] 視窗隨即開啟。

    系統會執行驗證來判斷此虛擬機器是否已啟用變更追蹤。 如果變更追蹤並未啟用，則會顯示橫幅讓您選擇啟用此解決方案。

   ![變更追蹤啟動設定橫幅](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. 若要啟用此解決方案，請選取橫幅。 如果您沒有下列項目，則系統會自動新增它們：

   * [Log Analytics](../log-analytics/log-analytics-overview.md) 工作區
   * [自動化](../automation/automation-offering-get-started.md)帳戶

5. 選取 Log Analytics 工作區以儲存變更追蹤的資料記錄，並選取自動化帳戶以追蹤變更，然後選取 [啟用]。  
    狀態列會通知您解決方案已啟用。 此程序可能需要 15 分鐘的時間。

## <a name="configure-change-tracking"></a>設定變更追蹤

啟用變更追蹤之後，[變更追蹤] 視窗隨即顯示。 

若要選擇想要追蹤的檔案和登錄機碼，請選取 [編輯設定]。

   ![變更追蹤編輯設定](./media/automation-vm-change-tracking/change-edit-settings.png)

   [工作區設定] 視窗隨即開啟。 

在 [工作區設定] 視窗中，新增要追蹤的 Windows 登錄機碼、Windows 檔案或 Linux 檔案，如接下來三節所述。

### <a name="add-a-windows-registry-key"></a>新增 Windows 登錄機碼

1. 在 [Windows 登錄] 索引標籤上，選取 [新增]。  
    [為變更追蹤新增 Windows 登錄] 視窗隨即開啟。

   ![變更追蹤新增登錄](./media/automation-vm-change-tracking/change-add-registry.png)

2. 在 [已啟用] 底下，選取 [True]。
3. 在 [項目名稱] 方塊中新增易記名稱。
4. (選擇性) 在 [群組] 方塊中輸入群組名稱。
5. 在 [Windows 登錄機碼] 方塊中，新增您要追蹤的登錄機碼名稱。
6. 選取 [ **儲存**]。

### <a name="add-a-windows-file"></a>新增 Windows 檔案

1. 在 [Windows 檔案] 索引標籤上，選取 [新增]。  
    [為變更追蹤新增 Windows 檔案] 視窗隨即開啟。

   ![變更追蹤新增 Windows 檔案](./media/automation-vm-change-tracking/change-add-win-file.png)

2. 在 [已啟用] 底下，選取 [True]。
3. 在 [項目名稱] 方塊中新增易記名稱。
4. (選擇性) 在 [群組] 方塊中輸入群組名稱。
5. 在 [輸入路徑] 方塊中，新增您要追蹤之檔案的完整路徑和檔案名稱。
6. 選取 [ **儲存**]。

### <a name="add-a-linux-file"></a>新增 Linux 檔案

1. 在 [Linux 檔案] 索引標籤上，選取 [新增]。  
    [為變更追蹤新增 Linux 檔案] 視窗隨即開啟。

   ![變更追蹤新增 Linux 檔案](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. 在 [已啟用] 底下，選取 [True]。
3. 在 [項目名稱] 方塊中新增易記名稱。
4. (選擇性) 在 [群組] 方塊中輸入群組名稱。
5. 在 [輸入路徑] 方塊中，新增您要追蹤之檔案的完整路徑和檔案名稱。
6. 在 [路徑類型] 方塊中，選取 [檔案] 或 [目錄]。
7. 若要追蹤指定的路徑及其之下所有檔案和路徑的變更，請在 [遞迴] 底下選取 [開啟]。 若要只追蹤選取的路徑或檔案，請選取 [關閉]。
8. 若要追蹤的檔案需要 `sudo` **命令才能存取，請在 [使用 Sudo] 底下選取 [開啟]**。 否則，請選取 [關閉]。
9. 選取 [ **儲存**]。

## <a name="view-changes"></a>檢視變更

在 [變更追蹤] 視窗中，您可以檢視虛擬機器於一段時間內在各種類別中的變更。

   ![變更追蹤新增檢視變更](./media/automation-vm-change-tracking/change-view-changes.png)

您可以選取要檢視的變更類別和時間範圍。 在視窗頂端，您可以檢視一段時間變更的圖形表示法。 在畫面底部，您可以檢視最近變更的清單。

## <a name="view-change-tracking-log-data"></a>檢視變更追蹤記錄資料

變更追蹤所產生的記錄資料會傳送到 Log Analytics。 若要透過執行查詢來搜尋記錄，請選取 [變更追蹤] 視窗頂端的 [Log Analytics]。

   ![變更追蹤 Log Analytics](./media/automation-vm-change-tracking/change-log-analytics.png)

若要深入了解在 Log Analytics 中執行和搜尋記錄檔，請參閱 [Log Analytics](../log-analytics/log-analytics-overview.md)。

## <a name="next-steps"></a>後續步驟

* 若要深入了解變更追蹤，請參閱[使用變更追蹤解決方案來追蹤環境中的軟體變更](../log-analytics/log-analytics-change-tracking.md)。
* 若要深入了解管理虛擬機器的更新，請參閱 [OMS 中的更新管理解決方案](../operations-management-suite/oms-solution-update-management.md)。
