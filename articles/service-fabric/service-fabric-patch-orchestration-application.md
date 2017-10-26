---
title: "Azure Service Fabric 修補程式協調流程應用程式 | Microsoft Docs"
description: "在 Service Fabric 叢集上將作業系統修補自動化的應用程式。"
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.openlocfilehash: aaceb556d926dbb09aeb2843a7941eadaaeb588b
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>修補 Service Fabric 叢集中的 Windows 作業系統

修補程式協調流程應用程式是 Azure Service Fabric 應用程式，可在 Service Fabric 叢集上將作業系統修補自動化，而不需要停機。

修補程式協調流程應用程式提供下列功能：

- **自動化的作業系統更新安裝**。 會自動下載並安裝作業系統更新。 會視需要重新啟動叢集節點，而不需要叢集停機。

- **叢集感知的修補和健康情況整合**。 套用更新時，修補程式協調流程應用程式會監視叢集節點的健康情況。 叢集節點一次只能升級一個節點，或一次升級一個網域。 如果因修補程序而造成叢集的健康情況下降，修補就會停止，防止問題繼續惡化。

## <a name="internal-details-of-the-app"></a>應用程式的內部詳細資料

修補程式協調流程應用程式包含下列子元件︰

- **協調器服務**︰是具狀態服務，負責：
    - 協調整個叢集上的 Windows Update 作業。
    - 儲存已完成之 Windows Update 作業的結果。
- **節點代理程式服務**︰是無狀態服務，在所有 Service Fabric 叢集節點上執行。 此服務負責：
    - 啟動節點代理程式 NTService。
    - 監視節點代理程式 NTService。
- **節點代理程式 NTService**：在較高層級權限 (系統) 執行的 Windows NT 服務。 相比之下，節點代理程式服務和協調器服務則是在較低層級權限 (網路服務) 執行。 此服務會負責執行下列所有叢集節點上的 Windows Update 作業：
    - 將節點上的自動 Windows Update 停用。
    - 根據使用者提供的原則下載並安裝 Windows Update。
    - 在 Windows Update 安裝後重新啟動機器。
    - 將 Windows Update 的結果上傳至協調器服務。
    - 萬一耗盡所有重試之後作業還是失敗，就報告健康情況報告。

> [!NOTE]
> 修補程式協調流程應用程式是使用 Service Fabric 的修復管理器系統服務，將節點停用或啟用以及執行健康情況檢查。 修補程式協調流程應用程式所建立的修復工作會追蹤每個節點的 Windows Update 進度。

## <a name="prerequisites"></a>必要條件

### <a name="minimum-supported-service-fabric-runtime-version"></a>支援的最低 Service Fabric 執行階段版本

#### <a name="azure-clusters"></a>Azure 叢集
修補程式協調流程應用程式必須在具有 Service Fabric 執行階段 5.5 版或更新版本的 Azure 叢集上執行。

#### <a name="standalone-on-premises-clusters"></a>獨立的內部部署叢集
修補程式協調流程應用程式必須在具有 Service Fabric 執行階段 5.6 版或更新版本的獨立叢集上執行。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>啟用修復管理器服務 (如果尚未執行中)

叢集必須啟用修復管理器系統服務，修補程式協調流程應用程式才能執行。

#### <a name="azure-clusters"></a>Azure 叢集

銀級耐久性層中的 Azure 叢集依預設會啟用修復管理器。 金級耐久性層中的 Azure 叢集可能會或也可能不會啟用修復管理器，取決於這些叢集的建立時間。 銅級耐久性層中的 Azure 叢集依預設不會啟用修復管理器服務。 如果已啟用服務，可以在 Service Fabric Explorer 的系統服務區段中看到它正在執行。

##### <a name="azure-portal"></a>Azure 入口網站
您可以在設定叢集時從 Azure 入口網站啟用修復管理員。 設定叢集時，選取 [附加元件功能] 底下的 [包含修復管理員] 選項。
![從 Azure 入口網站啟用修復管理員的映像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
或者，您可以使用 [Azure Resource Manager 範本](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)在新的和現有的 Service Fabric 叢集上啟用修復管理員。 取得您想要部署之叢集的範本。 您可以使用範例範本或建立自訂的 Resource Manager 範本。 

若要使用 [Azure Resource Manager 範本](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)啟用修復管理員服務：

1. 首先，檢查 `Microsoft.ServiceFabric/clusters` 資源的 `apiversion` 是否已設定為 `2017-07-01-preview`，如下列程式碼片段所示。 如果不是，您就需要將 `apiVersion` 更新為 `2017-07-01-preview` 值：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 立即啟用修復管理器服務，方法是在 `fabricSettings` 區段之後新增下列 `addonFeatures` 區段：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 一旦您使用這些變更將叢集範本進行更新之後，將它們加以套用，使升級完成。 您現在可以看到修復管理器系統服務在您的叢集中執行。 它在 Service Fabric Explorer 的系統服務區段中叫作 `fabric:/System/RepairManagerService`。 

### <a name="standalone-on-premises-clusters"></a>獨立的內部部署叢集

您可以使用[獨立 Windows 叢集的組態設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)在新的和現有的 Service Fabric 叢集上啟用修復管理器。

啟用修復管理器服務：

1. 首先，檢查[一般叢集設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations)中的 `apiversion` 是否已設定為 `04-2017` 或更高版本：

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. 立即啟用修復管理器服務，方法是在 `fabricSettings` 區段之後新增下列 `addonFeatures` 區段，如下所示：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 以這些變更更新您的叢集資訊清單，使用更新後的叢集資訊清單[建立新叢集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server)或[升級叢集設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration)。 叢集開始以更新的資訊清單執行後，您現在可以在 Service Fabric Explorer 的系統服務區段下，看到修復管理器系統服務 (亦稱為 `fabric:/System/RepairManagerService`) 在叢集中執行。

### <a name="disable-automatic-windows-update-on-all-nodes"></a>停用所有節點上的自動 Windows Update

自動 Windows Update 可能會導致可用性遺失，因為在相同時間重新啟動多個叢集節點。 依預設，修補程式協調流程應用程式會在每個叢集節點上嘗試將自動 Windows Update 停用。 不過，如果設定是由系統管理員或群組原則所管理，建議將 Windows Update 原則明確地設定為「下載前先通知」。

### <a name="optional-enable-azure-diagnostics"></a>選擇性：啟用 Azure 診斷

執行 Service Fabric 執行階段版本 `5.6.220.9494` 和以上版本的叢集，會收集修補程式協調流程應用程式記錄作為 Service Fabric 記錄的一部分。
如果您的叢集是在 Service Fabric 執行階段 `5.6.220.9494` 版和以上版本執行，您可以略過此步驟。

對於執行 Service Fabric 執行階段 `5.6.220.9494` 以下版本的叢集，只會在每個叢集節點的本機收集修補程式協調流程應用程式記錄。
我們建議您設定 Azure 診斷，將所有節點的記錄上傳到中央位置。

如需啟用 Azure 診斷的詳細資訊，請參閱[使用 Azure 診斷收集記錄](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad)。

產生的修補程式協調流程應用程式的記錄，具有下列的固定提供者識別碼：

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

在 Resource Manager 範本中，移至 `WadCfg` 下的 `EtwEventSourceProviderConfiguration` 區段，並新增下列項目：

```json
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  }
```

> [!NOTE]
> 如果 Service Fabric 叢集有多種節點類型，則必須在所有的 `WadCfg` 區段新增上述區段。

## <a name="download-the-app-package"></a>下載安裝套件

從[下載連結](https://go.microsoft.com/fwlink/P/?linkid=849590)下載應用程式。

## <a name="configure-the-app"></a>設定應用程式

您可以設定修補程式協調流程應用程式的行為以符合您的需求。 在應用程式建立或更新期間傳入應用程式參數，將預設值加以覆寫。 在 `Start-ServiceFabricApplicationUpgrade` 或 `New-ServiceFabricApplication` Cmdlet 中指定 `ApplicationParameter`，即可提供應用程式參數。

|**參數**        |**類型**                          | **詳細資料**|
|:-|-|-|
|MaxResultsToCache    |long                              | Windows Update 結果的最大數目，應加以快取。 <br>預設值為 3000，是基於以下假設： <br> - 節點數目 20。 <br> - 每個月在節點上發生的更新數目為 5。 <br> - 每個作業的結果數目可為 10。 <br> - 過去 3 個月的結果皆加以儲存。 |
|TaskApprovalPolicy   |例舉 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 會指出協調器服務在 Service Fabric 叢集節點中用來安裝 Windows 更新的原則。<br>                         允許的值包括： <br>                                                           <b>NodeWise</b>。 一次只會在一個節點上安裝 Windows Update。 <br>                                                           <b>UpgradeDomainWise</b>。 一次只會在一個升級網域上安裝 Windows Update。 (最多，屬於升級網域的所有節點都可以進行 Windows Update。)
|LogsDiskQuotaInMB   |long  <br> (預設值︰1024)               |修補程式協調流程應用程式記錄的大小上限 (以 MB 為單位)，可在節點上本機保留。
| WUQuery               | 字串<br>(預設值："IsInstalled=0")                | 用以取得 Windows 更新的查詢。 如需詳細資訊，請參閱 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
| InstallWindowsOSOnlyUpdates | Bool <br> (預設值︰True)                 | 這個旗標可讓您安裝 Windows 作業系統更新。            |
| WUOperationTimeOutInMinutes | int <br>(預設值︰90)                   | 指定任何 Windows Update 作業的逾時 (搜尋或下載或安裝)。 如果作業未在指定的逾時內完成，它就會中止。       |
| WURescheduleCount     | int <br> (預設值︰5)                  | 如果作業持續失敗，服務會將 Windows Update 重新排程的次數上限。          |
| WURescheduleTimeInMinutes | int <br>(預設值︰30) | 如果作業持續失敗，服務會將 Windows Update 重新排程的時間間隔。 |
| WUFrequency           | 以逗號分隔的字串 (預設值︰"Weekly, Wednesday, 7:00:00")     | 安裝 Windows Update 的頻率。 格式與可能的值如下： <br>-   Monthly, DD,HH:MM:SS，例如 Monthly, 5,12:22:32。 <br> -   Weekly, DAY,HH:MM:SS，例如 Weekly, Tuesday, 12:22:32。  <br> -   Daily, HH:MM:SS，例如 Daily, 12:22:32。  <br> -  None 表示不應該進行 Windows Update。  <br><br> 請注意，所有時間都是 UTC 格式。|
| AcceptWindowsUpdateEula | Bool <br>(預設值︰true) | 藉由設定這個旗標，應用程式會代表電腦的擁有者接受 Windows Update 的使用者授權合約 (EULA)。              |

> [!TIP]
> 如果需要 Windows Update 立即發生，請將 `WUFrequency` 設定為相對於應用程式部署的時間。 例如，假設您的測試叢集有五個節點，計劃於大約下午 5:00 UTC 部署應用程式。 如果您假設應用程式的升級或部署最多會花費 30 分鐘的時間，則將 WUFrequency 設定為 "Daily, 17:30:00"。

## <a name="deploy-the-app"></a>部署應用程式

1. 完成準備叢集的所有必要條件步驟。
2. 部署修補程式協調流程應用程式，和任何其他 Service Fabric 應用程式一樣。 您可以使用 PowerShell 部署此應用程式。 請遵循[使用 PowerShell 部署與移除應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)中的步驟。
3. 若要在部署時設定應用程式，可將 `ApplicationParamater` 傳遞給 `New-ServiceFabricApplication` Cmdlet。 為了您的方便，我們提供了 Deploy.ps1 指令碼以及我們的應用程式。 若要使用指令碼：

    - 使用 `Connect-ServiceFabricCluster` 連線至 Service Fabric 叢集。
    - 利用適當的 `ApplicationParameter` 值執行 Powershell 指令碼 Deploy.ps1。

> [!NOTE]
> 將指令碼和應用程式資料夾 PatchOrchestrationApplication 保存在同一個目錄中。

## <a name="upgrade-the-app"></a>升級應用程式

若要使用 PowerShell 將現有的修補程式協調流程應用程式進行升級，請遵循[使用 PowerShell 升級 Service Fabric應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)中的步驟。

## <a name="remove-the-app"></a>移除應用程式

若要移除應用程式，請遵循[使用 PowerShell 部署與移除應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)中的步驟。

為了您的方便，我們提供了 Undeploy.ps1 指令碼以及我們的應用程式。 若要使用指令碼：

  - 使用 ```Connect-ServiceFabricCluster``` 連線至 Service Fabric 叢集。

  - 執行 Powershell 指令碼 Undeploy.ps1。

> [!NOTE]
> 將指令碼和應用程式資料夾 PatchOrchestrationApplication 保存在同一個目錄中。

## <a name="view-the-windows-update-results"></a>檢視 Windows Update 結果

修補程式協調流程應用程式會公開 REST API，以向使用者顯示歷程記錄的結果。 JSON 結果的範例：
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

以下描述 JSON 的欄位。

欄位 | 值 | 詳細資料
-- | -- | --
OperationResult | 0 - 成功<br> 1 - 成功但發生錯誤<br> 2 - 失敗<br> 3 - 已中止<br> 4 - 中止但逾時 | 指出整體作業 (通常牽涉到安裝一或多個更新) 的結果。
ResultCode | 與 OperationResult 相同 | 此欄位指出個別更新安裝作業的結果。
OperationType | 1 - 安裝<br> 0 - 搜尋和下載。| 「安裝」是依預設會顯示在結果中的唯一一個 OperationType。
WindowsUpdateQuery | 預設值為 "IsInstalled=0" |用來搜尋更新的 Windows Update 查詢。 如需詳細資訊，請參閱 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
RebootRequired | true - 需要重新開機<br> false - 不需要重新開機 | 指出完成更新的安裝是否需要重新開機。

如果尚未排程更新，JSON 結果會是空的。

登入叢集，查詢 Windows Update 的結果。 接著，找出主要協調器服務的複本位址，然後點閱瀏覽器的 URL：http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults。

協調器服務的 REST 端點具有動態連接埠。 若要知道確切 URL，請查看 Service Fabric Explorer。 例如，可在 `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults` 找到結果。

![REST 端點的圖片](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


如果在叢集上啟用反向 Proxy，您也可以從叢集外部存取 URL。
需要點閱的端點為 http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults。

若要啟用叢集的反向 Proxy，請遵循 [Azure Service Fabric 中的反向 Proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) 中的步驟。 

> 
> [!WARNING]
> 設定反向 Proxy 之後，叢集中公開 HTTP 端點的所有微服務就能從叢集外部尋址。

## <a name="diagnosticshealth-events"></a>診斷/健康情況事件

### <a name="collect-patch-orchestration-app-logs"></a>收集修補程式協調流程應用程式記錄

收集修補程式協調流程應用程式的記錄，是執行階段版本 `5.6.220.9494` 及以上版本作為 Service Fabric 記錄的一部分。
至於執行 Service Fabric 執行階段 `5.6.220.9494` 以下版本的叢集，可使用下列方法收集記錄。

#### <a name="locally-on-each-node"></a>本機的每個節點上

如果 Service Fabric 執行階段版本小於 `5.6.220.9494`，則只會在每個 Service Fabric 叢集節點的本機收集記錄。 存取記錄的位置為 \[Service Fabric\_Installation\_Drive\]:\\PatchOrchestrationApplication\\logs。

例如︰如果 Service Fabric 是安裝在 D 磁碟機上，則路徑為 D:\\PatchOrchestrationApplication\\logs。

#### <a name="central-location"></a>中央位置

如果 Azure 診斷設為必要條件步驟的一部分，就可在 Azure 儲存體中使用修補程式協調流程應用程式的記錄。

### <a name="health-reports"></a>健康狀態報告

修補程式協調流程應用程式在下列情況下也會發佈協調器服務或節點代理程式服務的健康情況報告：

#### <a name="a-windows-update-operation-failed"></a>Windows Update 作業失敗

如果節點上的 Windows Update 作業失敗，就會產生節點代理程式服務的健康情況報告。 健康情況報告的詳細資料中包含有問題的節點名稱。

在有問題的節點上順利完成修補後，系統會自動清除報告。

#### <a name="the-node-agent-ntservice-is-down"></a>節點代理程式 NTService 關閉

如果節點上的節點代理程式 NTService 關閉，就會產生節點代理程式服務的警告等級健康情況報告。

#### <a name="the-repair-manager-service-is-not-enabled"></a>修復管理器服務未啟用

如果在叢集上找不到修復管理器服務，就會產生協調器服務的警告等級健康情況報告。

## <a name="frequently-asked-questions"></a>常見問題集

問： **當修補程式協調流程應用程式執行時，為什麼我看到叢集處於錯誤狀態？**

A. 在安裝程序期間，修補程式協調流程應用程式會停用或重新啟動節點，而這可能會導致叢集暫時關閉的健康情況。

根據應用程式的原則，可能是一個節點在修補作業期間關閉，「或是」整個升級網域同時關閉。

Windows Update 安裝結束時，在重新啟動前會重新啟用節點。

在下列範例中，因為兩個節點關閉，且違反 MaxPercentageUnhealthNodes 原則，叢集會暫時進入錯誤狀態。 錯誤是暫時性的，直到修補作業進行中為止。

![健康情況不良之叢集的圖片](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果問題持續發生，請參閱〈疑難排解〉一節。

問： **修補程式協調流程應用程式處於警告狀態**

A. 檢查針對應用程式所公佈的健康情況報告，是否為根本原因。 警告通常包含問題的詳細資料。 如果是暫時性的問題，應用程式預期會從這個狀態中自動復原。

問： **如果我的叢集健康情況不良，我可以做什麼？我需要採取緊急作業系統更新嗎？**

A. 當叢集健康情況不良時，修補程式協調流程應用程式就不會安裝更新。 請嘗試使叢集處於良好的健康情況，以便將修補程式協調流程應用程式工作流程解除封鎖。

問： **為什麼跨叢集修補的執行時間這麼久？**

A. 修補程式協調流程應用程式所花費的時間大部分是取決於下列因素︰

- 協調器服務的原則。 
  - 預設原則 `NodeWise` 的結果是一次只修補一個節點。 特別是在叢集較大的情況下，建議您使用 `UpgradeDomainWise` 原則以更快速修補多個叢集。
- 適用於下載和安裝的更新數目。 
- 下載並安裝更新時所需的平均時間，不應該超過幾個小時。
- VM 和網路頻寬的效能。

問： **為什麼看到 Windows Update 結果中的某些更新是透過 REST API 取得，而不是電腦上的 Windows Update 歷程記錄下？**

A. 某些產品更新需要簽入其各自的更新/修補歷程記錄。 例如，Windows Defender 更新不會顯示在 Windows Server 2016 上的 Windows Update 歷程記錄。

## <a name="disclaimers"></a>免責聲明

- 修補程式協調流程應用程式會代表使用者接受 Windows Update 的使用者授權合約 (EULA)。 可在應用程式的設定中選擇性地將此設定關閉。

- 修補程式協調流程應用程式會收集遙測來追蹤使用情形和效能。 應用程式的遙測會遵循 Service Fabric 執行階段遙測設定 (預設為開啟) 的設定。

## <a name="troubleshooting"></a>疑難排解

### <a name="a-node-is-not-coming-back-to-up-state"></a>節點不會回到開啟狀態

**節點可能會卡在停用中狀態，因為**：

安全性檢查擱置。 若要補救這種情況，請確定有足夠多健康情況良好的節點。

**節點可能會卡在已停用狀態，因為**：

- 已將節點手動停用。
- 因為 Azure 基礎結構作業正在進行中而導致節點停用。
- 修補程式協調流程應用程式已暫時將節點停用，以修補節點。

**節點可能會卡在關閉狀態，因為**：

- 已手動將節點置於關閉狀態。
- 節點正在重新啟動 (可能是由修補程式協調流程應用程式觸發)。
- 由於 VM 或機器故障或網路連線問題，使節點關閉。

### <a name="updates-were-skipped-on-some-nodes"></a>已略過某些節點上的更新

修補程式協調流程應用程式會根據重新排定的原則，嘗試安裝 Windows Update。 服務會根據應用程式原則，嘗試復原節點並略過更新。

在這種情況下，系統會針對節點代理程式服務產生警告等級的健康情況報告。 Windows Update 的結果也包含可能的失敗原因。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>開始安裝更新時，叢集的健康情況出現錯誤

錯誤的 Windows Update 會損及特定節點或升級網域上應用程式或叢集的健康情況。 修補程式協調流程應用程式會中止所有後續的 Windows Update作業，直到叢集恢復良好健康情況。

系統管理員必須介入，判斷應用程式或叢集為何因為 Windows Update 變成健康情況不良。

## <a name="release-notes"></a>版本資訊

### <a name="version-110"></a>1.1.0 版
- 公開版本

### <a name="version-111"></a>1.1.1 版
- 修正 SetupEntryPoint of NodeAgentService 中的錯誤，該錯誤導致無法安裝 NodeAgentNTService。

### <a name="version-120-latest"></a>1.2.0 版 (最新)

- 關於系統重新啟動工作流程的錯誤修正。
- 由於準備修復工作期間健康情況檢查未如預期般發生，而在 RM 工作建立時進行的錯誤修正。
- 將 Windows 服務 POANodeSvc 的啟動模式從自動變更為延遲自動。
