---
title: "Microsoft Azure Stack 開發套件版本資訊 | Microsoft Docs"
description: "Azure Stack 開發套件的增強功能、修正及已知問題。"
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2017
ms.author: andredm
ms.openlocfilehash: 6abbad8a086571702fd2e9d4d5d172189bb3c339
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 開發套件版本資訊

適用於：Azure Stack 開發套件

這些版本資訊提供 Azure Stack 開發套件中的增強功能、修正和已知問題的相關資訊。 如果您不確定所執行的版本，可以使用[入口網站來進行檢查](azure-stack-updates.md#determine-the-current-version)。

## <a name="build-201711221"></a>組建 20171122.1

### <a name="new-features-and-fixes"></a>新功能和修正

- 請參閱 Azure Stack 整合系統之 Azure Stack 1711 更新版本資訊的[新功能和修正](azure-stack-update-1711.md#new-features-and-fixes)一節。

    > [!IMPORTANT]
    > **新功能和修正**一節中所列的項目之中，有部分僅與 Azure Stack 整合系統相關。

### <a name="known-issues"></a>已知問題
 
#### <a name="deployment"></a>部署
- 在部署期間，您必須使用 IP 位址指定時間伺服器。

#### <a name="infrastructure-management"></a>基礎結構管理
- 請勿在 [基礎架構備份] 刀鋒視窗上啟用基礎結構備份。
- 縮放單位節點的基本資訊中不會顯示基礎板管理控制器 (BMC) IP 位址和模型。 這是 Azure Stack 開發套件中的預期行為。

#### <a name="portal"></a>入口網站
- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。
- 當您檢視資源群組的內容時，會停用 [移動] 按鈕。 這是預期的行為。 目前不支援在訂用帳戶之間移動資源群組。
-  針對任何您可在下拉式清單中選取訂用帳戶、資源群組或位置的工作流程，您可能會遇到一或多個下列問題：

   - 您可能會在清單頂端看到一個空白資料列。 您應該仍能如預期般選取項目。
   - 如果下拉式清單中的項目清單很短，您可能無法檢視任何項目名稱。
   - 如果您有多個使用者訂用帳戶，資源群組的下拉式清單可能是空的。 

   若要解決後兩個問題，您可以輸入訂用帳戶或資源群組的名稱 (如果您知道)，或者可以改用 PowerShell。

- 您會看到 [需要啟用] 警告警示，提示您註冊 Azure Stack 開發套件。 這是預期的行為。
- 如果從任何 [基礎結構角色] 警示按一下 [元件] 連結，產生的 [概觀] 刀鋒視窗就會嘗試載入並且失敗。 此外，[概觀] 刀鋒視窗不會逾時。
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。
- 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。 因應措施是，可以使用 PowerShell 確認權限。
 
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
- 如果您在 [網路] 下按一下 [連線] 來設定 VPN 連線，就會列出 **VNet 對 VNet** 作為可能的連線類型。 請勿選取此選項。 目前，僅支援**站對站 (IPsec)** 選項。
- 建立 VM 並與公用 IP 位址建立關聯之後，您就無法取消該 IP 位址與虛擬機器 (VM) 的關聯。 取消關聯看似正常運作，但先前指派的公用 IP 位址仍然會與原始 VM 建立關聯。 即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。 您目前只有在建立新的 VM 時，才能使用新的公用 IP 位址。
- Azure Stack 操作員可能無法部署、刪除、修改 VNET 或網路安全性群組。 此問題主要會出現在相同套件的後續更新嘗試。 這是因目前正在進行調查的更新之封裝問題所致。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。 
- 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能導致不相符的狀態。

#### <a name="app-service"></a>App Service
- 在訂用帳戶中建立第一個 Azure 函式之前，使用者必須先註冊儲存體資源提供者。
 
#### <a name="usage-and-billing"></a>使用量與計費
- 公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 EventDateTime 值，而不是建立記錄時顯示的 TimeDate 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。

#### <a name="identity"></a>身分識別

在已部署 Azure Active Directory Federation Services (ADFS) 的環境中，**azurestack\azurestackadmin** 帳戶不再是預設提供者訂用帳戶的擁有者。 並非使用 **azurestack\azurestackadmin** 登入 **Admin portal / adminmanagement endpoint**，您可以使用 **azurestack\cloudadmin** 帳戶，因此您可以管理及使用「預設提供者訂用帳戶」。

> [!IMPORTANT]
> 即使 **azurestack\cloudadmin** 帳戶是 ADFS 部署環境中「預設提供者訂用帳戶」的擁有者，但它並沒有 RDP 到主機的權限。 視需要繼續使用 **azurestack\azurestackadmin** 帳戶或本機系統管理員帳戶登入、存取和管理主機。


## <a name="build-201710201"></a>組建 20171020.1

### <a name="improvements-and-fixes"></a>改良功能與修正

若要查看 20171020.1 組建中的增強功能和修正清單，請參閱 Azure Stack 整合系統 1710 版本資訊的[增強功能和修正](azure-stack-update-1710.md#improvements-and-fixes)> 一節。 「其他品質增強功能與修正」一節中所列的一些項目僅與整合系統相關。

此外，已進行下列修正：
- 已修正計算資源提供者會顯示未知狀態的問題。
- 已修正在您建立配額，並於稍後嘗試檢視方案詳細資料之後，系統管理員入口網站中可能不會顯示配額的問題。

### <a name="known-issues"></a>已知問題

#### <a name="powershell"></a>PowerShell
- AzureRM 1.2.11 PowerShell 模組的發行版本隨附重大變更清單。 如需從 1.2.10 版升級的詳細資訊，請參閱[移轉指南](https://aka.ms/azspowershellmigration)。
 
#### <a name="deployment"></a>部署
- 在部署期間，您必須使用 IP 位址指定時間伺服器。

#### <a name="infrastructure-management"></a>基礎結構管理
- 請勿在 [基礎架構備份] 刀鋒視窗上啟用基礎結構備份。
- 縮放單位節點的基本資訊中不會顯示基礎板管理控制器 (BMC) IP 位址和模型。 這是 Azure Stack 開發套件中的預期行為。

#### <a name="portal"></a>入口網站
- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。
- 當您檢視資源群組的內容時，會停用 [移動] 按鈕。 這是預期的行為。 目前不支援在訂用帳戶之間移動資源群組。
-  針對任何您可在下拉式清單中選取訂用帳戶、資源群組或位置的工作流程，您可能會遇到一或多個下列問題：

   - 您可能會在清單頂端看到一個空白資料列。 您應該仍能如預期般選取項目。
   - 如果下拉式清單中的項目清單很短，您可能無法檢視任何項目名稱。
   - 如果您有多個使用者訂用帳戶，資源群組的下拉式清單可能是空的。 

   若要解決後兩個問題，您可以輸入訂用帳戶或資源群組的名稱 (如果您知道)，或者可以改用 PowerShell。

- 您會看到 [需要啟用] 警告警示，提示您註冊 Azure Stack 開發套件。 這是預期的行為。
- 在 [需要啟用] 警告警示詳細資料中，請勿按下 **AzureBridge** 元件的連結。 如果您這樣做，[概觀] 刀鋒視窗嘗試載入就會失敗，且不會逾時。
- 在系統管理員入口網站中，您可能會在 [通知] 區域中看到 [擷取租用戶時發生錯誤] 錯誤。 您可以放心地忽略此錯誤。
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。
- 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。 因應措施是，可以使用 PowerShell 確認權限。
 
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
- 如果您在 [網路] 下按一下 [連線] 來設定 VPN 連線，就會列出 **VNet 對 VNet** 作為可能的連線類型。 請勿選取此選項。 目前，僅支援**站對站 (IPsec)** 選項。
- 建立 VM 並與公用 IP 位址建立關聯之後，您就無法取消該 IP 位址與虛擬機器 (VM) 的關聯。 取消關聯看似正常運作，但先前指派的公用 IP 位址仍然會與原始 VM 建立關聯。 即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。 您目前只有在建立新的 VM 時，才能使用新的公用 IP 位址。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。 
- 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能導致不相符的狀態。

#### <a name="app-service"></a>App Service
- 在訂用帳戶中建立第一個 Azure 函式之前，使用者必須先註冊儲存體資源提供者。
 
#### <a name="usage-and-billing"></a>使用量與計費
- 公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 EventDateTime 值，而不是建立記錄時顯示的 TimeDate 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。

## <a name="build-201709283"></a>組建 20170928.3

### <a name="known-issues"></a>已知問題

#### <a name="powershell"></a>PowerShell
- AzureRM 1.2.11 PowerShell 模組的發行版本隨附重大變更清單。 如需從 1.2.10 版升級的詳細資訊，請參閱[移轉指南](https://aka.ms/azspowershellmigration)。

#### <a name="deployment"></a>部署
- 在部署期間，您必須使用 IP 位址指定時間伺服器。

 #### <a name="infrastructure-management"></a>基礎結構管理
- 請勿在 [基礎架構備份] 刀鋒視窗上啟用基礎結構備份。
- 計算資源提供者會顯示未知的狀態。
- 縮放單位節點的基本資訊中不會顯示基礎板管理控制器 (BMC) IP 位址和模型。 這是 Azure Stack 開發套件中的預期行為。 
   
#### <a name="portal"></a>入口網站
- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。
- 當您檢視資源群組的內容時，會停用 [移動] 按鈕。 這是預期的行為。 目前不支援在訂用帳戶之間移動資源群組。
- 您會看到 [需要啟用] 警告警示，提示您註冊 Azure Stack 開發套件。 這是預期的行為。
- 在 [需要啟用] 警告警示詳細資料中，請勿按下 **AzureBridge** 元件的連結。 如果您這樣做，[概觀] 刀鋒視窗嘗試載入就會失敗，且不會逾時。
- 正在您建立配額，並於稍後嘗試檢視方案詳細資料之後，系統管理員入口網站中可能不會顯示配額。 在 [服務和配額] 中按一下 [新增]來新增項目，以作為因應措施。
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。
- 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。 因應措施是，可以使用 PowerShell 確認權限。
  
#### <a name="marketplace"></a>Marketplace
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。
 
#### <a name="compute"></a>計算
- 系統會提供選項，供使用者建立具備異地備援儲存體的虛擬機器。 此設定會導致虛擬機器建立失敗。
- 您可以設定只含一個容錯網域和一個更新網域的虛擬機器可用性設定組。
- 沒有任何可用以建立虛擬機器擴展集的市集體驗。 您可以使用範本來建立擴展集。

#### <a name="networking"></a>網路
- 您無法使用入口網站，利用公用 IP 位址來建立負載平衡器。 作為因應措施，您可以使用 PowerShell 來建立負載平衡器。
- 當您建立網路負載平衡器時，必須建立網路位址轉譯 (NAT) 規則。 如果沒有，在建立負載平衡器之後嘗試新增 NAT 規則時會收到錯誤。
- 如果您在 [網路] 下按一下 [連線] 來設定 VPN 連線，就會列出 **VNet 對 VNet** 作為可能的連線類型。 請勿選取此選項。 目前，僅支援**站對站 (IPsec)** 選項。
- 建立 VM 並與公用 IP 位址建立關聯之後，您就無法取消該 IP 位址與虛擬機器 (VM) 的關聯。 取消關聯看似正常運作，但先前指派的公用 IP 位址仍然會與原始 VM 建立關聯。 即使您將 IP 位址重新指派給新的 VM (有時候稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。 您目前只有在建立新的 VM 時，才能使用新的公用 IP 位址。

#### <a name="sqlmysql"></a>SQL/MySQL
- 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。 
- 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能導致不相符的狀態。

#### <a name="app-service"></a>App Service
- 在訂用帳戶中建立第一個 Azure 函式之前，使用者必須先註冊儲存體資源提供者。
