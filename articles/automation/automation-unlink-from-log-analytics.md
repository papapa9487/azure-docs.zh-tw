---
title: "從 Log Analytics 取消 Azure 自動化帳戶連結 | Microsoft Docs"
description: "此文章提供如何將 Azure 自動化帳戶從 OMS 工作區取消連結的概觀。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to-article
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 86af4eacf460e16d837643ec6f03d530450e56ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>如何將自動化帳戶從 Log Analytics 工作區取消連結

Azure 自動化與 Log Analytics 整合，不僅是為了支援監視所有自動化帳戶上的 Runbook 作業，也是您匯入下列相依於 Log Analytics 的解決方案時所必須的：

* [更新管理](../operations-management-suite/oms-solution-update-management.md)
* [變更追蹤](../log-analytics/log-analytics-change-tracking.md)
* [於下班時間啟動/停止 VM](automation-solution-vm-management.md)
 
若決定不想再讓自動化帳戶與 Log Analytics 整合，您可以直接從 Azure 入口網站將您的帳戶取消連結。  繼續之前，您必須先移除稍早所述的解決方案，否則無法進行此程序。  檢閱您已匯入之特定解決方案的主題，以了解移除解決方案所需的步驟。  

移除這些解決方案之後，您可以執行下列步驟以將您的自動化帳戶取消連結。

## <a name="unlink-workspace"></a>取消連結工作區

1. 從 Azure 入口網站開啟您的自動化帳戶，然後在 [自動化帳戶] 頁面上，在左側的 [相關資源] 區段下選取 [取消連結工作區]。<br><br> ![取消連結工作區選項](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)<br><br>  
2. 在 [取消連結工作區] 頁面上，按一下 [取消連結工作區]。<br><br> ![取消連結工作區頁面](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png)。<br><br>  您會收到提示，確認您想要繼續。<br><br>
3. 當 Azure 自動化嘗試將您的帳戶從 Log Analytics 工作區取消連結時，您可以從功能表在 [通知] 下追蹤進度。

若使用「更新管理」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 更新排程。  每個都會有符合您建立之更新部署的名稱)

* 針對解決方案建立的混合式背景工作角色群組。  每個都會具有如下名稱：machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)。

若使用「於下班時間啟動/停止 VM」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 啟動及停止 VM Runbook 排程 
* 啟動及停止 VM Runbook
* 變數   

## <a name="next-steps"></a>後續步驟

若要重新設定您的自動化帳戶以與 OMS Log Analytics 整合，請參閱[從自動化將作業狀態和作業串流轉送到 Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md)。 