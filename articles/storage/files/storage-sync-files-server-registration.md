---
title: "向 Azure 檔案同步 (預覽) 註冊/取消註冊伺服器 | Microsoft Docs"
description: "了解如何向 Azure 檔案同步儲存體同步服務註冊及取消註冊 Windows Server。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0acf183fbaea99e4316b668a3da28d79b20b7bef
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>向 Azure 檔案同步 (預覽) 註冊/取消註冊伺服器
透過 Azure 檔案同步 (預覽)，您可以在內部部署或 Azure 中複寫共用，並透過 Windows Server 上的 SMB 或 NFS 共用來存取。 Azure 檔案同步適用於離 Azure 資料中心很遠的位置 (例如分公司) 需要存取和修改資料的情況。 資料可以在多個 Windows Server 端點之間複寫，例如多個分公司之間。

下列文章說明如何向儲存體同步服務註冊及取消註冊伺服器。 這適用於伺服器正在解除委任或同步群組中需要新伺服器端點的情況。 如需如何部署端對端 Azure 檔案同步的資訊，請參閱[如何部署 Azure 檔案同步 (預覽)](storage-sync-files-deployment-guide.md)。

## <a name="prerequisites"></a>必要條件
若要向儲存體同步服務註冊 Windows Server，您必須先準備好符合下列必要條件的 Windows Server：

* 確定已部署儲存體同步服務。 如需如何部署儲存體同步服務的詳細資訊，請參閱[如何部署 Azure 檔案同步 (預覽)](storage-sync-files-deployment-guide.md)。
* 確定伺服器已連線到網際網路，而且 Azure 可供存取。
* 使用 [伺服器管理員] UI 停用系統管理員的 [IE 增強式安全性設定]。
    
    ![醒目提示 [IE 增強式安全性設定] 的 [伺服器管理員] UI](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* 確定您的伺服器上已安裝 AzureRM PowerShell 模組。 如果您的伺服器是容錯移轉叢集的成員，則叢集中的每個節點都需要 AzureRM 模組。 您可以在 [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (安裝和設定 Azure PowerShell) 中，找到有關如何安裝 AzureRM 模組的更多詳細資料。

    > [!Note]  
    > 建議使用最新版 AzureRM PowerShell 模組來註冊/取消註冊伺服器。 如果先前在此伺服器上已安裝 AzureRM 套件 (而且此伺服器上的 PowerShell 版本為 5.* 或更新版本)，您可以使用 `Update-Module` Cmdlet 更新此套件。 

## <a name="register-a-server-with-storage-sync-service"></a>向儲存體同步服務註冊伺服器
您必須向「儲存體同步服務」註冊 Windows Server，才能將伺服器當作 Azure 檔案同步之「同步群組」中的「伺服器端點」使用。 一次只能向一個「儲存體同步服務」註冊一部伺服器。

### <a name="install-the-azure-file-sync-agent"></a>安裝 Azure 檔案同步代理程式
1. [下載 Azure 檔案同步代理程式](https://go.microsoft.com/fwlink/?linkid=858257)。
2. 啟動 Azure 檔案同步代理程式安裝程式。
    
    ![Azure 檔案同步代理程式安裝程式的第一個窗格](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. 請務必使用 Microsoft Update 來啟用 Azure 檔案同步代理程式的更新。 這很重要，因為伺服器套件的重大安全性問題修正與功能加強隨附於 Microsoft Update。

    ![確定已在 Azure 檔案同步代理程式安裝程式的 [Microsoft Update] 窗格中啟用 Microsoft Update](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. 如果先前尚未註冊伺服器，完成安裝之後就會立即快顯伺服器註冊 UI。

> [!Important]  
> 如果伺服器是容錯移轉叢集的成員，則必須在叢集中的每個節點上安裝 Azure 檔案同步代理程式。

### <a name="register-the-server-using-the-server-registration-ui"></a>使用伺服器註冊 UI 註冊伺服器
1. 如果完成 Azure 檔案同步代理程式的安裝之後未立即啟動 [伺服器註冊] UI，您可以執行 `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` 手動將它啟動。
2. 按一下 [登入] 以存取您的 Azure 訂用帳戶。 

    ![開啟 [伺服器註冊] UI 的對話方塊](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. 從對話方塊中，選取正確的訂用帳戶、資源群組和儲存體同步服務。

    ![儲存體同步服務資訊](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. 在預覽版中，需要再登入一次才能完成此程序。 

    ![登入對話方塊](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> 如果伺服器是容錯移轉叢集的成員，每部伺服器都必須執行伺服器註冊。 當您在 Azure 入口網站中檢視已註冊的伺服器時，Azure 檔案同步會自動將每個節點識別為相同容錯移轉叢集的成員，並將它們適當地分組在一起。

## <a name="unregister-the-server-with-storage-sync-service"></a>向儲存體同步服務取消註冊伺服器
向儲存體同步服務取消註冊伺服器需要執行幾個步驟。 讓我們看看如何正確地取消註冊伺服器。

### <a name="optional-recall-all-tiered-data"></a>(選擇性) 重新叫用階層式資料
針對伺服器端點啟用時，雲端階層處理會將檔案「分層」到 Azure 檔案共用。 如此可讓內部部署檔案共用作為快取，而不是資料集的完整複本，以便有效率地使用檔案伺服器上的空間。 不過，如果移除伺服器端點時，本機伺服器上仍有階層式檔案，這些檔案會變成無法存取。 因此，如果需要繼續存取檔案，則必須重新叫用所有階層式檔案，再繼續取消註冊。 

這可以透過 PowerShell Cmdlet 來完成，如下所示：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> 如果裝載伺服器的本機磁碟區沒有足以重新叫用所有階層式資料的可用空間，`Invoke-StorageSyncFileRecall` Cmdlet 將會失敗。  

### <a name="remove-the-server-from-all-sync-groups"></a>從所有同步群組移除伺服器
取消註冊儲存體同步服務上的伺服器之前，必須先移除該伺服器的所有伺服器端點。 這可以透過入口網站來完成：

1. 巡覽至您的伺服器註冊所在的儲存體同步服務。
2. 在儲存體同步服務的每個同步群組中，移除此伺服器的所有伺服器端點。 這可以透過以滑鼠右鍵按一下 [同步群組] 窗格中的相關伺服器來完成。

    ![從同步群組移除伺服器端點](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

這也可以透過簡單的 PowerShell 指令碼來完成：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>取消註冊伺服器
重新叫用所有資料並從所有同步群組移除伺服器之後，即可取消註冊伺服器。 

1. 在 Azure 入口網站中，巡覽儲存體同步服務的 [已註冊的伺服器] 區段。
2. 以滑鼠右鍵按一下您要取消註冊的伺服器，然後按一下 [取消註冊伺服器]。

    ![取消註冊伺服器](media/storage-sync-files-server-registration/unregister-server-1.png)
