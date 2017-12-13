---
title: "將更新與變更追蹤解決方案上線至 Azure 自動化 | Microsoft Docs"
description: "了解如何將更新與變更追蹤解決方案上線至 Azure 自動化。"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>將更新與變更追蹤解決方案上線至 Azure 自動化

在本教學課程中，您會了解如何將適用於 VM 的更新、變更追蹤與清查解決方案上線至 Azure 自動化：

> [!div class="checklist"]
> * 手動將 Azure 虛擬機器上線。
> * 安裝及更新所需的 Azure 模組。
> * 匯入能將 Azure VM 上線的 Runbook。
> * 啟動會自動將 Azure VM 上線的 Runbook。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，需要有下列項目。
+ Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
+ [自動化帳戶](automation-offering-get-started.md)，以管理電腦。

## <a name="onboard-an-azure-virtual-machine-manually"></a>手動將 Azure 虛擬機器上線

1.  開啟自動化帳戶。
2.  按一下 [清查] 頁面。
![將清查解決方案上線](media/automation-onboard-solutions/inventory-onboard.png)
3.  選取現有的 Log Analytics 工作區，或建立新的工作區。 按一下 [啟用] 按鈕。
4.  當變更追蹤與清查解決方案上線通知完成時，按一下 [更新管理] 頁面。
![將更新解決方案上線](media/automation-onboard-solutions/update-onboard.png)
4.  按一下 [啟用] 按鈕，將更新解決方案上線。
5.  當更新解決方案上線通知完成時，按一下 [變更追蹤] 頁面。
![將變更追蹤上線](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  按一下 [新增 Azure VM] 按鈕。
![選取要進行變更追蹤的 VM](media/automation-onboard-solutions/enable-change-tracking.png)
7.  選取 Azure VM，然後按一下 [啟用] 按鈕以將變更追蹤與清查解決方案上線。
8.  當 VM 上線通知完成時，按一下 [更新管理] 頁面。
![將 VM 上線以進行更新管理](media/automation-onboard-solutions/update-onboard-vm.png)
9.  按一下 [新增 Azure VM] 按鈕。
![選取要進行更新管理的 VM](media/automation-onboard-solutions/enable-update.png)
10.  選取 Azure VM，然後按一下 [啟用] 按鈕以將更新管理解決方案上線。

## <a name="install-and-update-required-azure-modules"></a>安裝及更新所需的 Azure 模組

需要更新為最新的 Azure 模組並匯入 AzureRM.OperationalInsights，才能成功將解決方案上線自動化。
1.  按一下 [模組] 頁面。
![更新模組](media/automation-onboard-solutions/update-modules.png)
2.  按一下 [更新 Azure 模組] 按鈕以更新至最新版本。 等候更新完成。
3.  按一下 [瀏覽資源庫] 按鈕以開啟模組資源庫。
![匯入 OperationalInsights 模組](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  搜尋 AzureRM.OperationalInsights，並將此模組匯入至自動化帳戶。

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>匯入能將解決方案上線至 Azure VM 的 Runbook

1.  按一下 [程序自動化] 類別下的 [Runbook] 頁面。
2.  按一下 [瀏覽資源庫] 按鈕。
3.  搜尋「更新和變更追蹤」，然後將 Runbook 匯入至自動化帳戶。
![匯入上線 Runbook](media/automation-onboard-solutions/import-from-gallery.png)
4.  按一下 [編輯] 以檢視 Runbook 來源，然後按一下 [發佈] 按鈕。
![匯入上線 Runbook](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>啟動會自動將 Azure VM 上線的 Runbook

您必須已將變更追蹤或更新解決方案上線至 Azure VM，才能啟動這個 Runbook。 它需要目前具有已針對參數上線之解決方案的虛擬機器和資源群組。
1.  開啟 Enable-MultipleSolution Runbook。
![多個解決方案 Runbook](media/automation-onboard-solutions/runbook-overview.png)
2.  按一下 [開始] 按鈕，並針對參數輸入下列值。
    *   VMNAME： 要上線至更新或變更追蹤解決方案的現有 VM 名稱。 若保留空白，系統會將資源群組中的所有 VM 上線。
    *   VMRESOURCEGROUP： VM 所屬的資源群組名稱。
    *   SUBSCRIPTIONID： 要上線之新 VM 所在的訂用帳戶識別碼。 若保留空白，系統會使用工作區的訂用帳戶。 指定不同的訂用帳戶識別碼時，也應該將此自動化帳戶的 RunAs 帳戶新增為此訂用帳戶的參與者。
    *   ALREADYONBOARDEDVM： 已手動上架至更新或變更追蹤解決方案的 VM 名稱。
    *   ALREADYONBOARDEDVMRESOURCEGROUP： VM 所屬的資源群組名稱。
    *   SOLUTIONTYPE： 輸入 "Updates" 或 "ChangeTracking"
    
    ![多個解決方案 Runbook 參數](media/automation-onboard-solutions/runbook-parameters.png)
3.  按一下 [確定] 以啟動 Runbook 作業。
4.  在 Runbook 作業頁面上監視進度和任何錯誤。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[為 Runbook 安排排程](automation-schedules.md)。