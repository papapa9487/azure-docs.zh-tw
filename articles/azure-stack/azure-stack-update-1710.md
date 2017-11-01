---
title: "Azure Stack 1710 更新 (組建 20171020.1) | Microsoft Docs"
description: "了解適用於 Azure Stack 整合系統之 1710 更新的新功能、已知問題，以及可從何處下載更新。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: twooley
ms.openlocfilehash: 6b54bb616accb926af9364865bf4108fe0aa3bc8
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="azure-stack-1710-update-build-201710201"></a>Azure Stack 1710 更新 (組建 20171020.1)

適用於：Azure Stack 整合系統

本文說明這個更新程式封裝中的改良功能與修正、此版本的已知問題，以及可從何處下載更新。 已知問題分為直接與更新程序相關的已知問題，以及組建 (安裝後) 相關的已知問題。

> [!IMPORTANT]
> 這個更新程式封裝僅適用於 Azure Stack 整合系統。 請勿將此更新程式封裝套用至 Azure Stack 開發套件。

## <a name="improvements-and-fixes"></a>改良功能與修正

此更新包含下列適用於 Azure Stack 的品質改良功能與修正。
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 的改良功能與修正

- 適用於 Windows Server 2016 的更新：2017 年 10 月 10 日 — KB4041691 (OS 組建 14393.1770)。 如需詳細資訊，請參閱 [https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691)。

### <a name="additional-quality-improvements-and-fixes"></a>其他的品質改良功能與修正

- 已新增具有特殊權限的端點 PowerShell Cmdlet，以協助對網路時間通訊協定 (NTP) 伺服器進行疑難排解和更新。
- 已新增對更新具有特殊權限之端點 Just Enough Administration (JEA) 端點模組和 Cmdlet 白名單的支援。 
- 已修正具有特殊權限之端點中的當地語言錯誤。
- 已新增輪替閘道認證的能力。
- 已移除 CBLocalAdmin 本機系統管理員帳戶。 
- 已修正活動訊號警示範本的內容，以確定活動訊號警示可在更新之後正確運作。
- 已修正在 FRU 作業期間處理逾時的網狀架構資源提供者。 
- 已為雲端開發人員新增在 Azure Stack 上使用 Azure Resource Manager API 設定檔的能力。
- 已停用部署虛擬機器 (DVM) 上的 Windows Update 服務。 
- 已從使用者介面中移除節點電源開啟/關閉動作。
- 各種其他的效能和穩定性修正。 
 
## <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題

本節包含在安裝 1710 更新時可能遇到的已知問題。

| 徵狀  | 原因  | 解決方案 |
|---------|---------|---------|
|當您執行更新時，可能會在更新動作方案的「儲存體主機重新啟動儲存體節點」步驟期間發生類似下列錯誤的錯誤。<br><br>**{"name":"重新啟動儲存體主機","description":"重新啟動儲存體主機。","errorMessage":"類型 'Restart' 的角色 'BareMetal' 引發了例外狀況:\n\n已略過電腦 HostName-05。無法透過 WMI 服務擷取其 LastBootUpTime，錯誤訊息如下: RPC 伺服器無法使用。(發生例外狀況於: 0x800706BA)。\nat Restart-Host** | 這個問題是由可能會在某些設定中出現的錯誤驅動程式所導致的。 | 1.登入基礎板管理控制器 (BMC) Web 介面，然後重新啟動錯誤訊息中識別出的主機。<br><br>2.繼續執行更新。 |
| 當您執行更新時，更新程序在更新動作方案的「步驟：正在執行步驟 2.4 - 安裝更新」步驟之後似乎停止且無任何進度。<br><br>接著，此步驟後面會跟著一連串將 .nupkg 檔案複製到內部基礎結構檔案共用的程序。 例如：<br><br>**正在將 1 個檔案從 content\PerfCollector\VirtualMachines 複製到 \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\PerfCounterConfiguration**  | 這個問題是由在基礎結構虛擬機器上填滿磁碟的記錄檔，以及將在後續更新中傳遞之 Windows Server 向外延展檔案伺服器 (SOFS) 中的問題所導致的。 | 請連絡 Microsoft 客戶服務與支援中心 (CSS) 以尋求協助。 | 
| 當您執行更新時，可能會在更新動作方案的「步驟：正在執行步驟 2.13.2 - 更新 *VM_Name*」步驟期間發生類似下列錯誤的錯誤 (虛擬機器的名稱可能不同)。<br><br>**ActionPlanInstanceWarning ece/MachineName: WarningMessage:Task: 角色 'Cloud\Fabric\WAS' 的介面 'LiveUpdate' 引動過程失敗:<br>類型 'LiveUpdate' 的角色 'WAS' 引發了例外狀況:<br>儲存體初始化期間發生錯誤: 嘗試對 Microsoft 存放裝置服務進行 API 呼叫時發生錯誤: {"Message":"與 Service Fabric 通訊時發生逾時。例外狀況類型: TimeoutException。例外狀況訊息: 作業已逾時。"}**  | 這個問題是由 Windows Server 中的 I/O 逾時所導致，將在後續更新中加以修正。 | 請連絡 Microsoft CSS 以尋求協助。
| 當您執行更新時，可能會在「步驟 21 重新啟動 SQL Server VM」步驟期間發生類似下列的錯誤。<br><br>**類型 'LiveUpdateRestart' 的角色 'VirtualMachines' 引發了例外狀況:<br>VerboseMessage:[VirtualMachines:LiveUpdateRestart] 正在查詢 VM MachineName-Sql01。 - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart] 已將 VM 標記為 HighlyAvailable。 - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart] at MS.Internal.ServerClusters.ExceptionHelp.Build at MS.Internal.ServerClusters.ClusterResource.BeginTakeOffline(Boolean force) at Microsoft.FailoverClusters.PowerShell.StopClusterResourceCommand.BeginTimedOperation() at Microsoft.FailoverClusters.PowerShell.TimedCmdlet.WrappedProcessRecord() at Microsoft.FailoverClusters.PowerShell.FCCmdlet.ProcessRecord() - 10/13/2017 5:11:50 PM WarningMessage:Task: 角色 'Cloud\Fabric\VirtualMachines' 的介面 'LiveUpdateRestart' 引動過程失敗:** | 如果虛擬機器無法重新啟動，可能會發生這個問題。 | 請連絡 Microsoft CSS 以尋求協助。
| 當您執行更新時，可能會發生類似下列的錯誤：<br><br>**2017-10-22T01:37:37.5369944Z 類型 'Shutdown' 的角色 'SQL' 引發了例外狀況:暫停節點 's45r1004-Sql01' 時發生錯誤。at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_App1\EnterpriseCloudEngineServicePkg.Code.1.0.597.18\CloudDeployment\Roles\SQL\SQL.psm1: line 542at Shutdown, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_App1\EnterpriseCloudEngineServicePkg.Code.1.0.597.18\CloudDeployment\Classes\SQL\SQL.psm1: line 50at <ScriptBlock>, <No file>: line 18at <ScriptBlock>, <No file>: line 16** | 如果無法使虛擬機器成為暫停狀態以清空角色，即會發生這個問題。 | 請連絡 Microsoft CSS 以尋求協助。
| 當您執行更新時，可能會發生下列其中一個錯誤：<br><br>**類型 'Validate' 的角色 'ADFS' 引發了例外狀況: 驗證 ADFS/Graph 角色失敗，錯誤為: 檢查 ADFS 探查端點 endpoint_URI 時發生錯誤: 以 "0" 引數呼叫 "GetResponse" 時發生例外狀況: "遠端伺服器傳回一個錯誤: (503) 伺服器無法使用。" at Invoke-ADFSGraphValidation**<br><br>**類型 'Validate' 的角色 'ADFS' 引發了例外狀況: 驗證 ADFS/Graph 角色失敗，錯誤為: 擷取 ADFS 屬性時發生錯誤: 無法連接到 net.tcp://localhost:1500/policy。連線嘗試持續了 00:00:02.0498923 時間範圍。TCP 錯誤碼 10061: 無法連線，因為目標電腦主動拒絕連線 127.0.0.1:1500。at Invoke-ADFSGraphValidation** | 更新動作方案無法驗證 Active Directory 同盟服務 (AD FS) 的健康情況。 | 請連絡 Microsoft CSS 以尋求協助。

## <a name="known-issues-post-installation"></a>已知問題 (安裝後)

本節包含關於組建 20171020.1 的安裝後已知問題。

### <a name="portal"></a>入口網站

- 它無法在管理員入口網站中檢視計算或儲存體資源。 這表示在更新安裝期間發生錯誤，且誤將更新報告為成功。 如果發生此問題，請連絡 Microsoft CSS 以尋求協助。
- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。
- [移動] 按鈕會在您檢視資源群組的內容時停用。 這是預期的行為。 目前不支援在訂用帳戶之間移動資源群組。
- 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。 因應措施是，可以使用 PowerShell 確認權限。
-  針對任何您可在下拉式清單中選取訂用帳戶、資源群組或位置的工作流程，您可能會遇到一或多個下列問題：

   - 您可能會在清單頂端看到一個空白資料列。 您應該仍能如預期般選取項目。
   - 如果下拉式清單中的項目清單很短，您可能無法檢視任何項目名稱。
   - 如果您有多個使用者訂用帳戶，資源群組的下拉式清單可能是空的。 

   若要解決後兩個問題，您可以輸入訂用帳戶或資源群組的名稱 (如果您知道)，或者可以改用 PowerShell。
  
### <a name="backup"></a>備份

- 請勿在 [基礎架構備份] 刀鋒視窗上啟用基礎結構備份。

### <a name="health-and-monitoring"></a>健康情況和監視

- 如果您重新啟動基礎結構角色執行個體，則可能會收到訊息，指出重新開機失敗。 不過，重新開機實際上是成功的。

### <a name="services"></a>服務

**Marketplace**
- 當您嘗試使用 [從 Azure 新增] 選項來將項目新增至 Azure Stack 市集時，可能不會看見所有可供下載的項目。
- 沒有任何可用以建立虛擬機器擴展集的市集體驗。 您可以使用範本來建立擴展集。
- 在訂用帳戶中建立第一個 Azure 函式之前，租用戶必須先註冊儲存體資源提供者。
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。 

**SQL/MySQL**
- 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。 
- 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能導致不相符的狀態。

**計算**
- 系統會提供選項，供使用者建立具備異地備援儲存體的虛擬機器。 此設定會導致虛擬機器建立失敗。
- 您可以設定只含一個容錯網域和一個更新網域的虛擬機器可用性設定組。
 
**網路**
- 您無法使用入口網站，利用公用 IP 位址來建立負載平衡器。 作為因應措施，您可以使用 PowerShell 來建立負載平衡器。
- 當您建立網路負載平衡器時，必須建立網路位址轉譯 (NAT) 規則。 如果沒有，在建立負載平衡器之後嘗試新增 NAT 規則時會收到錯誤。
 
### <a name="field-replaceable-unit-fru-procedures"></a>現場可更換單位 (FRU) 程序

- 在更新執行期間，無法修補離線映像。 如果您需要更換縮放單位節點，請洽詢您的 OEM 硬體廠商，以確定更換的節點具有最新修補程式等級。

## <a name="download-the-update"></a>下載更新

您可以在[這裡](https://aka.ms/azurestackupdatedownload)下載 1710 更新程式封裝。

## <a name="next-steps"></a>後續步驟

- 如需 Azure Stack 中更新管理的概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。
- 如需如何套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。