---
title: "Microsoft Azure Stack 疑難排解 | Microsoft Docs"
description: "Azure Stack 疑難排解。"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 3b40a657ee8eb391d14a38cb95acc0729a8dda21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure Stack 疑難排解

適用於：Azure Stack 開發套件

此文件提供 Azure Stack 的常見疑難排解資訊。 

因為「Azure Stack 技術開發套件」是以評估環境方式提供，「Microsoft 客戶支援服務」不提供官方支援。  若您遇到未記載的問題，請務必檢查 [Azure Stack MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)以取得進一步的協助與資訊。  

本節中針對疑難排解問題的建議衍生自數個來源，而且不保證能解決您的問題。 程式碼範例係依現況提供，而且無法保證程式碼會產生預期結果。 當產品功能改良時，此節可能也會更新。

## <a name="deployment"></a>部署
### <a name="deployment-failure"></a>部署失敗
若安裝失敗，您可以使用部署指令碼的重新執行選項從失敗的步驟重新開始部署。  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>在部署結束時，PowerShell 工作階段仍開啟，而且不會顯示任何輸出
此行為可能只是 PowerShell 命令視窗預設行為的結果 (當已選取它時)。 開發套件部署實際上成功，但當您選取視窗時，指令碼已暫停。 您可以尋找命令視窗標題列的「選取」字樣以驗證是不是這種情況。  按 ESC 鍵以將它取消選取，之後將顯示完成訊息。

## <a name="templates"></a>範本
### <a name="azure-template-wont-deploy-to-azure-stack"></a>Azure 範本將不會部署到 Azure Stack
請確定：

* 範本必須使用已經可用或在 Azure Stack 中為預覽版的 Microsoft Azure 服務。
* 本機 Azure Stack 執行個體與您以有效位置 (在 Azure Stack 開發套件中為「本機」，在 Azure 中則為「美國東部」或「印度南部」) 為目標的 Azure Stack 執行個體，都支援用於特定資源的 API。
* 您可以檢閱[此文章](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md)以了解 Test-AzureRmResourceGroupDeployment Cmdlet，這與 Azure Resource Manager 語法有些微差異。

您也可以使用已在 [GitHub 存放庫](http://aka.ms/AzureStackGitHub/)提供的 Azure Stack 範本來協助您開始。

## <a name="virtual-machines"></a>虛擬機器
### <a name="default-image-and-gallery-item"></a>預設映像與資源庫項目
在 Azure Stack 中部署 VM 之前，您必須先新增 Windows Server 映像與資源庫項目。

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>重新啟動我的 Azure Stack 主機之後，某些 VM 可能不會自動啟動。
將您的主機重新開機之後，您可能會注意到 Azure Stack 服務並非立即可用。  這是因為 Azure Stack [基礎結構 VM](azure-stack-architecture.md#virtual-machine-roles) 與 RP 需要一些時間來檢查一致性，但最終將會自動啟動。

您可能也會注意到當 Azure Stack 開發套件主機重新開機之後，租用戶 VM 未自動重新啟動。  這是已知問題，而且只需要手動執行一些步驟就能讓它們上線：

1.  在 Azure Stack 開發套件主機上，從 [開始] 功能表啟動 [容錯移轉叢集管理員]。
2.  選取叢集 **S-Cluster.azurestack.local**。
3.  選取 [角色]。
4.  租用戶 VM 將會顯示為「已儲存」狀態。  一旦所有巳基礎結構 VM 都正常執行，在租用戶 VM 上按一下滑鼠右鍵，然後選取 [啟動] 以繼續該 VM。

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>我已刪除某些虛擬機器，但仍在磁碟上看到 VHD 檔案。 這是不是預期行為？
是，這是預期行為。 將它設計成這樣的原因是：

* 當您刪除 VM 時，不會刪除 VHD。 磁碟是資源群組中的獨立資源。
* 當儲存體帳戶被刪除時，Azure Resource Manager (入口網站、PowerShell) 將會立即反映刪除情況，但其中可能包含的磁碟仍保留在儲存體中，直到執行記憶體回收為止。

若您看到「孤立」的 VHD，您必須知道它是否屬於已刪除之儲存體帳戶的資料夾。 若該儲存體帳戶未被刪除，則它們仍然存在於該處就是正常情況。

您可以在[管理儲存體帳戶](azure-stack-manage-storage-accounts.md)中深入了解如何設定保留閾值與隨選回收。

## <a name="storage"></a>儲存體
### <a name="storage-reclamation"></a>儲存體回收
已回收的容量最多可能需要 14 小時才會顯示在入口網站中。 空間回收取決於各種因素，包括區塊 Blob 存放區中內部容器檔案的使用量百分比。 因此，視刪除的資料量而定，我們無法保證當記憶體回收行程執行時可回收多少空間。

## <a name="powershell"></a>PowerShell
### <a name="resource-providers-not-registered"></a>資源提供者未註冊
當使用 PowerShell 連線到租用戶訂用帳戶時，您將會注意到資源提供者未自動註冊。 使用[Connect 模組](https://github.com/Azure/AzureStack-Tools/tree/master/Connect)，或從 PowerShell 執行下列命令 (在您[安裝並連線為](azure-stack-connect-powershell.md)租用戶之後)： 
  
       Get-AzureRMResourceProvider | Register-AzureRmResourceProvider

## <a name="cli"></a>CLI

* CLI 互動模式 (例如 `az interactive` 命令) 在 Azure Stack 中尚未支援。
* 若要取得 Azure Stack 中的可用虛擬機器映像清單，請使用 `az vm images list --all` 命令，而非 `az vm image list` 命令。 指定 `--all` 選項可確保回應只傳回您 Azure Stack 環境中的可用映像。 
* Azure 中可用的虛擬機器映像別名可能不適用於 Azure Stack。 使用虛擬機器映像時，您必須使用整個 URN 參數 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0)，而非映像別名。 此外，此 URN 必須符合從 `az vm images list` 命令衍生的映像規格。
* 根據預設值，CLI 2.0 使用 “Standard_DS1_v2” 作為預設虛擬機器映像大小。 不過，此大小在 Azure Stack 中還無法使用，因此您必須在建立虛擬機器時明確地指定 `--size` 參數。 您可以使用 `az vm list-sizes --location <locationName>` 命令來取得 Azure Stack 中的可用虛擬機器大小清單。


## <a name="windows-azure-pack-connector"></a>Windows Azure 套件連接器
* 若您在部署 Azure Stack 開發套件之後變更 azurestackadmin 帳戶的密碼，則您再也無法設定多雲端模式。 因此，將無法連線到目標 Windows Azure 套件環境。
* 當您設定多雲端模式之後：
    * 使用者只有在重設入口網站設定之後，才能看到儀表板 (在使用者入口網站中，按一下入口網站設定圖示 (右上角的齒輪圖示)。 在 [還原預設設定] 下，按一下 [套用])。
    * 儀表板標題可能不會出現。 若發生此問題，您必須手動將它們新增回來。
    * 首次將圖格新增到儀表板時，某些圖格可能無法正確顯示。 若要修正此問題，請重新整理瀏覽器。



