---
title: "部署 Azure 檔案同步 (預覽) | Microsoft Docs"
description: "了解部署 Azure 檔案同步的完整程序。"
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
ms.openlocfilehash: 42a0e7a3816e0f1d96951feac210e5770add4fe1
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="deploy-azure-file-sync-preview"></a>部署 Azure 檔案同步 (預覽)
使用 Azure 檔案同步 (預覽版)，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的靈活度、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

強烈建議您先閱讀[規劃 Azure 檔案服務部署](storage-files-planning.md)和[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)，再完成本文章中描述的步驟。

## <a name="prerequisites"></a>必要條件
* 位於和您要部署 Azure 檔案同步相同之區域中的 Azure 儲存體帳戶和 Azure 檔案共用。如需詳細資訊，請參閱：
    - Azure 檔案同步的[區域可用性](storage-sync-files-planning.md#region-availability)。
    - 如需如何建立儲存體帳戶的逐步說明，請參閱[建立儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
    - [建立檔案共用](storage-how-to-create-file-share.md)以取得如何建立檔案共用的逐步說明。
* 至少有一個 Windows Server 或 Windows Server 叢集的受支援執行個體，以與 Azure 檔案同步進行同步處理。如需 Windows Server 受支援版本的詳細資訊，請參閱[與 Windows Server 的互通性](storage-sync-files-planning.md#azure-file-sync-interoperability)。

## <a name="deploy-the-storage-sync-service"></a>部署儲存體同步服務 
儲存體同步服務是 Azure 檔案同步的最上層 Azure 資源。若要部署儲存體同步服務，請移至 [Azure 入口網站](https://portal.azure.com/)，並搜尋 Azure 檔案同步。在搜尋結果中，選取 [Azure 檔案同步 (預覽)]，然後選取 [建立] 以開啟 [部署儲存體同步] 索引標籤。

在開啟的窗格中，輸入下列資訊：

- **名稱**：儲存體同步服務的唯一名稱 (每一訂用帳戶)。
- **訂用帳戶**：您要在其中建立儲存體同步服務的訂用帳戶。 視您組織的設定策略而定，您可能具有一或多個訂用帳戶的存取權限。 Azure 訂用帳戶是針對每個雲端服務 (例如 Azure 檔案服務) 計費的最基本容器。
- **資源群組**：資源群組是 Azure 資源的邏輯群組，例如儲存體帳戶或儲存體同步服務。 針對 Azure 檔案同步，您可以建立新的資源群組或使用現有的資源群組。(建議使用資源群組作為容器，來以邏輯方式為您的組織隔離資源，例如將 HR 資源或適用於特定專案的資源組成群組)。
- **位置**：您要在其中部署 Azure 檔案同步的區域。只有支援的區域會列在此清單中。

完成時，請選取 [建立] 來部署儲存體同步服務。

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>準備 Windows Server 以搭配 Azure 檔案同步使用
針對您要搭配 Azure 檔案同步使用的每部伺服器 (包括容錯移轉叢集中的伺服器節點)，請完成下列步驟：

1. 停用 [Internet Explorer 增強式安全性設定]。 此動作只需要在初始伺服器註冊時執行。 您可以在註冊伺服器後重新啟用它。
    1. 開啟 [伺服器管理員]。
    2. 按一下 [本機伺服器]：  
        ![位於伺服器管理員 UI 左側的 [本機伺服器]](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. 選取 [屬性] 子窗格上的 [IE 增強式安全性設定] 連結。  
        ![位於伺服器管理員 UI 中的 [IE 增強式安全性設定] 窗格](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. 在 [Internet Explorer 增強式安全性設定] 對話方塊中，針對 [系統管理員] 和 [使用者] 選取 [關閉]：  
        ![已選取 [關閉] 的 [Internet Explorer 增強式安全性設定] 快顯視窗](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. 請確定您至少執行 PowerShell 5.1.\*(Windows Server 2016 上預設為 PowerShell 5.1)。 您可以查看 **$PSVersionTable** 物件的 **PSVersion** 屬性值，確定執行的是否為 PowerShell 5.1.\*：

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    如果您的 PSVersion 值小於 5.1.\* (這會發生於大部分的 Windows Server 2012 R2 安裝)，您可以下載並安裝 [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) \(英文\) 來輕鬆地升級。 適用於 Windows Server 2012 R2 下載和安裝的適當套件為 **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**。

3. [安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 建議您使用最新版的 Azure PowerShell 模組。

## <a name="install-the-azure-file-sync-agent"></a>安裝 Azure 檔案同步代理程式
Azure 檔案同步代理程式是可下載的套件，可讓 Windows Server 能夠和 Azure 檔案共用進行同步處理。 您可以從 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257) \(英文\) 下載該代理程式。 下載之後，請按兩下 MSI 套件以啟動 Azure 檔案同步代理程式安裝。

> [!Important]  
> 如果您打算將 Azure 檔案同步搭配容錯移轉叢集使用，則必須在叢集中的每個節點上安裝 Azure 檔案同步代理程式。

Azure 檔案同步代理程式安裝套件的安裝速度應該相當快速，而不會出現太多其他提示。 建議您採取下列動作︰
- 保留預設的安裝路徑 (C:\Program Files\Azure\StorageSyncAgent)，以簡化疑難排解和伺服器維護。
- 啟用 Microsoft Update 以將 Azure 檔案同步保持在最新狀態。 Azure 檔案同步代理程式的所有更新 (包括功能更新和 Hotfix) 都會從 Microsoft Update 進行。 建議安裝最新的 Azure 檔案同步更新。如需詳細資訊，請參閱 [Azure 檔案同步更新原則](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)。

當 Azure 檔案同步代理程式安裝結束時，伺服器註冊 UI 會自動開啟。 請參閱下一節以了解如何向 Azure 檔案同步註冊此伺服器。

## <a name="register-windows-server-with-storage-sync-service"></a>向儲存體同步服務註冊 Windows Server
向儲存體同步服務註冊 Windows Server，會在您的伺服器 (或叢集) 與儲存體同步服務之間建立信任關係。 安裝 Azure 檔案同步代理程式之後，伺服器註冊 UI 應該會自動開啟。 如果沒有，您可以從其檔案位置手動開啟它：C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe。 伺服器註冊 UI 開啟時，請選取 [登入] 以開始。

登入之後，系統會提示您輸入下列資訊：

![伺服器註冊 UI 的螢幕擷取畫面](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure 訂用帳戶**：包含儲存體同步服務的訂用帳戶 (請參閱[部署儲存體同步服務](#deploy-the-storage-sync-service))。 
- **資源群組**：包含儲存體同步服務的資源群組。
- **儲存體同步服務**：您要註冊的目標儲存體同步服務名稱。

選取適當的資訊之後，請選取 [註冊] 以完成伺服器註冊。 在註冊過程中，系統會提示您額外再登入一次。

## <a name="create-a-sync-group"></a>建立同步群組
同步群組定義一組檔案的同步拓撲。 同步群組內的端點會與彼此保持同步。 同步群組必須包含至少一個雲端端點 (代表 Azure 檔案共用) 和一個伺服器端點 (代表 Windows Server 上的路徑)。 若要建立同步群組，請在 [Azure 入口網站](https://portal.azure.com/)中移至您的儲存體同步服務，然後選取 [+ 同步群組]：

![在 Azure 入口網站中建立新的同步群組](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

在開啟的窗格中，輸入下列資訊以建立具有雲端端點的同步群組：

- **同步群組名稱**：要建立的同步群組名稱。 此名稱在儲存體同步服務中必須是唯一的，但可以是任何對您而言合理的名稱。
- **訂用帳戶**：您在[部署儲存體同步服務](#deploy-the-storage-sync-service)中部署儲存體同步服務的訂用帳戶。
- **儲存體帳戶**：如果您選取 [選取儲存體帳戶]，則會顯示另一個窗格，您可在其中選取具有您想要同步之 Azure 檔案共用的儲存體帳戶。
- **Azure 檔案共用**：您要同步處理的 Azure 檔案共用名稱。

若要新增伺服器端點，請移至新建立的同步群組，然後選取 [新增伺服器端點]。

![在 [同步群組] 窗格中新增新的伺服器端點](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

在 [新增伺服器端點] 窗格中，輸入下列資訊以建立伺服器端點：

- **已註冊的伺服器**：您要建立伺服器端點的伺服器或叢集名稱。
- **路徑**：要隨著同步群組同步處理的 Windows Server 路徑。
- **雲端階層處理**：啟用或停用雲端階層處理的開關。 透過雲端階層處理，可將不常使用或存取的檔案分層處理至 Azure 檔案服務。
- **磁碟區可用空間**：在伺服器端點所在磁碟區上要保留的可用空間數量。 例如，如果具有單一伺服器端點之磁碟區上的磁碟區可用空間是設定為 50%，則大約有一半的資料量會分層處理至 Azure 檔案服務。 無論雲端階層處理是否啟用，您的 Azure 檔案共用在同步群組中一律會有完整的資料複本。

若要新增伺服器端點，請選取 [建立]。 您的檔案現在會在 Azure 檔案共用和 Windows Server 之間保持同步。 

> [!Important]  
> 您可以對同步群組中的任何雲端端點或伺服器端點進行變更，您的檔案將會與同步群組中的其他端點同步。 如果直接對雲端端點 (Azure 檔案共用) 進行變更，則必須先由 Azure 檔案同步變更偵測作業探索到該變更。 針對雲端端點的變更偵測作業，每隔 24 小時才會起始一次。 如需詳細資訊，請參閱 [Azure 檔案服務常見問題集](storage-files-faq.md#afs-change-detection)。

## <a name="next-steps"></a>後續步驟
- [新增或移除 Azure 檔案同步伺服器端點](storage-sync-files-server-endpoint.md)
- [向 Azure 檔案同步註冊或取消註冊伺服器](storage-sync-files-server-registration.md)
