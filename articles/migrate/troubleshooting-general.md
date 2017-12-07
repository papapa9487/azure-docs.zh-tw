---
title: "為 Azure Migrate 問題進行疑難排解 | Microsoft Docs"
description: "概括介紹 Azure Migrate 服務的已知問題以及常見錯誤的疑難排解訣竅。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 40faffa3f-1f44-4a72-94bc-457222ed7ac8
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 8c7c79a23ee09a7de35252d7819d1f0e5b1d98c5
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-migrate"></a>為 Azure Migrate 疑難排解

## <a name="troubleshoot-common-errors"></a>常見問題疑難排解

[Azure Migrate](migrate-overview.md) 會評估要移轉至 Azure 的內部部署工作負載。 本文可對 Azure Migrate 部署與使用方面的問題進行疑難排解。


**收集器無法使用我從入口網站複製的專案識別碼和金鑰連線到專案。**

請確定已複製並貼上正確的資訊。 若要進行疑難排解，請安裝 Microsoft Monitoring Agent (MMA)，如下所示：

1. 在收集器虛擬機器上，下載 [MMA](https://go.microsoft.com/fwlink/?LinkId=828603)。
2. 若要開始安裝，請按兩下下載的檔案。
3. 在設定的 [歡迎] 頁面中按 [下一步]。 在 [授權條款] 頁面上，按一下 [我同意] 以接受授權。
4. 在 [目的地資料夾] 中，保留或修改預設的安裝資料夾 > [下一步]。
5. 在 [代理程式安裝選項] 中，選取 [Azure Log Analytics (OMS)] > [下一步]。
6. 按一下 [新增] 以新增 Log Analytics 工作區。 貼上您複製的專案識別碼和金鑰。 然後按 [下一步] 。
7. 確認代理程式是否可連線至專案。 如果無法連線，請檢查設定。 如果代理程式可以連線，但是收集器無法連線，請連絡支援服務。


**錯誤 802：出現日期和時間同步處理錯誤。**

伺服器時鐘可能與目前的時間不同步，相差到五分鐘以上。 請變更收集器虛擬機器的時間，使 之與目前的時間相符，方法如下所示：

1. 在虛擬機器上開啟系統管理命令提示字元。
2. 若要檢查時區，請執行 w32tm /tz。
3. 若要同步時間，請執行 w32tm /resync。

**我的專案金鑰是以「==」符號結尾。這是被收集器編碼為其他英數字元。這是預期行為嗎？**

是的，每個專案金鑰都是以「==」結尾。 收集器會先將專案金鑰加密再處理。

**磁碟和網路介面卡的效能資料顯示為零**

如果 vCenter 伺服器上的統計資料設定層級設為小於 3，可能會發生這種情況。 在層級 3 以上，vCenter 會儲存運算、儲存體和網路的虛擬機器效能歷程記錄。 層級 3 以下的話，vCenter 不會儲存儲存體和網路資料，只會儲存 CPU 和記憶體資料。 在此情況下，Azure Migrate 的效能資料會顯示為零，而且 Azure Migrate 會根據從內部部署機器收集的中繼資料來建議磁碟和網路的規模大小。

若要啟用磁碟和網路效能資料的收集功能，請將統計資料設定層級變更為 3。 然後，等待至少一天以探索並評估您的環境。 

**我已經安裝代理程式，並使用相依性視覺化建立群組。現在，在容錯移轉後，機器會顯示「安裝代理程式」動作，而不是「檢視相依性」**
* 在已規劃或未規劃的容錯移轉後，內部部署機器都會關閉，而且對等的機器會在 Azure 中啟動。 這些機器會取得不同的 MAC 位址。 根據使用者是否選擇保留內部部署 IP 位址，這些機器可能會取得不同的 IP 位址。 如果 MAC 及 IP 位址不同，Azure Migrate 不會使內部部署機器與任何服務對應相依性資料產生關聯，而會要求使用者安裝代理程式，而不是檢視相依性。
* 在測試容錯移轉後，內部部署機器如預期保持開啟。 在 Azure 中啟動的對等機器會取得不同的 MAC 位址，而且可能會取得不同的 IP 位址。 除非使用者封鎖這些機器傳出的 OMS 流量，否則 Azure Migrate 不會使內部部署機器與任何服務對應相依性資料產生關聯，而會要求使用者安裝代理程式，而不是檢視相依性。


## <a name="troubleshoot-readiness-issues"></a>整備問題的疑難排解

**問題** | 修正
--- | ---
不支援的開機類型 | 執行移轉之前，請變更為 BIOS。
磁碟計數超過限制 | 移轉之前從機器移除未使用的磁碟。
磁碟大小超過限制 | 在移轉前將磁碟壓縮為小於 4 TB。 
指定的位置沒有磁碟可用 | 在移轉之前，請確定磁碟已在目標位置。
沒有磁碟可當作指定的備援 | 磁碟應該使用評估設定 (預設為 LRS) 中定義的備援儲存體類型。
由於發生內部錯誤，因此無法判斷磁碟適合性 | 嘗試建立群組的新評估。 
找不到具備所需核心和記憶體的虛擬機器 | Azure 找不到適當的虛擬機器類型。 在移轉之前，請減少內部部署電腦的記憶體和核心數目。 
一個或多個不適合的磁碟。 | 執行移轉之前，確定內部部署磁碟是 4 TB 以下。
一個或多個不適用的網路介面卡。 | 在移轉之前，從機器移除未使用的網路介面卡。
由於發生內部錯誤，無法判斷虛擬機器的適合性。 | 嘗試建立群組的新評估。 
由於發生內部錯誤，因此無法判斷一個或多個磁碟的適用性。 | 嘗試建立群組的新評估。
由於發生內部錯誤，因此無法判斷一個或多個網路介面卡的適用性。 | 嘗試建立群組的新評估。
找不到所需儲存體效能的虛擬機器。 | 機器需要的儲存體效能 (IOPS/輸送量) 超出 Azure VM 支援。 在移轉之前，降低機器的儲存體需求。
找不到所需網路效能的虛擬機器。 | 機器需要的網路效能 (傳入/傳出) 超出 Azure VM 支援。 減少機器的網路需求。 
找不到指定定價層的虛擬機器。 | 檢查定價層設定。 
找不到指定位置的虛擬機器。 | 在移轉之前，使用不同的目標位置。
Linux OS 支援問題 | 對於這些支援的[作業系統](../virtual-machines/linux/endorsed-distros.md)，請確定您執行的是 64 位元版本。
Windows 作業系統支援問題 | 請確定您執行支援的作業系統。 [深入了解](concepts-assessment-calculation.md#azure-suitability-analysis)
作業系統不明。 | 請檢查 vCenter 中指定的作業系統正確，並重複進行探索程序。
需要 Visual Studio 訂用帳戶。 | Visual Studio (MSDN) 訂用帳戶只支援 Windows 用戶端作業系統。


## <a name="collect-logs"></a>收集記錄

**如何在收集器 VM 上收集記錄？**

記錄預設為啟用。 記錄的位置如下所示：

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

若要收集 Windows 的事件追蹤，請執行下列作業：

1. 在收集器虛擬機器上，開啟 PowerShell 命令視窗。
2. 執行 **Get-EventLog -LogName Application | export-csv eventlog.csv**。

**如何收集入口網站網路流量記錄？**

1. 開啟瀏覽器，然後瀏覽並登入[入口網站](https://portal.azure.com)。
2. 按 F12 開啟 Developer Tools。 如有需要，請清除 [清除瀏覽的項目] 設定。
3. 按一下 [網路] 索引標籤，並開始擷取網路流量：
 - 在 Chrome 中，選取 [保留記錄]。 記錄應該會自動啟動。 紅色圓圈表示正在擷取流量。 如果未出現，請按一下黑色圓圈啟動
 - 在 Edge/IE 中，記錄應該會自動啟動。 如果未啟動，請按一下綠色播放按鈕。
4. 嘗試重現錯誤。
5. 您在記錄時發生錯誤之後，請停止錄製，並儲存一份記錄的活動：
 - 在 Chrome 中，以滑鼠右鍵按一下 [內容另存為 HAR]。 這會壓縮記錄並匯出為 .har 檔案。
 - 在 Edge/IE 中，按一下 [匯出擷取流量] 圖示。 這會壓縮並匯出記錄。
6. 瀏覽至 [主控台] 索引標籤，檢查是否有任何警告或錯誤。 若要儲存主控台記錄：
 - 在 Chrome 中，以滑鼠右鍵按一下主控台記錄的任何位置。 選取 [另存新檔]，以匯出並壓縮記錄。
 - 在 Edge/IE 中，以滑鼠右鍵按一下錯誤，然後選取 [全部複製]。 
7. 關閉 Developer Tools。
 



