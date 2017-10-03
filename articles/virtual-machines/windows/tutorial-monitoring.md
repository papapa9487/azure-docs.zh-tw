---
title: "Azure 監視和更新 Windows 虛擬機器 | Microsoft Docs"
description: "教學課程 - 使用 Azure PowerShell 監視和更新 Windows 虛擬機器"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: a37aed8b3321d3518ffd73e09f5bb21266a7e577
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>使用 Azure PowerShell 監視和更新 Windows 虛擬機器

Azure 監視器使用代理程式從 Azure VM 收集開機和效能資料，將此資料儲存在 Azure 儲存體，並讓資料可透過入口網站、Azure PowerShell 模組和 Azure CLI 存取。 更新管理可讓您管理 Azure Windows VM 的更新和修補程式。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視 VM 主機計量
> * 安裝診斷擴充功能
> * 檢視 VM 計量
> * 建立警示
> * 管理 Windows 更新
> * 設定進階監視

本教學課程需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

若要完成本教學課程中的範例，您目前必須具有虛擬機器。 如有需要，這個[指令碼範例](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)可以為您建立一部虛擬機器。 逐步完成教學課程之後，請視需要取代資源群組、VM 名稱、位置。

## <a name="view-boot-diagnostics"></a>檢視開機診斷

Windows 虛擬機器開機時，開機診斷代理程式會擷取可用於疑難排解的螢幕輸出。 此功能預設為啟用狀態。 擷取的螢幕畫面會儲存在 Azure 儲存體帳戶，這也是預設會建立的帳戶。 

您可以使用 [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) 命令取得開機診斷資料。 在下列範例中，開機診斷會下載到 *c:\* 磁碟機的根目錄。 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>檢視主機計量

在 Azure 中有 Windows VM 專用的主機 VM 與它互動。 系統會自動收集主機的計量，而且可以在 Azure 入口網站中檢視這些計量。

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
2. 按一下 VM 刀鋒視窗上的 [計量]，然後選取 [可用的計量] 下的任何 [主機] 計量以查看主機 VM 的執行狀況。

    ![檢視主機計量](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>安裝診斷擴充功能

系統提供基本的主機計量，但若要查看更細微或 VM 特定的計量，則需要在 VM 上安裝 Azure 診斷的擴充功能。 Azure 診斷擴充功能可額外提供從 VM 擷取的監視和診斷資料。 您可以檢視這些效能計量，並依據 VM 的執行狀況建立警示。 診斷擴充功能可透過 Azure 入口網站安裝，如下所示︰

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
2. 按一下 [診斷設定]。 清單會顯示在上一節已啟用開機診斷。 按一下 [基本計量] 的核取方塊。
3. 按一下 [啟用來賓層級監視] 按鈕。

    ![檢視診斷計量](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>檢視 VM 計量

您可以檢視 VM 計量，和您檢視主機 VM 計量資訊的方式相同︰

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
2. 若要查看 VM 的執行狀況，按一下 VM 刀鋒視窗上的 [計量]，然後選取 [可用的計量] 下的任何診斷計量。

    ![檢視 VM 計量](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>建立警示

您可以根據特定效能計量來建立警示。 舉例來說，可使用警示來通知您平均 CPU 使用量超過特定臨界值、或是可用磁碟空間降到低於某個量。 警示會顯示在 Azure 入口網站，或是可以透過電子郵件傳送。 您也可以觸發 Azure 自動化 Runbook 或 Azure Logic Apps 以回應產生的警示。

以下範例會建立平均 CPU 使用量的警示。

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
2. 按一下VM 刀鋒視窗中的 [警示規則]，按一下橫跨在警示刀鋒視窗上方的 [新增計量警示]。
4. 提供警示的 [名稱]，例如 myAlertRule。
5. 若要在 CPU 百分比連續五分鐘超過 1.0 時觸發警示，保留所有其他已選取的預設值。
6. (選擇性) 選取 [電子郵件的擁有者、參與者及讀者] 核取方塊以傳送電子郵件通知。 預設動作是在入口網站中顯示通知。
7. 按一下 [確定] 按鈕。

## <a name="manage-windows-updates"></a>管理 Windows 更新

更新管理可讓您管理 Azure Windows VM 的更新和修補程式。
您可以直接從 VM 快速評估可用更新的狀態、排定何時安裝必要的更新，並檢閱部署結果來確認更新已成功套用至 VM。

如需價格資訊，請參閱[更新管理的自動化價格](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>啟用更新管理

啟用 VM 的更新管理：
 
1. 在畫面左邊，選取 [虛擬機器]。
2. 從清單中選取 VM。
3. 在 [VM] 畫面的 [作業] 區段中，按一下 [更新管理]。 [啟用更新管理] 畫面隨即開啟。

將會執行驗證來判斷此 VM 是否已啟用更新管理。 驗證包括檢查 Log Analytics 工作區及連結的自動化帳戶，以及解決方法是否在工作區中。

Log Analytics 工作區用來收集功能和服務 (例如更新管理) 所產生的資料。 工作區提供單一位置來檢閱和分析來自多個來源的資料。 若要在需要更新的 VM 上執行其他動作，Azure 自動化可讓您對 VM 執行指令碼，例如下載和套用更新。

驗證程序也會檢查 VM 是否以 Microsoft Monitoring Agent (MMA) 和混合式背景工作角色佈建。 此代理程式用來與 VM 通訊，並取得更新狀態的相關資訊。 

如果不符合這些必要條件，將會出現橫幅讓您選擇啟用此解決方案。

![更新管理上架設定橫幅](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

按一下橫幅以啟用解決方案。 如果在驗證之後遺漏下列任何必要條件，則會自動新增：

* [Log Analytics](../../log-analytics/log-analytics-overview.md) 工作區
* [自動化](../../automation/automation-offering-get-started.md)
* VM 上已啟用 [Hybrid Runbook 背景工作角色](../../automation/automation-hybrid-runbook-worker.md)

[啟用更新管理] 畫面隨即開啟。 進行設定，然後按一下 [啟用]。

![啟用更新管理解決方案](./media/tutorial-monitoring/manageupdates-update-enable.png)

啟用解決方案可能需要長達 15 分鐘，在這段時間，請勿關閉瀏覽器視窗。 啟用解決方案之後，有關在 VM 上遺漏更新的相關資訊會流向 Log Analytics。
可能需要 30 分鐘到 6 小時，資料才可供分析。

### <a name="view-update-assessment"></a>檢視更新評量

啟用**更新管理**之後，[更新管理] 畫面隨即出現。 您可以在 [遺漏更新] 索引標籤上看到遺漏的更新清單。

 ![檢視更新狀態](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>排程更新部署

若要安裝更新，請將部署排程在發行排程和服務時段之前。
您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

按一下 [更新管理] 畫面頂端的 [排程更新部署]，以針對 VM 來排程新的更新部署。 在 [新增更新部署] 畫面上，指定下列資訊：

* **名稱** - 提供唯一名稱來識別更新部署。
* **更新分類** - 選取更新部署在部署中包含的軟體類型。 分類類型包括：
  * 重大更新
  * 安全性更新
  * 更新彙總套件
  * Feature Pack
  * Service Pack
  * 定義更新
  * 工具
  * 更新

* **排程設定** - 您可以接受預設的日期和時間 (目前時間之後的 30 分鐘)，或指定不同的時間。
  您也可以指定部署是否發生一次，或設定週期性排程。 按一下 [週期] 下的 [週期性] 選項，以設定週期性排程。

  ![更新排程設定畫面](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **維護時間範圍 (分鐘)** - 指定您要執行更新部署的時段。  這有助於確保在您定義的服務時段內執行變更。

排程設定完成之後，請按一下 [建立] 按鈕，並回到狀態儀表板。
請注意，[已排程] 表格會顯示您已建立的部署排程。

> [!WARNING]
> 如果更新需要重新開機，VM 會自動重新啟動。

### <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排程的部署開始之後，您可以在 [更新管理] 畫面的 [更新部署] 索引標籤上看到該部署的狀態。
如果目前正在執行，狀態會顯示為 [進行中]。 完成時，如果成功，狀態會變更為 [成功]。
如果部署中的一或多個更新失敗，則狀態為 [部分失敗]。
按一下已完成的更新部署，以查看該更新部署的儀表板。

   ![特定部署的更新部署狀態儀表板](./media/tutorial-monitoring/manageupdates-view-results.png)

[更新結果] 磚包含 VM 上的更新總數和部署結果的摘要。
右邊表格是每個更新和安裝結果的詳細解析，可能是下列其中一個值：

* **未嘗試** - 未安裝更新，因為在已定義的維護時段內，沒有足夠的時間可用。
* **成功** - 更新已順利完成
* **失敗** - 更新失敗

按一下 [所有記錄] 以查看部署已建立的所有記錄項目。

按一下 [輸出] 磚，以查看負責在目標 VM 上管理更新部署之 Runbook 的作業串流。

按一下 [錯誤]，以查看部署傳回之任何錯誤的詳細資訊。

## <a name="advanced-monitoring"></a>進階監視 

您可以使用 [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) (OMS) 對您的 VM 進行更進階的監視。 如果您尚未這麼做，可以註冊 Operations Management Suite 的[免費試用版](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)。

當您可以存取 OMS 入口網站時，可以在 [設定] 刀鋒視窗中找到工作區金鑰和工作區識別碼。 使用 [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) 命令將 OMS 擴充新增至 VM。 更新以下範例中的變數值，以反映您的 OMS 工作區金鑰和工作區識別碼。  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

幾分鐘後，您應該會在 OMS 工作區中看到新的 VM。 

![OMS 刀鋒視窗](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您利用 Azure 資訊安全中心設定並檢閱 VM。 您已了解如何︰

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立資源群組和 VM 
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視主機計量
> * 安裝診斷擴充功能
> * 檢視 VM 計量
> * 建立警示
> * 管理 Windows 更新
> * 設定進階監視

請前進到下一個教學課程，了解 Azure 資訊安全中心。

> [!div class="nextstepaction"]
> [管理 VM 安全性](./tutorial-azure-security.md)
