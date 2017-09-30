---
title: "如何部署 Azure 檔案同步 (預覽) | Microsoft Docs"
description: "了解如何從頭到尾部署 Azure 檔案同步。"
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
ms.openlocfilehash: d9cf205cd3e7a8017efbe9078ff495c04f065374
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="how-to-deploy-azure-file-sync-preview"></a>如何部署 Azure 檔案同步 (預覽)
透過 Azure 檔案同步 (預覽)，您可以在內部部署或 Azure 中複寫共用，並在 Windows Server 上透過 SMB 或 NFS 共用來存取。 Azure 檔案同步適用於需要存取和修改離 Azure 資料中心很遠的位置 (例如分公司) 之資料的情況。 資料可以在多個 Windows Server 端點之間複寫，例如多個分公司之間。

強烈建議您先閱讀[規劃 Azure 檔案服務部署](storage-files-planning.md)和[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)，再遵循本指南中的步驟。

## <a name="prerequisites"></a>先決條件
* 儲存體帳戶和 Azure 檔案共用都位於您要部署 Azure 檔案同步的相同區域。如需詳細資訊，請參閱：
    - Azure 檔案共用的[區域可用性](storage-sync-files-planning.md#region-availability)、
    - [建立儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)以取得如何建立儲存體帳戶的逐步指示，和
    - [建立檔案共用](storage-how-to-create-file-share.md)以取得如何建立檔案共用的逐步指示。
* 至少有一個支援的 Windows Server 或 Windows Server 叢集，以與 Azure 檔案同步進行同步處理。如需支援之 Windows Server 版本的詳細資訊，請參閱[與 Windows Server 的互通性](storage-sync-files-planning.md#azure-file-sync-interoperability)。

## <a name="deploy-the-storage-sync-service"></a>部署儲存體同步服務 
儲存體同步服務是最上層的 Azure 資源，代表 Azure 檔案同步。若要部署儲存體同步服務，請瀏覽至 [Azure 入口網站](https://portal.azure.com/)，並搜尋 Azure 檔案同步。從搜尋結果中選取 [Azure 檔案同步 (預覽)] 之後，選取 [建立] 以快速開啟 [部署儲存體同步] 索引標籤。

產生的刀鋒視窗會要求下列資訊：

- **名稱**：儲存體同步服務的唯一名稱 (每一訂用帳戶)。
- **訂用帳戶**：要在其中建立儲存體同步服務的訂用帳戶。 視您組織的設定策略而定，您可能具有一或多個訂用帳戶的存取權限。 Azure 訂用帳戶是用於每個雲端服務 (例如 Azure 檔案服務) 計費的最基本容器。
- **資源群組**：資源群組是 Azure 資源的邏輯群組，例如儲存體帳戶或儲存體同步服務。 您可以為 Azure 檔案同步建立新的資源群組，或使用現有的資源群組 (建議使用資源群組做為容器，用來以邏輯方式隔離組織的資源，例如將 HR 資源或是特定專案的資源分組)。
- **位置**：您要在其中部署 Azure 檔案同步的區域。只有支援的區域會列在此清單中。

完成 [部署儲存體同步] 表單之後，請按一下 [建立] 以部署儲存體同步服務。

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>準備 Windows Server 以搭配 Azure 檔案同步使用
針對您要用以使用 Azure 檔案同步的每部伺服器 (包括容錯移轉叢集中的伺服器節點) 完成下列步驟：

針對您要搭配 Azure 檔案同步使用的每部伺服器 (包括容錯移轉叢集中的伺服器節點) 完成下列步驟：

1. 停用 Internet Explorer 增強式安全性設定。 此步驟只有初始伺服器註冊才需要，而且可以在伺服器註冊後重新啟用。
    1. 開啟 [伺服器管理員]。
    2. 按一下 [本機伺服器]：  
        ![伺服器管理員 UI 左側的 [本機伺服器]](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. 選取 [屬性] 子窗格上的 [IE 增強式安全性設定] 連結：  
        ![伺服器管理員 UI 中的 [IE 增強式安全性設定]](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. 針對 [Internet Explorer 增強式安全性設定] 快顯視窗中的 [系統管理員] 和 [使用者] 選取 [關閉]：  
        ![已選取 [關閉] 的 [Internet Explorer 增強式安全性設定] 快顯視窗](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. 請確定您至少執行 PowerShell 5.1.\*(Windows Server 2016 上預設為 PowerShell 5.1)。 您可以查看 $PSVersionTable 物件的 PSVersion 屬性值，確定執行的是否為 PowerShell 5.1.\*：

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - 如果您的 PSVersion 小於 5.1.\* (這大部分發生於 Windows Server 2012 R2 的安裝)，您可以下載並安裝 [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) 來輕鬆地升級。 適用於 Windows Server 2012 R2 下載和安裝的適當套件為 **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**。

3. [安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 一律建議使用最新版的 Azure PowerShell 模組。

## <a name="install-the-azure-file-sync-agent"></a>安裝 Azure 檔案同步代理程式
Azure 檔案同步代理程式是可下載的套件，可同步處理 Windows Server 與 Azure 檔案共用。 您可以從 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257)下載代理程式。 下載之後，在 MSI 套件上按兩下以啟動 Azure 檔案同步代理程式安裝。

> [!Important]  
> 如果您打算將 Azure 檔案同步搭配容錯移轉叢集使用，則必須在叢集中的每個節點上安裝 Azure 檔案同步代理程式。

Azure 檔案同步代理程式安裝套件應該會安裝地相當快速，而不會出現太多其他提示。 我們建議您採取下列動作︰
- 保留預設安裝路徑 `C:\Program Files\Azure\StorageSyncAgent`) 以簡化疑難排解和伺服器維護。
- 啟用 Microsoft Update 以將 Azure 檔案同步保持在最新狀態。 Azure 檔案同步代理程式的所有更新 (包括功能更新和 Hotfix) 都會從 Microsoft Update 進行。 我們一律建議採用最新的 Azure 檔案同步更新。如需詳細資訊，請參閱 [Azure 檔案同步更新原則](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)。

在 Azure 檔案同步代理程式安裝結束時，伺服器註冊 UI 會自動啟動。 請參閱下一節以了解如何向 Azure 檔案同步註冊此伺服器。

## <a name="register-windows-server-with-storage-sync-service"></a>向儲存體同步服務註冊 Windows Server
向儲存體同步服務註冊 Windows Server 會在您的伺服器 (或叢集) 與儲存體同步服務之間建立信任關係。 伺服器註冊 UI 應該會在 Azure 檔案同步代理程式安裝之後自動啟動，如果沒有，您可以手動從它的位置開啟：`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`。 伺服器註冊 UI 開啟之後，按一下 [登入] 以開始。

登入之後，系統會提示您輸入下列資訊：

![伺服器註冊 UI 的螢幕擷取畫面](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure 訂用帳戶**：包含儲存體同步服務的訂用帳戶 (請參閱上面的[部署儲存體同步服務](#deploy-the-storage-sync-service))。 
- **資源群組**：包含儲存體同步服務的資源群組。
- **儲存體同步服務**：您要註冊的目標儲存體同步服務名稱。

從下拉式清單選取了適當的資訊之後，按一下 [註冊] 以完成伺服器註冊。 在註冊過程中，系統會提示您額外再登入一次。

## <a name="create-a-sync-group"></a>建立同步群組
同步群組定義一組檔案的同步拓撲。 同步群組內的端點會彼此保持同步。 同步群組必須包含至少一個雲端端點 (代表 Azure 檔案共用) 和一個伺服器端點 (代表 Windows Server 上的路徑)。 若要建立同步群組，請瀏覽至 [Azure 入口網站](https://portal.azure.com/)中的儲存體同步服務，然後按一下 [+ 同步群組]：

![在 Azure 入口網站中建立新的同步群組](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

產生的窗格會要求您輸入下列資訊，以建立具有雲端端點的同步群組：

- **同步群組名稱**：要建立的同步群組名稱。 此名稱在儲存體同步服務中必須是唯一的，但可以是任何對您而言合理的名稱。
- **訂用帳戶**：您在上面的[部署儲存體同步服務](#deploy-the-storage-sync-service)中部署儲存體同步服務的訂用帳戶。
- **儲存體帳戶**：按一下 [選取儲存體帳戶] 將快速開啟一個額外窗格，讓您可以選取儲存體帳戶 (其中包含您要同步處理的 Azure 檔案共用)。
- **Azure 檔案共用**：您要同步處理的 Azure 檔案共用名稱。

若要新增伺服器端點，請瀏覽至新建立的同步群組，並按一下 [新增伺服器端點]。

![在 [同步群組] 窗格中新增新的伺服器端點](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

產生的 [新增伺服器端點] 窗格需要下列資訊以建立伺服器端點：

- **已註冊的伺服器**：要在其上建立伺服器端點的伺服器或叢集名稱。
- **路徑**：Windows Server 上要隨著同步群組同步的路徑。
- **雲端階層**：切換啟用或停用雲端階層的開關，可將不常使用或存取的檔案分層至 Azure 檔案服務。
- **磁碟區可用空間**：伺服器端點所在磁碟區要保留的可用空間數量。 例如，如果具有單一伺服器端點之磁碟區上的磁碟區可用空間設定為 50%，則大約有一半的資料量會分層至 Azure 檔案服務。 請注意，無論雲端階層是否啟用，您的 Azure 檔案共用在同步群組中一律會有完整的資料複本。

按一下 [建立] 以新增伺服器端點。 您的檔案現在會在 Azure 檔案共用和 Windows Server 之間保持同步。 

## <a name="next-steps"></a>後續步驟
- [新增/移除 Azure 檔案同步伺服器端點](storage-sync-files-server-endpoint.md)
- [向 Azure 檔案同步註冊/取消註冊伺服器](storage-sync-files-server-registration.md)
