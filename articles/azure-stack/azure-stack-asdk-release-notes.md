---
title: "Microsoft Azure Stack 開發套件版本資訊 | Microsoft Docs"
description: 
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2bd93faf01c5d1790989a0231020ce8340eff57d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 開發套件版本資訊

適用於：Azure Stack 開發套件

這些版本資訊提供新功能和已知問題的相關資訊。

## <a name="release-build-201706271"></a>版本組建 20170627.1
從 [20170627.1](azure-stack-updates.md#determine-the-current-version) 版本開始，Azure Stack 概念證明已重新命名為 Azure Stack 開發套件。  與 Azure Stack POC 一樣，Azure Stack 開發套件也是作為開發和評估環境，用於探索 Azure Stack 功能，並提供 Azure Stack 的開發平台。

### <a name="whats-new"></a>新功能
- 您現在可以使用 CLI 2.0，從熱門作業系統中的命令列管理 Azure Stack 資源。
- DSV2 虛擬機器大小支援 Azure 和 Azure Stack 之間的範本可攜性。
- 雲端操作員可以在容量管理刀鋒視窗中預覽容量管理體驗。
- 您現在可以使用「Azure 診斷」擴充功能從虛擬機器收集診斷資料。  在分析工作負載效能及調查問題時，收集此資料會非常有用。
- 新的[部署體驗](azure-stack-run-powershell-script.md)取代了先前已編寫指令碼的部署步驟。  新的部署體驗會在整個部署生命週期中提供通用的圖形化介面。
- 現在部署期間支援 Microsoft 帳戶 (MSA)。
- 現在部署期間也支援 Multi-Factor Authentication (MFA)。  之前在部署期間必須停用 MFA。

### <a name="known-issues"></a>已知問題
#### <a name="deployment"></a>部署
* 您可能會注意到部署時使用的時間比舊版長。 
* Get-AzureStackLogs 可產生診斷記錄檔，但是不會將進度記錄至主控台。
* 您必須使用新的[部署體驗](azure-stack-run-powershell-script.md)部署 Azure Stack，否則部署可能會失敗。
* 使用 *PublicVLANID* 參數進行部署將會失敗。

#### <a name="portal"></a>入口網站
* 您可能會在入口網站中看到空白的儀表板。  您可以選取位於入口網站右上角的齒輪，然後選取 [還原預設設定]，以還原儀表板。
* 租用戶不需要訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。  對租用戶而言，這些項目都是非功能性項目。
* 在選取基礎結構角色執行個體時，您會看到顯示參考錯誤的錯誤。 請使用瀏覽器的重新整理功能重新整理管理入口網站。
* [資源群組] 刀鋒視窗上的 [移動] 按鈕已經停用。  這是預期的行為，因為目前不支援在訂用帳戶之間移動資源群組。
* 您將會收到和已完成下載之聯合市集項目有關的重複通知。
* 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。  解決方法是，可以使用 Powershell 確認權限。
* 從入口網站將完整的部署匯出為自動指令碼時，必須將 `-TenantID` 新增為旗標。

#### <a name="services"></a>服務
* 必須從租用戶入口網站或租用戶 API 建立 Key Vault 服務。  如果是以系統管理員身分登入，請務必使用租用戶入口網站建立新的 Key Vault 保存庫、密碼和金鑰。
* 雖然可以透過範本建立虛擬機器擴展集，但是沒有可用於建立它們的市集體驗。
* 您無法透過入口網站將負載平衡器與後端網路建立關聯。  此工作可以透過 PowerShell 或透過範本來完成。
* VM 可用性設定組只能設定一個容錯網域和一個更新網域。  
* 租用戶必須有現有的儲存體帳戶，才能建立新的 Azure Function。
* VM 可能會失敗並報告「無法將引數繫結到 'VM Network Adapter' 參數，因為它是 Null。」  虛擬機器重新部署成功。  
* 刪除租用戶訂用帳戶會導致產生孤立的資源。  因應措施是，先刪除租用戶資源或整個資源群組，然後再刪除租用戶訂用帳戶。 
* 建立網路負載平衡器時，必須建立 NAT 規則，否則在建立負載平衡器之後嘗試新增 NAT 規則時，會收到錯誤。
* 租用戶可以建立大於配額允許大小的虛擬機器。  此行為是因為未強制套用計算配額。
* 系統會提供選項，供租用戶建立具備異地備援儲存體的虛擬機器。  此設定會導致虛擬機器建立失敗。
* 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。 
* 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能會導致不相符的狀態。
* AzureRM PowerShell 1.2.10 需要額外的設定步驟：
    * 請在執行 Azure AD 部署的 Add-AzureRMEnvironment 之後執行此作業。  使用 `Add-AzureRMEnvironment` 的輸出提供 Name 和 GraphAudience 值。
      
      ```PowerShell
      Set-AzureRmEnvironment -Name <Environment Name> -GraphAudience <Graph Endpoint URL>
      ```
    * 請在執行 AD FS 部署的 Add-AzureRMEnvironment 之後執行此作業。  使用 `Add-AzureRMEnvironment` 的輸出提供 Name 和 GraphAudience 值。
      
      ```PowerShell
      Set-AzureRmEnvironment <Environment Name> -GraphAudience <Graph Endpoint URL> -EnableAdfsAuthentication:$true
      ```
    
    例如，以下項目用於 Azure AD 環境：

    ```PowerShell
      Set-AzureRmEnvironment AzureStack -GraphAudience https://graph.local.azurestack.external/
    ```

#### <a name="fabric"></a>網狀架構
* 計算資源提供者會顯示未知的狀態。
* 縮放單位節點的基本資訊中不會顯示 BMC IP 位址和模型。  這是 Azure Stack 開發套件中的預期行為。
* 不應該使用計算控制器基礎結構角色 (AzS-XRP01 執行個體) 上的重新啟動動作。
* 不應該使用基礎結構備份刀鋒視窗。

