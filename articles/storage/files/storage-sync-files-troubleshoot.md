---
title: "針對 Azure 檔案同步 (預覽) 進行疑難排解 | Microsoft Docs"
description: "針對 Azure 檔案同步常見問題進行疑難排解。"
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
ms.openlocfilehash: 265c5f660c4bee53a2faf4a073384587eb3f65fc
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>針對 Azure 檔案同步 (預覽) 進行移難排解
使用 Azure 檔案同步 (預覽)，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的靈活度、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定以從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文旨在協助您針對使用 Azure 檔案同步部署時所發生的問題進行疑難排解，並解決這些問題。 文中也說明如果需要更深入調查問題，如何從系統收集重要的記錄。 如果您找不到問題的答案，可透過下列管道 (依先後順序) 和我們連絡：

1. 本文的留言區。
2. [Azure 儲存體論壇](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)。
3. [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\)。 
4. Microsoft 支援服務。 若要建立新的支援要求，在 Azure 入口網站的 [說明] 索引標籤上，選取 [說明 + 支援] 按鈕，然後選取 [新增支援要求]。

## <a name="agent-installation-and-server-registration"></a>代理程式安裝和伺服器註冊
<a id="agent-installation-failures"></a>**針對代理程式安裝失敗進行疑難排解**  
如果 Azure 檔案同步代理程式安裝失敗，使用提升權限的命令提示字元，執行下列命令，以啟用代理程式安裝期間的記錄：

```
StorageSyncAgent.msi /l*v Installer.log
```

檢閱 installer.log 判斷安裝失敗的原因。 

> [!Note]  
> 如果您的機器設為使用 Microsoft Update 且 Windows Update 服務未執行，則代理程式安裝將會失敗。

<a id="server-registration-missing"></a>**伺服器未列在 Azure 入口網站的 [已註冊的伺服器] 下**  
如果伺服器未列在儲存體同步服務的 [已註冊的伺服器] 下：
1. 登入您要註冊的伺服器。
2. 開啟 [檔案總管]，然後移至儲存體同步代理程式的安裝目錄 (預設位置是 C:\Program Files\Azure\StorageSyncAgent)。 
3. 執行 ServerRegistration.exe，然後完成精靈的指示向儲存體同步服務註冊伺服器。

<a id="server-already-registered"></a>**Azure 檔案同步代理程式安裝期間，伺服器註冊顯示下列訊息：「此伺服器已註冊」** 

![[伺服器註冊] 對話方塊的螢幕擷取畫面顯示 [此伺服器已註冊] 錯誤訊息](media/storage-sync-files-troubleshoot/server-registration-1.png)

如果先前已向儲存體同步服務註冊該伺服器，就會出現此訊息。 若要向目前的儲存體同步服務取消註冊伺服器，再向新的儲存體同步服務註冊，請完成[向 Azure 檔案同步取消註冊伺服器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)中所述的步驟。

如果伺服器未列在儲存體同步服務的 [已註冊的伺服器] 底下，在您想要取消註冊的伺服器上，執行下列 PowerShell 命令：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 如果伺服器是叢集的一部分，您可以使用選擇性的 *Reset-StorageSyncServer -CleanClusterRegistration* 參數，一併移除叢集的註冊。

<a id="web-site-not-trusted"></a>**我在註冊伺服器時收到多個「網站不受信任」回應，原因為何？**  
如果在伺服器註冊期間啟用了**增強的 Internet Explorer 安全性**原則，便會發生此問題。 有關如何正確停用**增強的 Internet Explorer 安全性**原則，如需詳細資訊請參閱[準備 Windows Server 以搭配 Azure 檔案同步使用](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync)和[如何部署 Azure 檔案同步 (預覽)](storage-sync-files-deployment-guide.md)。

## <a name="sync-group-management"></a>同步群組管理
<a id="cloud-endpoint-using-share"></a>**雲端端點建立失敗，發生錯誤：「指定的 Azure 檔案共用已由不同雲端端點使用中」**  
如果 Azure 檔案共用已由另一個雲端端點使用中，就會發生這個問題。 

如果您看到這個訊息，而且 Azure 檔案共用目前未由雲端端點使用中，請執行下列步驟，清除 Azure 檔案共用上的 Azure 檔案同步中繼資料：

> [!Warning]  
> 刪除 Azure 檔案共用上目前正由雲端端點使用中的中繼資料時，會導致 Azure 檔案同步作業失敗。 

1. 在 Azure 入口網站中，移至您的 Azure 檔案共用。  
2. 以滑鼠右鍵按一下 Azure 檔案共用，然後選取 [編輯中繼資料]。
3. 以滑鼠右鍵按一下 [SyncService]，然後選取 [刪除]。

<a id="cloud-endpoint-authfailed"></a>**雲端端點建立失敗，發生錯誤："AuthorizationFailed"**  
如果您的使用者帳戶沒有建立雲端端點的足夠權限，就會發生此問題。 

若要建立雲端端點，您的使用者帳戶必須具有下列 Microsoft 授權權限：  
* 讀取：取得角色定義
* 寫入：建立或更新自訂角色定義
* 讀取：取得角色指派
* 寫入：建立角色指派

下列內建角色具有所需的 Microsoft 授權權限：  
* 擁有者
* 使用者存取系統管理員

判斷您的使用者帳戶角色是否具有所需的權限：  
1. 在 Azure 入口網站中，按一下 [資源群組]。
2. 選取儲存體帳戶所在的資源群組，然後選取 [存取控制 (IAM)]。
3. 選取您的使用者帳戶的 [角色] (例如，[擁有者]、[參與者])。
4. 在 [資源提供者] 清單中，選取 [Microsoft 授權]。 
    * [角色指派] 應具有 [讀取] 和 [寫入] 權限。
    * [角色定義] 應具有 [讀取] 和 [寫入] 權限。

<a id="cloud-endpoint-deleteinternalerror"></a>**端點刪除失敗，發生錯誤："MgmtInternalError"**  
如果在刪除雲端端點之前就已刪除 Azure 檔案共用或儲存體帳戶，可能會發生此問題。 這個問題將在未來版本中修正。 現階段，您能夠先刪除 Azure 檔案共用或儲存體帳戶，再刪除雲端端點。

同時，為了避免發生此問題，請先刪除雲端端點，然後再刪除 Azure 檔案共用或儲存體帳戶。

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**如果我直接在我的 Azure 檔案共用中透過 SMB 建立檔案，或是透過入口網站建立檔案，要等多久檔案才會同步至同步群組中的伺服器？**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**伺服器上的同步處理失敗**  
如果伺服器上的同步處理失敗：
1. 確認伺服器端點存在於 Azure 入口網站中您要同步到 Azure 檔案共用的目錄：
    
    ![在 Azure 入口網站中同時顯示雲端端點和伺服器端點的同步群組螢幕擷取畫面](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. 在 [事件檢視器] 中，檢閱 Applications and Services\Microsoft\FileSync\Agent 下的作業和診斷事件記錄。
    1. 確認伺服器具有網際網路連線。
    2. 確認 Azure 檔案同步服務正在伺服器上執行。 做法是，開啟 [服務] MMC 嵌入式管理單元，並確認儲存體同步代理程式服務 (FileSyncSvc) 正在執行。

<a id="replica-not-ready"></a>**同步處理失敗，發生錯誤：「0x80c8300f - 複本未就緒，無法執行要求的作業」**  
如果您建立雲端端點並使用包含資料的 Azure 檔案共用，便會發生此預期的問題。 當 Azure 檔案共用上的變更偵測完成 (最多可能需要 24 小時)，同步處理應該就能開始正常運作。

<a id="broken-sync-files"></a>**針對無法同步的個別檔案進行疑難排解**  
如果個別檔案無法同步：
1. 在 [事件檢視器] 中，檢閱 Applications and Services\Microsoft\FileSync\Agent 下的作業和診斷事件記錄。
2. 確認檔案中沒有開啟的控制代碼。

    > [!NOTE]
    > Azure 檔案同步會定期建立 VSS 快照集，來同步已開啟控制代碼的檔案。

## <a name="cloud-tiering"></a>雲端階層處理 
<a id="files-fail-tiering"></a>**針對檔案無法分層的問題進行疑難排解**  
如果無法在 Azure 檔案中將檔案分層：

1. 確認檔案存在於 Azure 檔案共用中。

    > [!NOTE]
    > 檔案必須同步到 Azure 檔案共用，才能將它分層。
2. 在 [事件檢視器] 中，檢閱 Applications and Services\Microsoft\FileSync\Agent 下的作業和診斷事件記錄。
    1. 確認伺服器具有網際網路連線。 
    2. 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行：
        - 在提高權限的命令提示字元，執行 `fltmc`。 確認已列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式。

<a id="files-fail-recall"></a>**針對無法重新叫用檔案的問題進行疑難排解**  
如果無法重新叫用檔案：
1. 在 [事件檢視器] 中，檢閱 Applications and Services\Microsoft\FileSync\Agent 下的作業和診斷事件記錄。
    1. 確認檔案存在於 Azure 檔案共用中。
    2. 確認伺服器具有網際網路連線。 
    3. 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行：
        - 在提高權限的命令提示字元，執行 `fltmc`。 確認已列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式。

<a id="files-unexpectedly-recalled"></a>**針對在伺服器上意外重新叫用檔案的問題進行疑難排解**  
防毒程式、備份及其他讀取大量檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致非預期的重新叫用。 略過支援此選項之產品的離線檔案，有助於在執行防毒掃描、備份等作業時，避免發生非預期的重新叫用。

請洽詢您的軟體廠商，了解如何設定其解決方案以略過讀取離線檔案。

非預期的重新叫用也會在其他情況下發生，例如在 [檔案總管] 中瀏覽檔案時。 在伺服器的 [檔案總管]中開啟包含雲端層檔案的資料夾，可能會導致非預期的重新叫用。 特別是伺服器上已啟用防毒程式時，更容易發生。

## <a name="general-troubleshooting"></a>一般疑難排解
如果在伺服器上使用 Azure 檔案同步時發生問題，請完成以下步驟開始疑難排解：
1. 在 [事件檢視器] 中檢閱作業和診斷事件記錄。
    - 系統會將同步、階層處理和重新叫用的問題記錄在 Applications and Services\Microsoft\FileSync\Agent 下的診斷和作業事件記錄檔中。
    - 與管理伺服器相關的問題 (例如組態設定) 則記錄在 Applications and Services\Microsoft\FileSync\Management 下的作業和診斷事件記錄中。
2. 確認 Azure 檔案同步服務正在伺服器上執行：
    - 開啟 [服務] MMC 嵌入式管理單元，並確認儲存體同步代理程式服務 (FileSyncSvc) 正在執行。
3. 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行：
    - 在提高權限的命令提示字元，執行 `fltmc`。 確認已列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式。

如果問題沒有解決，執行 AFSDiag 工具：
1. 建立將用來儲存 AFSDiag 輸出的目錄 (例如 C:\Output)。
2. 開啟提升權限的 PowerShell 視窗，然後執行下列命令 (每個命令後要按 Enter 鍵)：

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. 針對 Azure 檔案同步核心模式追蹤層級，輸入 **1** (除非為了建立更詳細的追蹤而指定其他值)，然後按 Enter。
4. 針對 Azure 檔案同步使用者模式追蹤層級，輸入 **1** (除非為了建立更詳細的追蹤而指定其他值)，然後按 Enter。
5. 重現問題。 完成時，輸入 **D**。
6. 含有記錄和追蹤檔案的 .zip 檔案將會儲存在指定的輸出目錄中。

## <a name="see-also"></a>另請參閱
- [Azure 檔案服務常見問題集](storage-files-faq.md)
- [針對 Windows 中的 Azure 檔案服務問題進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)
- [針對 Linux 中的 Azure 檔案服務問題進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
