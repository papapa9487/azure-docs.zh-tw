---
title: "針對 Azure 檔案同步 (預覽) 進行疑難排解 | Microsoft Docs"
description: "針對 Azure 檔案同步常見問題進行疑難排解"
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
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 1ea7956e92dbc85f62383e4b041c4c830599f765
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>針對 Azure 檔案同步 (預覽) 進行移難排解
Azure 檔案同步 (預覽) 可讓您將貴組織的檔案共用集中在「Azure 檔案」中，而不需要犧牲內部部署檔案伺服器的靈活度、效能及相容性。 它會將您的 Windows Server 轉換成 Azure 檔案共用的快速快取來達到這個目的。 您可以使用 Windows Server 上可用的任何通訊協定來存取本機資料 (包括 SMB、NFS 和 FTPS)，並且可以在世界各地擁有任何所需數量的快取。

本文旨在協助您針對使用 Azure 檔案同步部署時所發生的問題進行疑難排解，並解決這些問題。 如果無法做到，本指南還說明如何從系統收集重要的記錄檔，以協助更深入調查這些問題。 您可以使用下列選項來取得 Azure 檔案同步的支援：

- Microsoft 支援服務：若要建立新的支援案例，請巡覽至 Azure 入口網站上的 說明 + 支援 索引標籤，然後按一下新增支援要求。
- [Azure 儲存體論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazuredata)

## <a name="how-to-troubleshoot-agent-installation-failures"></a>如何針對代理程式安裝失敗進行疑難排解
如果 Azure 檔案同步代理程式安裝失敗，請從提升權限的命令提示字元執行下列命令，以在代理程式安裝期間啟用記錄：

```
StorageSyncAgent.msi /l*v Installer.log
```

一旦安裝失敗，請檢閱 installer.log 以判斷原因。 

> [!Note]  
> 如果您選擇使用 Microsoft Update 且 Windows Update 服務未執行，則代理程式安裝將會失敗。

## <a name="cloud-endpoint-creation-fails-with-the-following-error-the-specified-azure-fileshare-is-already-in-use-by-a-different-cloudendpoint"></a>雲端端點建立失敗，並出現下列錯誤：「指定的 Azure 檔案共用已由不同雲端端點使用中」
如果 Azure 檔案共用已由另一個雲端端點使用中，則會發生這個錯誤。 

如果您收到這個錯誤，而且 Azure 檔案共用目前未由雲端端點使用中，請執行下列步驟，清除 Azure 檔案共用上的 Azure 檔案同步中繼資料：

> [!Warning]  
> 刪除 Azure 檔案共用上目前正由雲端端點使用中的中繼資料時，將會導致 Azure 檔案同步作業失敗。 

1. 在 Azure 入口網站中瀏覽至您的 Azure 檔案共用。  
2. 以滑鼠右鍵按一下 Azure 檔案共用，然後選取 [編輯中繼資料]
3. 以滑鼠右鍵按一下 SyncService，然後選取 [刪除]。

## <a name="server-is-not-listed-under-registered-servers-in-the-azure-portal"></a>伺服器未列在 Azure 入口網站的 [已註冊的伺服器] 下
如果伺服器未列在儲存體同步服務的 [已註冊的伺服器] 下，請執行下列步驟：
1. 登入您要註冊的伺服器。
2. 開啟檔案總管，然後巡覽至儲存體同步代理程式安裝目錄 (預設位置是 `C:\Program Files\Azure\StorageSyncAgent`)。 
3. 執行 ServerRegistration.exe，然後遵循精靈向儲存體同步服務註冊伺服器。

## <a name="server-registration-displays-the-following-message-after-installing-the-azure-file-sync-agent-this-server-is-already-registered"></a>安裝 Azure 檔案同步代理程式之後，伺服器註冊會顯示下列訊息：「此伺服器已註冊」
![[伺服器註冊] 對話方塊的螢幕擷取畫面顯示 [此伺服器已註冊] 錯誤訊息](media/storage-sync-files-troubleshoot/server-registration-1.png)

如果先前已向儲存體同步服務註冊該伺服器，則會顯示此訊息。 若要向目前的儲存體同步服務取消註冊伺服器，再向新的儲存體同步服務註冊，請遵循步驟以[向 Azure 檔案同步取消註冊伺服器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)。

如果伺服器未列在儲存體同步服務的 [已註冊的伺服器] 底下，請在您想要取消註冊的伺服器上，執行下列 PowerShell 命令：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 如果伺服器是叢集的一部分，則有一個也會移除叢集註冊的選用 `Reset-StorageSyncServer -CleanClusterRegistration` 參數。 當取消註冊叢集中的最後一個節點時，應該使用這個參數。

## <a name="how-to-troubleshoot-sync-not-working-on-a-server"></a>如何針對同步在伺服器上無法運作的問題進行疑難排解
如果同步在伺服器上失敗，請執行下列作業：
- 確認伺服器端點存在於 Azure 入口網站中您要同步到 Azure 檔案共用的目錄：
    
    ![在 Azure 入口網站中同時具有雲端和伺服器端點的同步群組螢幕擷取畫面](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- 使用事件檢視器檢閱 `Applications and Services\Microsoft\FileSync\Agent` 下的作業和診斷事件記錄檔。
- 確認伺服器具有網際網路連線。
- 確認 Azure 檔案共用服務正在伺服器上執行，方法是開啟 [服務] MMC 嵌入式管理單元，並確認儲存體同步代理程式服務 (FileSyncSvc) 正在執行。

## <a name="how-to-troubleshoot-individual-files-failing-to-sync"></a>如何針對個別檔案無法同步的問題進行疑難排解 
如果個別檔案無法同步，請執行下列作業：
- 在事件檢視器中檢閱 `Applications and Services\Microsoft\FileSync\Agent` 下的作業和診斷事件記錄檔
- 確認檔案中沒有開啟的控制代碼
    - 注意：Azure 檔案同步會定期建立 VSS 快照集，來同步已開啟控制代碼的檔案

## <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>如何針對檔案無法分層的問題進行疑難排解
如果檔案無法分層到 Azure 檔案服務，請執行下列步驟：

- 確認檔案存在於 Azure 檔案共用
    - 注意：檔案必須同步到 Azure 檔案共用，才能將它分層
- 在事件檢視器中檢閱 `Applications and Services\Microsoft\FileSync\Agent` 下的作業和診斷事件記錄檔
- 確認伺服器具有網際網路連線 
- 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行
    - 開啟提升權限的命令提示字元，執行 `fltmc` 並確認列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式

## <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>如何針對無法重新叫用檔案的問題進行疑難排解
如果無法重新叫用檔案，請執行下列步驟：
- 在事件檢視器中檢閱 `Applications and Services\Microsoft\FileSync\Agent` 下的作業和診斷事件記錄檔
- 確認檔案存在於 Azure 檔案共用
- 確認伺服器具有網際網路連線 
- 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行
    - 開啟提升權限的命令提示字元，執行 `fltmc` 並確認列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式

## <a name="how-to-troubleshoot-files-being-unexpectedly-recalled-on-a-server"></a>如何針對在伺服器上意外重新叫用檔案的問題進行疑難排解
防毒程式、備份及其他讀取大量檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 略過支援產品的離線檔案有助於在執行防毒掃描或備份工作等作業時，避免發生不想要的重新叫用。

請洽詢您的軟體廠商，了解如何設定其解決方案以略過讀取離線檔案。

在其他情況下 (例如在檔案總管中瀏覽檔案)，可能會發生其他不想要的重新叫用。 在伺服器的檔案總管中開啟具有雲端階層式檔案的資料夾可能會導致不想要的重新叫用，特別是在伺服器上啟用防毒程式時。

## <a name="general-troubleshooting"></a>一般疑難排解
如果在伺服器上使用 Azure 檔案同步時發生問題，一開始請執行下列作業：
- 在事件檢視器中檢閱診斷和作業事件記錄檔
    - 系統會將同步、階層處理和重新叫用問題記錄在 `Applications and Services\Microsoft\FileSync\Agent` 下的診斷和作業事件記錄檔中
    - 管理伺服器的問題 (例如組態設定) 會記錄在 `Applications and Services\Microsoft\FileSync\Management` 下的診斷和作業事件記錄檔中
- 確認 Azure 檔案同步服務正在伺服器上執行
    - 開啟 [服務] MMC 嵌入式管理單元，並確認儲存體同步代理程式服務 (FileSyncSvc) 正在執行
- 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行
    - 開啟提升權限的命令提示字元，執行 fltmc 並確認列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式

如果執行上述步驟之後無法解決問題，請執行下列步驟來執行 AFSDiag 工具：
1. 建立用來儲存 AFSDiag 輸出的目錄 (例如 c:\output)。
2. 開啟提升權限的 PowerShell 視窗並執行下列命令 (每個命令後點擊 Enter 鍵)：

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. 針對 Azure 檔案同步核心模式追蹤層級，輸入 1 (除非指定建立更詳細的追蹤)，然後點擊 Enter 鍵。
4. 針對 Azure 檔案同步使用者模式追蹤層級，輸入 1 (除非指定建立更詳細的追蹤)，然後點擊 Enter 鍵。
5. 重現問題，並在完成時按 D 鍵。
6. 含有記錄檔和追蹤檔案的 .zip 檔案將會在指定的輸出目錄中。