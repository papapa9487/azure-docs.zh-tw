---
title: "管理多部 Azure 虛擬機器的更新 | Microsoft Docs"
description: "本主題會說明如何管理 Azure 虛擬機器的更新。"
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
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: 80a6caff51631637825d560d270198be0336e806
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>管理多部 Azure 虛擬機器的更新

更新管理可讓您管理 Azure 虛擬機器的更新和修補程式。
從您的 [Azure 自動化](automation-offering-get-started.md)帳戶，您可以讓虛擬機器快速上線、評估可用更新的狀態、排定何時安裝必要的更新，以及檢閱部署結果來確認更新已成功套用至已啟用更新管理功能的所有虛擬機器。

## <a name="prerequisites"></a>必要條件

若要使用更新管理，您需要：

* Azure 自動化帳戶。 如需建立 Azure 自動化執行身分帳戶的指示，請參閱 [開始使用 Azure 自動化](automation-offering-get-started.md)。

* 已安裝其中一個支援作業系統的虛擬機器或電腦。

## <a name="supported-operating-systems"></a>受支援的作業系統

下列作業系統支援更新管理。

### <a name="windows"></a>Windows

* Windows Server 2008 及更新版本，以及依附 Windows Server 2008 R2 SP1 和更新版本的更新部署。  不支援「伺服器核心」和「Nano 伺服器」安裝選項。

    > [!NOTE]
    > 支援將更新部署到 Windows Server 2008 R2 SP1 需要 .NET Framework 4.5 和 WMF 5.0 或更新版本。
    > 
* 不支援 Windows 用戶端作業系統。

Windows 代理程式必須設定為可與 Windows Server Update Services (WSUS) 伺服器通訊，或必須能夠存取 Microsoft Update。

> [!NOTE]
> Windows 代理程式不可由 System Center Configuration Manager 並行管理。
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) 和 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) 和 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)  
* Ubuntu 12.04 LTS 和更新的 x86/x64   

> [!NOTE]  
> 若要避免在 Ubuntu 維護期間以外套用更新，請將自動安裝升級套件重新設定為停用自動更新。 如需設定方式的資訊，請參閱 [Ubuntu Server 指南中的自動更新主題](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

Linux 代理程式必須能夠存取更新存放庫。

> [!NOTE]
> 此解決方案不支援適用於 Linux 且設定為向多個 OMS 工作區報告的 OMS 代理程式。  
>

## <a name="enable-update-management-for-azure-virtual-machines"></a>為 Azure 虛擬機器啟用更新管理

1. 在 Azure 入口網站中，開啟自動化帳戶。
2. 在畫面左邊，選取 [更新管理]。
3. 在畫面頂端，按一下 [新增 Azure VM]。
    ![讓 VM 上線](./media/manage-update-multi/update-onboard-vm.png)
4. 選取要上線的虛擬機器。 [啟用更新管理] 畫面隨即顯示。
5. 按一下 [啟用] 。

   ![啟用更新管理](./media/manage-update-multi/update-enable.png)

隨即會為您的 Azure 虛擬機器啟用更新管理。

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>為非 Azure 虛擬機器和電腦啟用更新管理

如需有關如何為非 Azure Windows 虛擬機器和電腦啟用更新管理的指示，請參閱[將 Windows 電腦連線到 Azure 中的 Log Analytics 服務](../log-analytics/log-analytics-windows-agents.md)。

如需有關如何為非 Azure Linux 虛擬機器和電腦啟用更新管理的指示，請參閱[將您的 Linux 電腦連線到 Operations Management Suite (OMS)](../log-analytics/log-analytics-agent-linux.md)。

## <a name="view-update-assessment"></a>檢視更新評估

啟用 [更新管理] 之後，隨即會顯示 [更新管理] 畫面。 您可以在 [遺失更新] 索引標籤上看到遺失的更新清單。

## <a name="data-collection"></a>資料收集

虛擬機器和電腦上安裝的代理程式會收集關於更新的資料，並將其傳送至 Azure 更新管理。

### <a name="supported-agents"></a>支援的代理程式

下表描述此方案支援的連接來源。

| 連接的來源 | 支援 | 說明 |
| --- | --- | --- |
| Windows 代理程式 |是 |更新管理會從 Windows 代理程式收集系統更新的相關資訊，並起始必要更新的安裝。 |
| Linux 代理程式 |是 |更新管理會從 Linux 代理程式收集系統更新的相關資訊，並且在支援的散發套件上起始必要更新的安裝。 |
| Operations Manager 管理群組 |是 |更新管理會從所連線之管理群組中的代理程式收集系統更新的相關資訊。 |
| Azure 儲存體帳戶 |否 |Azure 儲存體未包含系統更新的相關資訊。 |

### <a name="collection-frequency"></a>收集頻率

對於每部受管理的 Windows 電腦，每天會掃描兩次。 系統會每隔 15 分鐘呼叫一次 Windows API 來查詢上次更新時間，以判斷狀態是否變更，若是如此，則會起始合規性掃描。  對於每部受管理的 Linux 電腦，每 3 個小時會掃描一次。

儀表板可能需要 30 分鐘以至 6 小時的時間，顯示來自受管理電腦的已更新資料。

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

如需記錄、輸出和錯誤訊息的詳細資訊，請參閱[更新管理](../operations-management-suite/oms-solution-update-management.md)。

## <a name="next-steps"></a>後續步驟

* 若要深入了解更新管理，請參閱[更新管理](../operations-management-suite/oms-solution-update-management.md)。