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
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cf3f3cf63cafc3b883d26144a53066ee421eb2a6
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshoot-azure-file-sync-preview"></a>針對 Azure 檔案同步 (預覽) 進行移難排解
透過 Azure 檔案同步 (預覽)，您可以將共用複寫到內部部署或 Azure 中的 Windows Server。 您及您的使用者接著可透過 Windows Server 存取檔案共用，例如透過 SMB 或 NFS 共用。 這特別適用於離 Azure 資料中心很遠的位置 (例如分公司) 存取和修改資料的情況。 資料可以在多個 Windows Server 端點之間複寫，例如多個分公司之間。

本文旨在協助您針對使用 Azure 檔案同步部署時所發生的問題進行疑難排解，並解決這些問題。 如果無法做到，本指南還說明如何從系統收集重要的記錄檔，以協助更深入調查這些問題。 您可以使用下列選項來取得 Azure 檔案同步的支援：

- Microsoft 支援服務：若要建立新的支援案例，請巡覽至 Azure 入口網站上的 [說明 + 支援] 索引標籤，然後按一下 [新增支援要求]。
- [Azure 儲存體論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazuredata)

## <a name="how-to-troubleshoot-agent-installation-failures"></a>如何針對代理程式安裝失敗進行疑難排解
如果 Azure 檔案同步代理程式安裝失敗，請從提升權限的命令提示字元執行下列命令，以在代理程式安裝期間啟用記錄：

```
StorageSyncAgent.msi /l*v Installer.log
```

一旦安裝失敗，請檢閱 installer.log 以判斷原因。

## <a name="server-is-not-listed-under-registered-servers-in-the-azure-portal"></a>伺服器未列在 Azure 入口網站的 [已註冊的伺服器] 下
如果伺服器未列在儲存體同步服務的 [已註冊的伺服器] 下，請執行下列步驟：
1. 登入您要註冊的伺服器。
2. 開啟檔案總管，然後巡覽至儲存體同步代理程式安裝目錄 (預設位置是 `C:\Program Files\Azure\StorageSyncAgent`)。 
3. 執行 ServerRegistration.exe，然後遵循精靈向儲存體同步服務註冊伺服器。

## <a name="server-registration-displays-the-following-message-after-installing-the-azure-file-sync-agent-this-server-is-already-registered"></a>安裝 Azure 檔案同步代理程式之後，伺服器註冊會顯示下列訊息：「此伺服器已註冊」
![[伺服器註冊] 對話方塊的螢幕擷取畫面顯示 [此伺服器已註冊] 錯誤訊息](media/storage-sync-files-troubleshoot/server-registration-1.png)

如果先前已向儲存體同步服務註冊該伺服器，則會顯示此訊息。 若要向目前的儲存體同步服務取消註冊伺服器，再向新的儲存體同步服務註冊，請遵循步驟以[向 Azure 檔案同步取消註冊伺服器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)。

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
