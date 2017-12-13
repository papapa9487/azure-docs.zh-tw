---
title: "Azure Stack 1711 更新 | Microsoft Docs"
description: "了解適用於 Azure Stack 整合系統之 1711 更新的新功能、已知問題，以及可從何處下載更新。"
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: andredm
ms.openlocfilehash: b9f45462fb108ff9cc9039cdb0d0a9ef318fc218
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="azure-stack-1711-update"></a>Azure Stack 1711 更新

適用於：Azure Stack 整合系統

本文說明這個更新程式封裝中的改良功能與修正、此版本的已知問題，以及可從何處下載更新。 已知問題分為直接與更新程序相關的已知問題，以及組建 (安裝後) 相關的已知問題。

> [!IMPORTANT]
> 此更新套件僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考

Azure Stack 1711 更新組建編號為 **171201.3**。

## <a name="before-you-begin"></a>開始之前

### <a name="prerequisites"></a>必要條件

您必須先安裝 Azure Stack [1710 更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710)之後才能套用此更新。

### <a name="new-features-and-fixes"></a>新功能和修正

此更新包含下列適用於 Azure Stack 的改良功能與修正。

#### <a name="new-features"></a>新功能

- 支援 Syndicating 解決方案範本
- Azure Stack 和 Graph 記錄與錯誤處理的更新
- 開啟和關閉主機的能力
- 使用者現在可以自動啟動 Windows VM
- 已新增特殊權限的端點 PowerShell Cmdlet 來就保留目的擷取 BitLocker 修復金鑰
- 更新基礎結構時，更新離線映像的支援

#### <a name="fixes"></a>修正

- 現場可更換單位 (FRU) 期間已修正 DNS 中的競爭條件，並已更新叢集記錄
- 現場可更換單位 (FRU) 中停用主機的重新啟動能力修正
- 各種其他的效能、安全性和穩定性修正

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 新功能和修正程式

- [2017 年 11 月 14 日—KB4048953 (OS 組建 14393.1884) ](https://support.microsoft.com/help/4048953)
 
### <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題

本節包含在 1711 更新安裝時可能遇到的已知問題。


1. **徵兆：**Azure Stack 作業員在更新程序期間可能會看到下列錯誤："name:Install Update.", "description": "Install Update on Hosts and Infra VMs.", "errorMessage": "Type 'LiveUpdate' of Role 'VirtualMachines' raised an exception:\n\nThere is not enough space on the disk.\n\nat <ScriptBlock>, <No file>: line22", "status": "Error", "startTimeUtc": "2017-11-10T16:46:59.123Z", "endTimeUtc": "2017-11-10T19:20:29.669Z", "steps": [ ]"
    2. **原因：**這個問題是因為屬於 Azure Stack 基礎結構的一或多個虛擬機器上可用磁碟空間不足
    3. **解決方式：**請連絡 Microsoft 客戶服務與支援中心 (CSS) 以尋求協助。
<br><br>
2. **徵兆：**Azure Stack 作業員在更新程序期間可能會看到下列錯誤：Exception calling "ExtractToFile" with "3" argument(s):"The process cannot access the file '<\\<machineName>-ERCS01\C$\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll>'
    1. **原因：**這個問題是因為繼續更新先前使用特殊權限結束點 (PEP) 繼續的入口網站所造成。
    2. **解決方式：**請連絡 Microsoft 客戶服務與支援中心 (CSS) 以尋求協助。
<br><br>
3. **徵兆：**Azure Stack 作業員在更新程序期間可能會看到下列錯誤：*"Type 'CheckHealth' of Role 'VirtualMachines' raised an exception:\n\nVirtual Machine health check for <machineName>-ACS01 produced the following errors.\nThere was an error getting VM information from hosts.例外狀況詳細資料：\nGet-VM : 電腦上的 'Node03' 作業失敗：WS-Management 服務無法處理要求。WMI \nservice 或 WMI 提供者傳回未知的錯誤：HRESULT 0x8004106c"。*
    1. **原因：**這個問題是因為 Windows Server 問題所造成，會在後續 Windows Server 更新中獲得解決。
    2. **解決方式：**請連絡 Microsoft 客戶服務與支援中心 (CSS) 以尋求協助。
<br><br>
4. **徵兆：**Azure Stack 作業員在更新程序期間可能會看到下列錯誤："Type 'DefenderUpdate' of Role 'URP' raised an exception: Failed getting version from \\SU1FileServer\SU1_Public\DefenderUpdates\x64\{file name}.exe after 60 attempts at Copy-AzSDefenderFiles, C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: line 262"
    1. **原因：**這個問題是因為 Windows Defender 定義更新的背景下載失敗或不完整所造成。
    2. **解決方式：**請在第一次更新嘗試起經過 8 小時之後，再嘗試繼續更新。

### <a name="known-issues-post-installation"></a>已知問題 (安裝後)

本節包含關於組建 **20171201.3** 的安裝後已知問題。

#### <a name="portal"></a>入口網站

- 它無法在管理員入口網站中檢視計算或儲存體資源。 這表示在更新安裝期間發生錯誤，且誤將更新報告為成功。 如果發生此問題，請連絡 Microsoft CSS 以尋求協助。
- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。
- 當您檢視資源群組的內容時，會停用 [移動] 按鈕。 這是預期的行為。 目前不支援在訂用帳戶之間移動資源群組。
- 針對任何您可在下拉式清單中選取訂用帳戶、資源群組或位置的工作流程，您可能會遇到一或多個下列問題：

   - 您可能會在清單頂端看到一個空白資料列。 您應該仍能如預期般選取項目。
   - 如果下拉式清單中的項目清單很短，您可能無法檢視任何項目名稱。
   - 如果您有多個使用者訂用帳戶，資源群組的下拉式清單可能是空的。 

        > [!NOTE]
        > 若要解決後兩個問題，您可以輸入訂用帳戶或資源群組的名稱 (如果您知道)，或者可以改用 PowerShell。

- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。
- 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。 因應措施是，可以使用 PowerShell 確認權限。

#### <a name="health-and-monitoring"></a>健康情況和監視

- 如果您重新啟動基礎結構角色執行個體，則可能會收到訊息，指出重新開機失敗。 不過，重新開機實際上是成功的。

#### <a name="marketplace"></a>Marketplace
- 當您嘗試使用 [從 Azure 新增] 選項來將項目新增至 Azure Stack 市集時，可能不會看見所有可供下載的項目。
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。

#### <a name="compute"></a>計算
- 系統會提供選項，供使用者建立具備異地備援儲存體的虛擬機器。 此設定會導致虛擬機器建立失敗。
- 您可以設定只含一個容錯網域和一個更新網域的虛擬機器可用性設定組。
- 沒有任何可用以建立虛擬機器擴展集的市集體驗。 您可以使用範本來建立擴展集。
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。
 
#### <a name="networking"></a>網路
- 您無法使用入口網站，利用公用 IP 位址來建立負載平衡器。 作為因應措施，您可以使用 PowerShell 來建立負載平衡器。
- 當您建立網路負載平衡器時，必須建立網路位址轉譯 (NAT) 規則。 如果沒有，在建立負載平衡器之後嘗試新增 NAT 規則時會收到錯誤。
- 建立 VM 並與公用 IP 位址建立關聯之後，您就無法取消該 IP 位址與虛擬機器 (VM) 的關聯。 取消關聯看似正常運作，但先前指派的公用 IP 位址仍然會與原始 VM 建立關聯。 即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。 您目前只有在建立新的 VM 時，才能使用新的公用 IP 位址。
- Azure Stack 操作員可能無法部署、刪除、修改 VNET 或網路安全性群組。 此問題主要會出現在相同套件的後續更新嘗試。 這是因目前正在進行調查的更新之封裝問題所致。
 
#### <a name="sqlmysql"></a>SQL/MySQL
- 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。 
- 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能導致不相符的狀態。
 
#### <a name="app-service"></a>App Service
- 在訂用帳戶中建立第一個 Azure 函式之前，使用者必須先註冊儲存體資源提供者。

#### <a name="identity"></a>身分識別

在已部署 Azure Active Directory Federation Services (ADFS) 的環境中，**azurestack\azurestackadmin** 帳戶不再是預設提供者訂用帳戶的擁有者。 並非使用 **azurestack\azurestackadmin** 登入 **Admin portal / adminmanagement endpoint**，您可以使用 **azurestack\cloudadmin** 帳戶，因此您可以管理及使用「預設提供者訂用帳戶」。

> [!IMPORTANT]
> 即使 **azurestack\cloudadmin** 帳戶是 ADFS 部署環境中「預設提供者訂用帳戶」的擁有者，但它並沒有 RDP 到主機的權限。 視需要繼續使用 **azurestack\azurestackadmin** 帳戶或本機系統管理員帳戶登入、存取和管理主機。

## <a name="download-the-update"></a>下載更新

您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1711 更新套件。

## <a name="more-information"></a>詳細資訊

Microsoft 已提供一個方式，可使用與更新 1711 一起安裝的特殊權限結束點 (PEP) 來監視及繼續更新。

- 請參閱[使用具有特殊權限的端點文件來監視 Azure Stack 中的更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)。 

## <a name="see-also"></a>另請參閱

- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。
