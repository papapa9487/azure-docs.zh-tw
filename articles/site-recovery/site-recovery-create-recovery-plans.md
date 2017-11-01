---
title: "在 Azure Site Recovery 中建立容錯移轉和復原的復原計劃 | Microsoft Docs"
description: "描述如何建立及自訂 Azure Site Recovery 中的復原方案，以容錯移轉和復原 VM 與實體伺服器"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: raynew
ms.openlocfilehash: 202e0ac8be36e9156ec16fadc1b722f4eb3d1432
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2017
---
# <a name="create-recovery-plans"></a>建立復原方案


本文提供在 [Azure Site Recovery](site-recovery-overview.md) 中建立和自訂復原方案的相關資訊。

在這篇文章下方或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

 建立復原方案可執行下列作業：

* 定義會一起容錯移轉並啟動的機器群組。
* 將機器分到同一個復原計畫群組，藉以建立機器間的相依性。 例如，若要執行容錯移轉，並帶出特定的應用程式，應將該應用程式的所有 VM 分到相同的復原計畫群組。
* 執行容錯移轉。 您可以透過復原方案執行測試，以及執行規劃或未規劃的容錯移轉。


## <a name="create-a-recovery-plan"></a>建立復原計畫

1. 按一下 [復原計畫] > [建立復原計畫]。
   指定復原方案的名稱，以及來源和目標伺服器。 來源位置必須有已啟用容錯移轉和復原功能的虛擬機器。

    - 針對 VMM 複寫至 VMM，請選取 [來源類型]  >  [VMM]，然後選取來源和目標 VMM 伺服器。 按一下 [Hyper-V]，查看受保護的雲端。
    - 若為 VMM 至 Azure，請選取 [來源類型] > [VMM]。  選取來源 VMM 伺服器及 [Azure] 做為目標。
    - 若為 Hyper-V 複寫至 Azure (無 VMM)，請選取 [來源類型] > [Hyper-V 網站]。 選取該網站做為來源，並選取 [Azure]做為目標。
    - 若為 VMware VM 或實體內部部署伺服器至 Azure，請選取組態伺服器做為來源，並選取 [Azure] 做為目標。
    - 對於 Azure 至 Azure 復原計劃，請選取 Azure 區域作為來源，以及次要 Azure 區域作為目標。 次要 Azure 區域只是虛擬機器受到保護的部分。
2. 在 [選取虛擬機器] 中，選取您要在復原計畫中新增至預設群組 (Group 1) 的虛擬機器 (或複寫群組)。

## <a name="customize-and-extend-recovery-plans"></a>自訂和擴充復原計畫

您可以自訂和擴充復原計畫：

- **新增群組**—將額外的復原計畫群組 (最多七個) 新增至預設群組，然後將更多電腦或複寫群組新增至這些復原計畫群組。 群組會依您加入的順序加以編號。 虛擬機器或複寫群組只能包含在一個復原計畫群組中。
- 新增手動動作 — 您可以新增手動動作，在建立復原方案群組之前或之後執行。 執行復原計畫時，它會在您插入手動動作之處停止。 對話方塊會提示您指定手動動作已完成。
- 新增指令碼 — 您可以新增指令碼，在建立復原計畫群組之前或之後執行。 新增指令碼後，群組中會加入新的動作集。 例如，系統會為 Group 1 建立一組前置步驟，並取名為 Group 1: Pre-steps。 所有前置步驟都會列於此動作集中。 若您已部署 VMM 伺服器，則只能在主要網站上新增指令碼。
- **新增 Azure Runbook**— 您可以使用 Azure runbook 擴充復原計畫。 例如，自動化工作或建立單步驟復原。 [深入了解](site-recovery-runbook-automation.md)。

## <a name="add-scripts"></a>新增指令碼

在您的復原計畫中，您可以使用 PowerShell 指令碼。

 - 請確認指令碼使用的是 Try-Catch 區塊，以便能適當地處理例外狀況。
    - 如果在指令碼中出現例外狀況，指令碼會隨即停止執行，而工作也會顯示為失敗。
    - 一旦發生錯誤，指令碼的剩餘部分將不會執行。
    - 如果在執行未規劃的容錯移轉時發生錯誤，復原計畫將會繼續執行。
    - 如果在執行未規劃的容錯移轉時發生錯誤，復原計畫將會停止。 您必須修正指令碼，確認其能如預期執行，然後再次執行復原方案。
- Write-Host 命令不會在復原方案指令碼中正常運作，指令碼將會失敗。 若要建立輸出，建立接著會執行主要指令碼的 proxy 指令碼。 請確定所有的輸出會使用 >> 命令經由管道輸出。
  * 如果指令碼未在 600 秒內傳回則會逾時。
  * 如果有任何資料寫入至 STDERR，則該指令碼將分類為失敗。 此資訊會顯示在指令碼的執行詳細資料中。

如果您在部署中使用 VMM：

* 復原方案中的指令碼會依據 VMM 服務帳戶中的內容執行。 請確定此帳戶具有指令碼所在的遠端共用讀取權限。 測試指令碼執行 VMM 服務帳戶權限層級。
* 會在 Windows PowerShell 模組中傳遞 VMM Cmdlet。 安裝 VMM 主控台時，會一併安裝模組。 它可以使用下列指令碼中的命令載入到您的指令碼︰
   - Import-Module -Name virtualmachinemanager。 [深入了解](https://technet.microsoft.com/library/hh875013.aspx)。
* 確認您的 VMM 部署中至少有一部程式庫伺服器。 根據預設，VMM 伺服器的程式庫共用路徑位於本機的 VMM 伺服器上，資料夾名稱為 MSCVMMLibrary。
    * 如果您的程式庫共用路徑位於遠端 (或是位於本機上，但未與 MSCVMMLibrary 共用)，請依以下所示設為共用 (以 \\libserver2.contoso.com\share\ 為例)：
      * 開啟登錄編輯程式，並瀏覽至 **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**。
      * 編輯值 **ScriptLibraryPath**，並將其設為 \\libserver2.contoso.com\share\. 指定完整的 FQDN。 提供共用位置的權限。 請注意，這是共用的根節點。 **若要檢查此情形，您可以在 VMM 的根節點中瀏覽程式庫。開啟的路徑會是路徑的根目錄，就是您需要在變數中使用的路徑**。
      * 請確定您使用與 VMM 服務帳戶具有相同權限的使用者帳戶來測試指令碼。 這會檢查該獨立測試指令碼以它們在復原計畫中的相同方式執行。 在 VMM 伺服器上，設定要略過的執行原則，如下所示：
        * 以較高的權限開啟 **64 位元 Windows PowerShell** 主控台。
        * 類型： **Set-executionpolicy bypass**。 [深入了解](https://technet.microsoft.com/library/ee176961.aspx)。

> [!IMPORTANT]
> 只有在 64 位元 PowerShell 上，您才應該將執行原則設為 [略過]。 如果您已針對 32 位元 PowerShell 設定它，則不會執行指令碼。

## <a name="add-a-script-or-manual-action-to-a-plan"></a>將指令碼或手動動作新增至方案

將 VM 或複寫群組新增至預設的復原計畫群組並建立計畫後，可將指令碼新增至復原計畫群組。

1. 開啟復原計畫。
2. 按一下 [步驟] 清單中的任意項目，然後按一下 [指令碼] 或 [手動動作]。
3. 指定要在已選取項目之前或之後新增指令碼或動作。 使用 [上移] 和 [下移] 按鈕，上下移動指令碼的位置。
4. 如果您新增 VMM 指令碼，請選取 [容錯移轉至 VMM 指令碼]。 在**指令碼路徑**中，輸入要共用的相對路徑。 在下列 VMM 範例中，您指定路徑︰**\RPScripts\RPScript.PS1**。
5. 如果新增 Azure 自動化 Runbook，請指定 Runbook 所在的 [Azure 自動化帳戶]，並選取適當的 [Azure Runbook 指令碼]。
6. 執行復原方案容錯移轉，以確保指令碼可以正常運作。


### <a name="add-a-vmm-script"></a>新增 VMM 指令碼

如果您有 VMM 來源站台，可在 VMM 伺服器上建立指令碼，並將其納入您的復原方案。

1. 在程式庫共用中建立新的資料夾。 例如，\<VMMServerName>\MSSCVMMLibrary\RPScripts。 將資料夾放在來源和目標 VMM 伺服器上。
2. 建立指令碼 (例如 RPScript)，並檢查其能否如預期運作。
3. 將指令碼放在來源和目標 VMM 伺服器的 \<VMMServerName>\MSSCVMMLibrary 位置。


## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-failover.md) 如何執行容錯移轉。
