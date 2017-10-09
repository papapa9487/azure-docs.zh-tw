---
title: "Azure Stack 中的診斷功能 | Microsoft Docs"
description: "如何在 Azure Stack 中收集記錄檔以進行診斷"
services: azure-stack
documentationcenter: 
author: adshar
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: adshar
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: d8f39d921222a3642e3da6e288b4ca11ae1ccaeb
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack 診斷工具

適用於：Azure Stack 整合系統和 Azure Stack 開發套件
 
Azure Stack 是元件共同作業並與彼此互動的大型集合。 所有這些元件都將會產生自己的唯一記錄，這表示診斷問題將很快就成為極富挑戰性的工作，尤其是針對來自多個互動的 Azure Stack 元件的錯誤。 

我們的診斷工具協助確定記錄集合機制簡單而有效。 下圖說明記錄收集工具在 Azure Stack 中的運作方式：

![記錄收集工具](media/azure-stack-diagnostics/image01.png)
 
 
## <a name="trace-collector"></a>追蹤收集器
 
「追蹤收集器」預設為啟用。 它將會持續在背景中執行，在 Azure Stack 上從元件服務收集 Windows 事件追蹤 (ETW) 記錄，並將其儲存在一般本機共用上。 

下列是需要知道「追蹤收集器」的重要事項：
 
* 「追蹤收集器」會持續以預設大小限制執行。 每個檔案預設的允許大小上限 (200 MB) 並**不**是截止大小。 大小檢查將會定期執行 (目前為每隔 10 分鐘)，而如果目前檔案 > = 200 MB 時，其將會儲存並產生新的檔案。 每個事件工作階段總檔案大小也有 8 GB (可設定) 的限制。 一旦達到此限制，建立新檔案時將會同時刪除最舊的檔案。
* 記錄限制只會記錄 5 天。 此限制也可設定。 
* 每個元件會透過 JSON 檔案定義追蹤組態屬性。 JSON 檔案會儲存於 `C:\TraceCollector\Configuration`。 如有必要，可以編輯這些檔案來變更收集記錄的時間和大小限制。 變更這些檔案將會需要重新啟動「Microsoft Azure Stack 追蹤收集器」 服務，變更才會生效。
* 下列範例是適用於來自 XRP 虛擬機器 FabricRingServices 作業的追蹤組態 JSON 檔： 

```
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**

    此參數將會控制保留檔案的時間。 較舊的記錄檔將會受到刪除。
* **MaxSizeInMB**

    此參數控制單一檔案的大小臨界值。 如果達到大小限制，則會建立新的 .etl 檔案。
* **TotalSizeInMB**

    此參數控制事件工作階段所產生 .etl 檔的總大小。 如果總檔案大小大於此參數值，則較舊的檔案將會受到刪除。
  
## <a name="log-collection-tool"></a>記錄收集工具
 
PowerShell 命令 `Get-AzureStackLog` 可用來收集 Azure Stack 環境中所有元件的記錄。 它會將它們以 ZIP 檔案儲存在使用者定義的位置。 如果我們的技術支援小組需要您的記錄，以便針對問題進行疑難排解，小組可能會要求您執行此工具。

> [!CAUTION]
> 這些記錄檔可能包含個人識別資訊 (PII)。 在您公開公佈任何記錄檔之前，請先將這點納入考量。
 
我們目前將會收集下列記錄類型︰
*   **Azure Stack 部署記錄**
*   **Windows 事件記錄**
*   **Panther 記錄**

     若是針對 RDP 問題進行疑難排解。
*   **叢集記錄**
*   **儲存體診斷記錄**
*   **ETW 記錄**

    這些是「追蹤收集器」所收集且儲存在共用中，可使用 `Get-AzureStackLog` 進行擷取。
 
若要識別收集來自所有元件的所有記錄，請參閱「位於 `C:\EceStore\<Guid>\<GuidWithMaxFileSize>` 客戶設定檔中的 `<Logs>` 標記」。
 
**執行 Get-AzureStackLog**
1.  以 AzureStack\AzureStackAdmin 身分登入主機。
2.  以系統管理員身分開啟 PowerShell 視窗。
3.  執行 `Get-AzureStackLog`。  

    **範例**

    - 為所有角色收集所有記錄：

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs`

    - 從 VirtualMachines 與 BareMetal 角色收集記錄：

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal`

    - 從 VirtualMachines 和 BareMetal 角色收集記錄，且記錄日期篩選為過去 8 小時：

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date)`

如果未指定 `FromDate` 和 `ToDate` 參數，預設將會收集過去 4 小時的記錄。

目前您可以透過下列角色使用 `FilterByRole` 參數來篩選記錄集合：

|   |   |   |
| - | - | - |
| `ACSMigrationService`     | `ACSMonitoringService`   | `ACSSettingsService` |
| `ACS`                     | `ACSFabric`              | `ACSFrontEnd`        |
| `ACSTableMaster`          | `ACSTableServer`         | `ACSWac`             |
| `ADFS`                    | `ASAppGateway`           | `BareMetal`          |
| `BRP`                     | `CA`                     | `CPI`                |
| `CRP`                     | `DeploymentMachine`      | `DHCP`               |
|`Domain`                   | `ECE`                    | `ECESeedRing`        |        
| `FabricRing`              | `FabricRingServices`     | `FRP`                |
|` Gateway`                 | `HealthMonitoring`       | `HRP`                |               
| `IBC`                     | `InfraServiceController` | `KeyVaultAdminResourceProvider`|
| `KeyVaultControlPlane`    | `KeyVaultDataPlane`      | `NC`                 |            
| `NonPrivilegedAppGateway` | `NRP`                    | `SeedRing`           |
| `SeedRingServices`        | `SLB`                    | `SQL`                |     
| `SRP`                     | `Storage`                | `StorageController`  |
| `URP`                     | `UsageBridge`            | `VirtualMachines`    |  
| `WAS`                     | `WASPUBLIC`              | `WDS`                |


幾個注意事項：

* 此命令將會需要一些時間來收集記錄，視乎將收集哪些角色記錄。 促成因素包括指定收集記錄的進行時間，以及 Azure Stack 環境中的節點數目。
* 完成記錄收集之後，請檢查以命令指定 `-OutputPath` 參數所建立的新資料夾。
* 名為 `Get-AzureStackLog_Output.log` 的檔案將會在包含 ZIP 檔案的資料夾中建立，並包括命令輸出，可以用於針對記錄集合中的任何失敗進行疑難排解。
* 每個角色在個別 ZIP 檔案皆有其記錄。 
* 若要調查特定失敗原因，則可能需要多個元件的記錄。
    -   所有基礎結構虛擬機器的系統和事件記錄，皆會收集在「VirtualMachines」角色中。
    -   所有主機的系統和事件記錄，皆會收集在「BareMetal」角色中。
    -   容錯移轉叢集和 Hyper-V 事件記錄，皆會收集在「Storage」角色中。
    -   ACS 記錄則會收集在「Storage」和「ACS」角色中。
* 如需詳細資訊，您可以參考客戶設定檔。 為不同角色調查 `<Logs>`標記。

> [!NOTE]
> 我們將會強制執行大小和時間限制，因為確保您能有效率地利用儲存空間，以及其不會因記錄而塞滿極其重要。 強調的是當診斷問題時，因為強制執行這些限制，您通常需要的記錄可能已不存在。 因此，**強烈建議**您每隔 8 到 12 個小時將記錄卸載到外部儲存體空間 (Azure 中的儲存體帳戶、額外的內部部署儲存體裝置等)，並視需求將它們存放在該處 1-3 個月。

### <a name="multi-node-considerations"></a>多節點考量
如果您想要在多節點環境中收集記錄，請注意下列差異：
* `get-date` 函式在多節點環境中並未加入白名單中。 因此，您必須明確指定一個日期。 例如：

   `-FromDate "Friday, August 18, 2017 6:34:48 AM" -ToDate "Friday, August 18, 2017 7:35:25 AM"`
* 指定一個 UNC 路徑來輸出至硬體生命週期主機上的共用資料夾，或您可存取的任何其他共用資料夾。 例如：

   `Get-AzureStackLog -OutputSharePath \\10.193.128.250\logs -OutputShareCredential $sharecred`

   `-OutputShareCredential` 參數會提示您提供認證來存取共用資料夾。

## <a name="next-steps"></a>後續步驟
[Microsoft Azure Stack 疑難排解](azure-stack-troubleshooting.md)

