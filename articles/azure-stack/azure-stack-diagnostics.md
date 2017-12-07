---
title: "Azure Stack 中的診斷功能"
description: "如何在 Azure Stack 中收集記錄檔以進行診斷"
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/28/2017
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 16b56c71e2c81bead7c578a973840391996e845b
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack 診斷工具

適用於：Azure Stack 整合系統和 Azure Stack 開發套件
 
Azure Stack 是元件共同作業並與彼此互動的大型集合。 這所有元件都會產生自己唯一的記錄。 這可能使得診斷問題成為極富挑戰性的工作，尤其是針對來自多個互動的 Azure Stack 元件的錯誤。 

我們的診斷工具協助確保記錄集合機制簡單而有效。 下圖說明記錄收集工具在 Azure Stack 中的運作方式：

![Azure Stack 診斷工具](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>追蹤收集器
 
追蹤收集器依預設會啟用，並在背景中連續執行以從 Azure Stack 元件服務收集所有的 Windows 事件追蹤 (ETW) 記錄。 ETW 記錄會儲存在通用的本機共用內，保留天數為五天。 一旦達到此限制，建立新檔案時將會同時刪除最舊的檔案。 每個檔案預設的允許大小上限為 200 MB。 大小檢查將會定期執行 (每隔 2 分鐘)，而如果目前檔案 > = 200 MB 時，其將會儲存並產生新的檔案。 每個事件工作階段總檔案大小也有 8 GB 的限制。 

## <a name="log-collection-tool"></a>記錄收集工具
 
PowerShell Cmdlet **Get-AzureStackLog** 可用來收集 Azure Stack 環境中所有元件的記錄。 它會將它們以 ZIP 檔案儲存在使用者定義的位置。 如果我們的技術支援小組需要您的記錄，以便針對問題進行疑難排解，小組可能會要求您執行此工具。

> [!CAUTION]
> 這些記錄檔可能包含個人識別資訊 (PII)。 在您公開公佈任何記錄檔之前，請先將這點納入考量。
 
以下是收集到的一些範例記錄類型：
*   **Azure Stack 部署記錄**
*   **Windows 事件記錄**
*   **Panther 記錄**
*   **叢集記錄**
*   **儲存體診斷記錄**
*   **ETW 記錄**

追蹤收集器會收集這些檔案並儲存在共用內。 然後，可於必要時使用 **Get-AzureStackLog** PowerShell Cmdlet 來收集這些檔案。
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>在 Azure Stack 開發套件 (ASDK) 系統上執行 Get-AzureStackLog
1. 以 **AzureStack\CloudAdmin** 身分登入主機。
2. 以系統管理員身分開啟 PowerShell 視窗。
3. 執行 **Get-AzureStackLog** PowerShell Cmdlet。

**範例：**

  為所有角色收集所有記錄：

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  從 VirtualMachines 與 BareMetal 角色收集記錄：

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  從 VirtualMachines 和 BareMetal 角色收集記錄，且記錄日期篩選為過去 8 小時：
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  從 VirtualMachines 和 BareMetal 角色收集記錄，以日期篩選 8 小時前到 2 小時前時段的記錄檔：

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="to-run-get-azurestacklog-on-an-azure-stack-integrated-system"></a>若要在 Azure Stack 整合系統上執行 Get-AzureStackLog

若要在整合系統上執行記錄收集工具，您需要能夠存取特殊權限端點 (PEP)。 以下是您可以使用 PEP 執行，以在整合系統上收集記錄的範例指令碼：

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- 從 PEP 收集記錄時，將 **OutputPath** 參數指定為硬體生命週期主機 (HLH) 機器上的位置。 也請確定位置已加密。
- **OutputSharePath** 和 **OutputShareCredential** 參數是選擇性的，並且是在您將記錄上傳至外部共用資料夾時使用。 請使用這些參數，但 **OutputPath** 除外。 如果未指定 **OutputPath**，記錄收集工具會使用 PEP VM 的系統磁碟機進行儲存。 這可能會導致指令碼失敗，因為磁碟機空間有限。
- 如先前範例中所示，**FromDate** 和 **ToDate** 參數可以用來收集特定時段的記錄。 對於在整合系統上套用更新套件後收集記錄之類案例中，這會非常方便。

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>ASDK 和整合系統的參數考量

- 如果未指定 **FromDate** 和 **ToDate** 參數，預設將會收集過去 4 小時的記錄。
- 您可以使用 **TimeOutInMinutes** 參數來設定記錄收集的逾時。 根據預設，它是設定為 150 (2.5 個小時)。

- 目前您可以透過下列角色使用 **FilterByRole** 參數來篩選記錄集合：

   |   |   |   |
   | - | - | - |
   | ACSMigrationService     | ACSMonitoringService   | ACSSettingsService |
   | ACS                     | ACSFabric              | ACSFrontEnd        |
   | ACSTableMaster          | ACSTableServer         | ACSWac             |
   | ADFS                    | ASAppGateway           | BareMetal          |
   | BRP                     | CA                     | CPI                |
   | CRP                     | DeploymentMachine      | DHCP               |
   | 網域                  | ECE                    | ECESeedRing        | 
   | FabricRing              | FabricRingServices     | FRP                |
   | 閘道器                 | HealthMonitoring       | HRP                |   
   | IBC                     | InfraServiceController | KeyVaultAdminResourceProvider|
   | KeyVaultControlPlane    | KeyVaultDataPlane      | NC                 |   
   | NonPrivilegedAppGateway | NRP                    | SeedRing           |
   | SeedRingServices        | SLB                    | SQL                |   
   | SRP                     | 儲存體                | StorageController  |
   | URP                     | UsageBridge            | VirtualMachines    |  
   | WAS                     | WASPUBLIC              | WDS                |


### <a name="collect-logs-using-a-graphical-user-interface"></a>使用圖形化使用者介面收集記錄
與其提供所需參數供 Get-AzureStackLog Cmdlet 擷取 Azure Stack 記錄，您也可以利用可用的開放原始碼 Azure Stack 工具 (位於主要的 Azure Stack 工具 GitHub 存放庫，網址為 http://aka.ms/AzureStackTools)。

**ERCS_AzureStackLogs.ps1** PowerShell 指令碼儲存在 GitHub 工具存放庫，並且會定期更新。 從系統管理 PowerShell 工作階段開始，指令碼會連線到特殊權限端點，並使用提供的參數執行 Get-AzureStackLog。 如果未提供任何參數，指令碼會預設為透過圖形化使用者介面提示您提供參數。

若要深入了解 ERCS_AzureStackLogs.ps1 PowerShell 指令碼，您可以觀看 Azure Stack 工具 GitHub 存放庫上的[短片](https://www.youtube.com/watch?v=Utt7pLsXEBc)或檢視指令碼的[讀我檔案](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md)。 

### <a name="additional-considerations"></a>其他考量

* 根據記錄要收集的角色，此命令需要一些時間來執行。 促成因素也包括指定收集記錄的進行時間，以及 Azure Stack 環境中的節點數目。
* 完成記錄收集之後，請檢查以命令指定 **OutputPath** 參數所建立的新資料夾。
* 每個角色在個別 ZIP 檔案皆有其記錄。 根據所收集的記錄的大小，角色的記錄可能會分割為多個 zip 檔案。 對於這類角色，如果您想要將所有記錄檔解壓縮至單一資料夾，請使用可以大量解壓縮的工具 (例如 7zip)。 選取角色的所有壓縮檔案，然後選取 [解壓縮到這裡]。 這會將該角色的所有記錄檔解壓縮至單一合併的資料夾中。
* 也會在包含壓縮記錄檔的資料夾中建立名為 **Get-AzureStackLog_Output.log** 的檔案。 這個檔案是命令輸出的記錄，可用於疑難排解記錄收集期間的問題。
* 若要調查特定失敗原因，則可能需要多個元件的記錄。
    -   所有基礎結構虛擬機器的系統和事件記錄，皆會收集在「VirtualMachines」角色中。
    -   所有主機的系統和事件記錄，皆會收集在「BareMetal」角色中。
    -   容錯移轉叢集和 Hyper-V 事件記錄，皆會收集在「Storage」角色中。
    -   ACS 記錄則會收集在「Storage」和「ACS」角色中。

> [!NOTE]
> 我們會強制執行大小和時間限制，因為確保您能有效率地利用儲存空間，以及其不會因記錄而塞滿極其重要。 不過，因為這些限制，在診斷問題時，有時需要的記錄可能不再存在。 因此，**強烈建議**您每隔 8 到 12 個小時將記錄卸載到外部儲存體空間 (Azure 中的儲存體帳戶、額外的內部部署儲存體裝置等)，並視需求將它們存放在該處 1-3 個月。 也請確定儲存位置已加密。

## <a name="next-steps"></a>後續步驟
[Microsoft Azure Stack 疑難排解](azure-stack-troubleshooting.md)
