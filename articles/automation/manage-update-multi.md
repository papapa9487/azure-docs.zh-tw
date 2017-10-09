---
title: "管理多部 Azure 虛擬機器的更新 | Microsoft Docs"
description: "讓 Azure 虛擬機器上線來管理更新。"
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>管理多部 Azure 虛擬機器的更新

更新管理可讓您管理 Azure 虛擬機器的更新和修補程式。
從您的 [Azure 自動化](automation-offering-get-started.md)帳戶，您可以讓虛擬機器快速上線、評估可用更新的狀態、排定何時安裝必要的更新，以及檢閱部署結果來確認更新已成功套用至已啟用更新管理功能的所有虛擬機器。

## <a name="prerequisites"></a>必要條件

若要完成本指南中的步驟，您將需要：

* Azure 自動化帳戶。 如需建立 Azure 自動化執行身分帳戶的指示，請參閱 [Azure 執行身分帳戶](automation-sec-configure-azure-runas-account.md)。
* Azure Resource Manager 虛擬機器 (非傳統)。 如需建立 VM 的指示，請參閱 [在 Azure 入口網站中建立第一個 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>為 Azure 虛擬機器啟用更新管理

1. 在 Azure 入口網站中，開啟自動化帳戶。
2. 在畫面左邊，選取 [更新管理]。
3. 在畫面頂端，按一下 [新增 Azure VM]。
    ![讓 VM 上線](./media/manage-update-multi/update-onboard-vm.png)
4. 選取要上線的虛擬機器。 [啟用更新管理] 畫面隨即顯示。
5. 按一下 [啟用]。

   ![啟用更新管理](./media/manage-update-multi/update-enable.png)

隨即會為您的 Azure 虛擬機器啟用更新管理。

## <a name="view-update-assessment"></a>檢視更新評估

啟用 [更新管理] 之後，隨即會顯示 [更新管理] 畫面。 您可以在 [遺失更新] 索引標籤上看到遺失的更新清單。

## <a name="schedule-an-update-deployment"></a>排定更新部署

若要安裝更新，請將部署排定在發行排程和服務時段之後。
您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，但排除更新彙總套件。

按一下 [更新管理] 畫面頂端的 [排程更新部署]，以針對一或多部虛擬機器排定新的「更新部署」。 在 [新增更新部署] 畫面中，指定下列資訊：

* **名稱** - 提供唯一名稱來識別更新部署。
* **OS 類型** - 選取 Windows 或 Linux。
* **要更新的電腦** - 選取您要更新的虛擬機器。

  ![選取要更新的虛擬機器](./media/manage-update-multi/update-select-computers.png)

* **更新分類** - 選取更新部署在部署中將包含的軟體類型。 分類類型包括：
  * 重大更新
  * 安全性更新
  * 更新彙總套件
  * Feature Pack
  * Service Pack
  * 定義更新
  * 工具
  * 更新
* **排程設定** - 您可以接受預設的日期和時間 (目前時間之後的 30 分鐘)，或指定不同的時間。
   您也可以指定部署是否為發生一次，或設定週期性排程。 按一下 [週期] 下的 [週期性] 選項，即可設定週期性排程。

   ![更新排程設定畫面](./media/manage-update-multi/update-set-schedule.png)

* **維護時間範圍 (分鐘)** - 指定您要執行更新部署的時段。  這有助於確保在您定義的服務時段內執行變更。

排程設定完成之後，請按一下 [建立] 按鈕，您就會返回狀態儀表板。
請注意，[已排定] 表格會顯示您剛才建立的部署排程。

> [!WARNING]
> 如果更新需要重新開機，虛擬機器將會自動重新啟動。

## <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排定的部署開始之後，您就可以在 [更新管理] 畫面的 [更新部署] 索引標籤上看到該部署的狀態。
如果該部署目前正在執行，其狀態會顯示為 [進行中]。 當它完成時，如果成功，狀態就會變更為 [成功]。
如果該部署中的一或多個更新失敗，則狀態會是 [部分失敗]。

![更新部署狀態 ](./media/manage-update-multi/update-view-results.png)

按一下已完成的更新部署，即可查看該更新部署的儀表板。

在 [更新結果] 圖格中，有虛擬機器上更新總數和部署結果的摘要。
在右邊的表格中，有每個更新的詳細明細及安裝結果，可能是下列其中一個值：

* 未嘗試 - 未安裝更新，因為根據所定義的維護時間範圍持續時間，可用的時間不足。
* 成功 - 更新成功
* 失敗 - 更新失敗

按一下 [所有記錄]，即可查看部署已建立的所有記錄項目。

按一下 [輸出] 圖格，即可查看負責管理目標虛擬機器上更新部署之 Runbook 的作業串流。

按一下 [錯誤]，即可查看部署所傳回之任何錯誤的詳細資訊。

## <a name="next-steps"></a>後續步驟

* 若要深入了解更新管理，請參閱[更新管理](../operations-management-suite/oms-solution-update-management.md)。
