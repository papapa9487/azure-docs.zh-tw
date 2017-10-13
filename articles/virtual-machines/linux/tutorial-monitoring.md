---
title: "在 Azure 中監視和更新 Linux 虛擬機器 | Microsoft Docs"
description: "了解如何在 Azure 中監視 Linux 虛擬機器上的開機診斷和效能計量及管理套件更新"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 70c17d9a8f7bf6d9106efcb56eee7cd996460c18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>如何在 Azure 中監視和更新 Linux 虛擬機器

為了確保您的虛擬機器 (VM) 在 Azure 中正確執行，您可以檢閱開機診斷、效能計量及管理套件更新。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視主機計量
> * 啟用 VM 上的診斷擴充功能
> * 檢視 VM 計量
> * 依據診斷計量建立警示
> * 管理套件更新
> * 設定進階監視


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="create-vm"></a>建立 VM

若要查看作用中的診斷和計量，您需要 VM。 首先，使用 [az group create](/cli/azure/group#create) 建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroupMonitor 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

現在，使用 [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) 建立 VM。 下列範例會建立名為 myVM 的 VM。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>啟用開機診斷

當 Linux VM 開機，開機診斷擴充功能會擷取開機輸出，並儲存在 Azure 儲存體。 這項資料可以用於 VM 開機問題的疑難排解。 當您使用 Azure CLI 建立 Linux VM 時，開機診斷不會自動啟用。

啟用開機診斷之前，需要建立儲存體帳戶用來儲存開機記錄。 儲存體帳戶必須具有全域唯一的名稱，介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 使用 [az storage account create](/cli/azure/storage/account#create) 建立儲存體帳戶。 在此範例中，使用隨機字串來建立唯一的儲存體帳戶名稱。 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

當啟用開機診斷時，需要 blob 儲存體容器的 URI。 以下命令會查詢儲存體帳戶傳回此 URI。 URI 值儲存在名為 bloburi 的變數，下一個步驟會使用此變數。

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

現在，使用 [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable) 啟用開機診斷。 `--storage` 值是在上一個步驟收集到的 blob URI。

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>檢視開機診斷

開機診斷啟用後，每次您停止並啟動 VM 時，開機程序的相關資訊便會寫入記錄檔。 在此範例中，先使用 [az vm deallocate](/cli/azure/vm#deallocate) 命令將 VM 解除配置，如下所示：

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

現在，使用 [az vm start]( /cli/azure/vm#stop) 命令啟動 VM，如下所示：

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

您可以使用 [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) 命令取得 myVM 的開機診斷資料，如下所示︰

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>檢視主機計量

在 Azure 中有 Linux VM 專用的主機與它互動。 系統會自動收集主機的計量，而且可以在 Azure 入口網站中檢視這些計量，如下所示︰

1. 從 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroupMonitor]，然後選取資源清單中的 [myVM]。
1. 若要查看主機 VM 的執行狀況，按一下 VM 刀鋒視窗上的 [計量]，然後選取 [可用的計量] 下的任何 [主機] 計量。

    ![檢視主機計量](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>安裝診斷擴充功能

> [!IMPORTANT]
> 本文件說明的 Linux 診斷延伸模組 2.3 版已被取代。 至 2018 年 6 月 30 日止就不再支援 2.3 版。
>
> 屆時可以啟用 3.0 版的 Linux 診斷延伸模組。 如需詳細資訊，請參閱[文件](./diagnostic-extension.md)。

系統提供基本的主機計量，但若要查看更細微或 VM 特定的計量，則需要在 VM 上安裝 Azure 診斷的擴充功能。 Azure 診斷擴充功能可額外提供從 VM 擷取的監視和診斷資料。 您可以檢視這些效能計量，並依據 VM 的執行狀況建立警示。 診斷擴充功能可透過 Azure 入口網站安裝，如下所示︰

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
1. 按一下 [診斷設定]。 清單會顯示在上一節已啟用開機診斷。 按一下 [基本計量] 的核取方塊。
1. 在 [儲存體帳戶] 區段中，瀏覽並選取上一節建立的 mydiagdata[1234] 帳戶。
1. 按一下 [儲存]  按鈕。

    ![檢視診斷計量](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>檢視 VM 計量

您可以檢視 VM 計量，和您檢視主機 VM 計量資訊的方式相同︰

1. 在 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroup]，然後選取資源清單中的 [myVM]。
1. 若要查看 VM 的執行狀況，按一下 VM 刀鋒視窗上的 [計量]，然後選取 [可用的計量] 下的任何診斷計量。

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

## <a name="manage-package-updates"></a>管理套件更新

您可以使用更新管理來管理 Azure Linux VM 的套件更新和修補程式。 您可以直接從 VM 快速評估可用更新的狀態、排定何時安裝必要的更新，並檢閱部署結果來確認更新已成功套用至 VM。

如需價格資訊，請參閱[更新管理的自動化價格](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management-preview"></a>啟用更新管理 (預覽)

啟用 VM 的更新管理

1. 在畫面左邊，選取 [虛擬機器]。
1. 從清單中選取 VM。
1. 在 [VM] 畫面的 [作業] 區段中，按一下 [更新管理]。 [啟用更新管理] 畫面隨即開啟。

將會執行驗證來判斷此 VM 是否已啟用更新管理。 驗證包括檢查 Log Analytics 工作區及連結的自動化帳戶，以及解決方法是否在工作區中。

Log Analytics 工作區用來收集功能和服務 (例如更新管理) 所產生的資料。 工作區提供單一位置來檢閱和分析來自多個來源的資料。 若要在需要更新的 VM 上執行其他動作，Azure 自動化可讓您對 VM 執行指令碼，例如下載和套用更新。

驗證程序也會檢查 VM 是否以 Microsoft Monitoring Agent (MMA) 和混合式背景工作角色佈建。 此代理程式用來與 VM 通訊，並取得更新狀態的相關資訊。 

如果不符合這些必要條件，將會出現橫幅讓您選擇啟用此解決方案。

![更新管理上架設定橫幅](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

按一下橫幅以啟用解決方案。 如果在驗證之後遺漏下列任何必要條件，則會自動新增：

* [Log Analytics](../../log-analytics/log-analytics-overview.md) 工作區
* [自動化](../../automation/automation-offering-get-started.md)
* VM 上已啟用 [Hybrid Runbook 背景工作角色](../../automation/automation-hybrid-runbook-worker.md)

[啟用更新管理] 畫面隨即開啟。 進行設定，然後按一下 [啟用]。

![啟用更新管理解決方案](./media/tutorial-monitoring/manage-updates-update-enable.png)

啟用解決方案可能需要長達 15 分鐘，在這段時間，請勿關閉瀏覽器視窗。 啟用解決方案之後，有關 VM 上的封裝管理員遺漏更新的相關資訊會流向 Log Analytics。
可能需要 30 分鐘到 6 小時，資料才可供分析。

### <a name="view-update-assessment"></a>檢視更新評量

啟用 [更新管理] 解決方案之後，[更新管理] 畫面隨即出現。 您可以在 [遺漏更新] 索引標籤上看到遺漏的更新清單。

![檢視更新狀態](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>排程更新部署

若要安裝更新，請將部署排程在發行排程和維護時段之前。

按一下 [更新管理] 畫面頂端的 [排程更新部署]，以針對 VM 來排程新的更新部署。 在 [新增更新部署] 畫面上，指定下列資訊：

* **名稱** - 提供唯一名稱來識別更新部署。
* **要排除的更新** - 選取此選項，以輸入要排除更新的套件名稱。
* **排程設定** - 您可以接受預設的日期和時間 (目前時間之後的 30 分鐘)，或指定不同的時間。 您也可以指定部署是否為發生一次，或設定週期性排程。 按一下 [週期] 下的 [週期性] 選項，即可設定週期性排程。

  ![更新排程設定畫面](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **維護時間範圍 (分鐘)** - 指定您要執行更新部署的時段。  這有助於確保在您定義的維護時段內執行變更。 

排程設定完成之後，請按一下 [建立] 按鈕，並回到狀態儀表板。
請注意，[已排程] 表格會顯示您已建立的部署排程。

> [!WARNING]
> 如果維護時段內有足夠的時間，則安裝更新之後，VM 會自動重新啟動。

更新管理會使用 VM 上現有的封裝管理員來安裝套件。

### <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排定的部署開始之後，您就可以在 [更新管理] 畫面的 [更新部署] 索引標籤上看到該部署的狀態。
如果該部署目前正在執行，其狀態會顯示為 [進行中]。 完成時，如果成功，狀態會變更為 [成功]。
如果部署中的一或多個更新失敗，則狀態為 [失敗]。
按一下已完成的更新部署，以查看該更新部署的儀表板。

![特定部署的更新部署狀態儀表板](./media/tutorial-monitoring/manage-updates-view-results.png)

[更新結果] 磚包含 VM 上的更新總數和部署結果的摘要。
右邊表格是每個更新和安裝結果的詳細解析，可能是下列其中一個值：

* **未嘗試** - 未安裝更新，因為在已定義的維護時段內，沒有足夠的時間可用。
* **成功** - 更新已成功下載並安裝在 VM 上
* **失敗** - 更新無法下載或安裝在 VM 上。

按一下 [所有記錄] 以查看部署已建立的所有記錄項目。

按一下 [輸出] 磚，以查看負責在目標 VM 上管理更新部署之 Runbook 的作業串流。

按一下 [錯誤]，即可查看部署所傳回之任何錯誤的詳細資訊。

## <a name="advanced-monitoring"></a>進階監視 

您可以使用 [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) (OMS) 對您的 VM 進行更進階的監視。 如果您尚未這麼做，可以註冊 Operations Management Suite 的[免費試用版](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)。

當您可以存取 OMS 入口網站時，可以在 [設定] 刀鋒視窗中找到工作區金鑰和工作區識別碼。 將 <workspace-key> 和 <workspace-id> 取代為您的 OMS 工作區的值，然後您可以使用 **az vm extension set** 將OMS 擴充功能新增至 VM：

```azurecli-interactive 
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

在 OMS 入口網站的 [記錄搜尋] 刀鋒視窗中，您應該會看到myVM，類似下圖顯示的這樣︰

![OMS 刀鋒視窗](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定、檢閱和管理 VM 的更新。 您已了解如何︰

> [!div class="checklist"]
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視主機計量
> * 啟用 VM 上的診斷擴充功能
> * 檢視 VM 計量
> * 依據診斷計量建立警示
> * 管理套件更新
> * 設定進階監視

請前進到下一個教學課程，以了解 Azure 資訊安全中心。

> [!div class="nextstepaction"]
> [管理 VM 安全性](./tutorial-azure-security.md)
