---
title: "如何在 Microsoft Azure 中使用 PerfInsights | Microsoft Docs"
description: "了解如何使用 PerfInsights 針對 Windows VM 效能問題進行疑難排解。"
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: f22bd42302b96118dba0d4e5e387c6798a0b8777
ms.contentlocale: zh-tw
ms.lasthandoff: 08/12/2017

---
# <a name="how-to-use-perfinsights"></a>如何使用 PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) 是自動化的指令碼，它收集有用的診斷資訊、執行 I/O 壓力負載，並提供分析報表以協助針對 Microsoft Azure 中的 Windows VM 效能問題進行疑難排解。 

我們建議您先執行此指令碼， 再開啟 Microsoft 支援票證處理 VM 效能問題。

## <a name="supported-troubleshooting-scenarios"></a>支援的疑難排解案例

PerfInsights 可以收集並分析合併到唯一案例的多種資訊。

### <a name="collect-disk-configuration"></a>收集磁碟設定 

此案例會收集磁碟設定和其他重要資訊，包括下列項目：

-   事件記錄檔

-   所有傳入和傳出連線的網路狀態

-   網路和防火牆組態設定

-   目前正在系統上執行之所有應用程式的工作清單

-   虛擬機器 (VM) 的 msinfo32 所建立的資訊檔案

-   Microsoft SQL Server 資料庫組態設定 (如果 VM 被識別為執行 SQL Server 的伺服器)

-   儲存體可靠性計數器

-   重要的 Windows Hotfix

-   已安裝的篩選器驅動程式

這是被動的資訊集合，應該不會影響系統。 

>[!Note]
>下列每一個案例都會自動包含此案例。

### <a name="benchmarkstorage-performance-test"></a>基準測試/儲存體效能測試

此案例會為連接至 VM 的所有磁碟機執行 [diskspd](https://github.com/Microsoft/diskspd) 基準測試 (IOPS 和 MBPS)。 

> [!Note]
> 此案例會影響系統，所以不應該在實際的生產系統上執行。 如有必要，請在專用的維護視窗中執行此案例，以免發生任何問題。 因追蹤或基準測試所增加的工作負載，可能會對 VM 的效能造成負面影響。
>

### <a name="general-vm-slow-analysis"></a>一般 VM Slow 分析 

此案例會使用 Generalcounters.txt 檔案中指定的計數器來執行[效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)追蹤。 如果 VM 被識別為執行 SQL Server 的伺服器，它會使用 Sqlcounters.txt 檔中找到的計數器執行效能計數器追蹤。 它也包含效能診斷資料。

### <a name="vm-slow-analysis-and-benchmark"></a>VM Slow 分析和基準測試

此案例會執行[效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)追蹤，後面接著 [diskspd](https://github.com/Microsoft/diskspd) 基準測試。 

> [!Note]
> 此案例會影響系統，所以不應該在實際的生產系統上執行。 如有必要，請在專用的維護視窗中執行此案例，以免發生任何問題。 因追蹤或基準測試所增加的工作負載，可能會對 VM 的效能造成負面影響。
>

### <a name="azure-files-analysis"></a>Azure 檔案分析 

此案例會同時執行特殊的效能計數器擷取與網路追蹤。 擷取包含所有的「SMB 用戶端共用」計數器。 以下是屬於擷取的一些重要 SMB 用戶端共用效能計數器：

| **類型**     | **SMB 用戶端共用計數器** |
|--------------|-------------------------------|
| IOPS         | 資料要求/秒             |
|              | 讀取要求/秒             |
|              | 寫入要求/秒            |
| 延遲      | Avg. 秒/資料要求         |
|              | Avg. 秒/讀取                 |
|              | Avg. 秒/寫入                |
| IO 大小      | Avg. 位元組/資料要求       |
|              | Avg. 位元組/讀取               |
|              | Avg. 位元組/寫入              |
| 輸送量   | 資料位元組/秒                |
|              | 讀取位元組/秒                |
|              | 寫入位元組/秒               |
| 佇列長度 | Avg. 讀取佇列長度        |
|              | Avg. 寫入佇列長度       |
|              | Avg. 資料佇列長度        |

### <a name="custom-configuration"></a>自訂組態 

當您執行自訂設定時，您是以平行方式執行所有的追蹤 (效能診斷、效能計數器、xperf、網路、storport)，視您選取多少不同的追蹤而定。 如已選取，此工具會在完成追蹤之後，執行 diskspd 基準測試。 

> [!Note]
> 此案例會影響系統，所以不應該在實際的生產系統上執行。 如有必要，請在專用的維護視窗中執行此案例，以免發生任何問題。 因追蹤或基準測試所增加的工作負載，可能會對 VM 的效能造成負面影響。
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>指令碼收集何種資訊？

收集的資訊包括 Windows VM、磁碟或儲存集區設定、效能計數器、記錄檔和各種追蹤，視所用的效能案例而定：

|收集的資料                              |  |  | 效能測試案例 |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | 收集磁碟設定 | 基準測試/儲存體效能測試 | 一般 VM Slow 分析 | VM Slow 分析和基準測試 | Azure 檔案分析 | 自訂組態 |
| 來自事件記錄檔的資訊      | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 系統資訊               | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 磁碟區對應                       | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 磁碟對應                         | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 正在執行的工作                    | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 儲存體可靠性計數器     | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 儲存體資訊              | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| Fsutil 輸出                    | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 篩選器驅動程式資訊               | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| Netstat 輸出                   | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 網路組態            | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 防火牆設定           | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| SQL Server 設定         | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 效能診斷追蹤 * |                            |                                    | 是                      |                                |                      | 是                  |
| 效能計數器追蹤 **     |                            |                                    |                          |                                |                      | 是                  |
| SMB 計數器追蹤 **             |                            |                                    |                          |                                | 是                  |                      |
| SQL Server 計數器追蹤 **      |                            |                                    |                          |                                |                      | 是                  |
| XPerf 追蹤                      |                            |                                    |                          |                                |                      | 是                  |
| StorPort 追蹤                   |                            |                                    |                          |                                |                      | 是                  |
| 網路追蹤                    |                            |                                    |                          |                                | 是                  | 是                  |
| Diskspd 基準追蹤 ***      |                            | 是                                |                          | 是                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>效能診斷追蹤 (*)

在背景中執行規則引擎來收集資料並診斷進行中的效能問題。 目前支援下列規則：

- HighCpuUsage 規則：偵測高 CPU 使用量時段，並顯示在這些時段內前幾名的 CPU 使用量取用者。
- HighDiskUsage 規則：偵測實體磁碟的高磁碟使用量時段，並顯示在這些時段內前幾名的磁碟使用量取用者。
- HighResolutionDiskMetric 規則： 顯示每部實體磁碟每 50 毫秒的 IOPS、輸送量和 IO 延遲計量。 它有助於快速找出磁碟節流時段。
- HighMemoryUsage 規則：偵測高記憶體使用量時段，並顯示在這些時段內前幾名的記憶體使用量取用者。

> [!NOTE] 
> 目前支援包含 .NET Framework 3.5 或更新版本的 Windows 版本。

### <a name="performance-counter-trace-"></a>效能計數器追蹤 (**)

收集下列效能計數器︰

- \Process、\Processor、\Memory、\Thread、\PhysicalDisk、\LogicalDisk
- \Cache\Dirty Pages、\Cache\Lazy Write Flushes/sec、\Server\Pool Nonpaged、Failures、\Server\Pool Paged Failures
- \Network Interface、\IPv4\Datagrams、\IPv6\Datagrams、\TCPv4\Segments、\TCPv6\Segments、\Network Adapter、\WFPv4\Packets、\WFPv6\Packets、\UDPv4\Datagrams、\UDPv6\Datagrams、\TCPv4\Connection、\TCPv6\Connection、\Network QoS Policy\Packets、\Per Processor Network Interface Card Activity、\Microsoft Winsock BSP 下的已選取計數器

#### <a name="for-sql-server-instances"></a>SQL Server 執行個體
- \SQL Server:Buffer Manager、\SQLServer:Resource Pool Stats、\SQLServer:SQL Statistics\
- \SQLServer:Locks、\SQLServer:General、Statistics
- \SQLServer:Access 方法

#### <a name="for-azure-files"></a>Azure 檔案
\SMB 用戶端共用

### <a name="diskspd-benchmark-trace-"></a>Diskspd 基準追蹤 (***)
Diskspd IO 工作負載測試 [OS 磁碟 (寫入) 和集區磁碟 (讀取/寫入)]

## <a name="run-the-perfinsights-on-your-vm"></a>在 VM 上執行 PerfInsights

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>執行指令碼之前應該知道什麼？ 

**指令碼需求**

1.  此指令碼必須在發生效能問題的 VM 上執行。 

2.  支援下列作業系統：Windows Server 2008 R2、2012、2012 R2、2016、Windows 8.1 和 Windows 10。

**當您在實際執行的 VM 上執行指令碼時可能發生的問題：**

1.  指令碼和使用 XPerf 或 DiskSpd 設定的「基準」或「自訂」案例一起使用時，可能會對 VM 效能產生不良的影響。 當您在生產環境中執行指令碼時，請小心謹慎。

2.  當您一起使用指令碼和使用 DiskSpd 設定的「基準」或「自訂」案例時，請確定測試磁碟的 I/O 工作負載不受其他背景活動干擾。

3.  根據預設，指令碼會使用暫存磁碟機收集資料。 如果追蹤長時間保持啟用，則收集的資料量會有所影響。 這會降低暫存磁碟的可用空間，從而影響依賴此磁碟機的任何應用程式。

### <a name="how-do-i-run-perfinsights"></a>如何執行 PerfInsights？ 

若要執行指令碼，請遵循下列步驟：

1. 下載 [PerfInsights.zip](http://aka.ms/perfinsightsdownload)。

2. 解除封鎖 PerfInsights.zip 檔案。 若要這樣做，請以滑鼠右鍵按一下 PerfInsights.zip 檔案，選取 [屬性]。 在 [一般] 索引標籤上，選取 [解除封鎖]，然後選取 [確定]。 如此可確保指令碼在執行時不會出現任何額外的安全性提示。  

    ![解壓縮 zip 檔案](media/how-to-use-perfInsights/unlock-file.png)

3.  將 PerfInsights.zip 檔案解壓縮至暫存磁碟機 (通常預設為 D 磁碟機)。 壓縮的檔案應該包含下列檔案和資料夾：

    ![zip 資料夾中的檔案](media/how-to-use-perfInsights/file-folder.png)

4.  以系統管理員身分開啟 Windows PowerShell，然後執行 PerfInsights.ps1 指令碼。

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    如果系統要求您確認是否想要變更執行原則，您可能必須輸入 "y"。

    在 [免責聲明] 對話方塊中，您可以選擇與 Microsoft 支援服務共用診斷資訊。 您也必須同意授權合約才能繼續。 確認選項後按一下 [執行指令碼]。

    ![[免責聲明] 方塊](media/how-to-use-perfInsights/disclaimer.png)

5.  如可用，請在執行指令碼時，提交案例編號 (這會用於統計資料)。 然後按 [下一步] 。
    
    ![輸入支援識別碼](media/how-to-use-perfInsights/enter-support-number.png)

6.  選取暫存磁碟機。 指令碼可以自動偵測磁碟機的磁碟機代號。 如果在這個階段發生任何問題，系統可能會提示您選取磁碟機 (預設磁碟機 D)。 產生的記錄檔會儲存在 log\_collection 資料夾。 在您輸入或接受磁碟機代號後，按一下 [確定]。

    ![輸入磁碟機](media/how-to-use-perfInsights/enter-drive.png)

7.  從提供的清單中選取疑難排解案例。

       ![選取支援案例](media/how-to-use-perfInsights/select-scenarios.png)

8.  您也可以執行 PerfInsights 不顯示 UI。

    下列命令會執行「一般 VM Slow 分析」疑難排解案例，但 30 秒內無 UI 提示或擷取資料。 它會提示您同意與步驟 4 中所述的相同免責聲明和授權條款。

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    如果您希望以無訊息模式執行 PerfInsights，請使用 **-AcceptDisclaimerAndShareDiagnostics** 參數。 例如，使用下列命令：

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>如何在執行指令碼時針對問題進行疑難排解？

如果異常終止指令碼，您可以執行指令碼加上 -Cleanup 參數，清除不一致的狀態，如下所示：

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

如果暫存磁碟機在自動偵測期間發生任何問題，系統可能會提示您選取磁碟機 (預設磁碟機 D)。

![enter-drive](media/how-to-use-perfInsights/enter-drive.png)

指令碼會解除安裝公用程式工具，並移除暫存資料夾。

### <a name="troubleshooting-other-script-issues"></a>對其他指令碼問題進行疑難排解 

如果在執行指令碼時發生任何問題，請按 Ctrl+C 中斷指令碼執行。 若要移除暫存物件，請參閱＜異常終止後清除＞一節。

如果在嘗試幾次之後指令碼仍持續失敗，建議您在啟動時使用 "-Debug" 參數選項，在「偵錯模式」中執行指令碼。

失敗發生之後，請複製完整的 PowerShell 主控台輸出，將它傳送給協助您疑難排解問題的 Microsoft 支援專員。

### <a name="how-do-i-run-the-script-in-custom-configuration-mode"></a>如何在自訂設定模式中執行指令碼？

藉由選取 [自訂] 設定，您可以平行方式啟用數個追蹤 (使用 Shift 鍵複選)：

![選取案例](media/how-to-use-perfInsights/select-scenario.png)

當您選取效能診斷、效能計數器追蹤、XPerf 追蹤、網路追蹤或 Storport 追蹤案例時，請遵循對話方塊中的指示，嘗試重現啟動追蹤之後的效能降低問題。

以下對話方塊可讓您啟動追蹤：

![start-trace](media/how-to-use-perfInsights/start-trace-message.png)

若要停止追蹤，您必須確認第二個對話方塊中的命令。

![stop-trace](media/how-to-use-perfInsights/stop-trace-message.png)
![stop-trace](media/how-to-use-perfInsights/ok-trace-message.png)

當追蹤或作業都完成之後，在 D:\\log\_collection (或暫存磁碟機) 中會產生新的檔案，名為**CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip。** 您可以將此檔案傳送給支援專員進行分析。

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>檢閱 PerfInsights 建立的診斷報告

在 PerfInsights 產生的 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip 檔案內**，您會找到詳細說明 PerfInsights 結果的 HTML 報表。 若要檢閱報表，請展開 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 檔案，然後開啟 **PerfInsights Report.html** 檔案。

選取 [Findings] \(結果) 索引標籤。

![[尋找] 索引標籤](media/how-to-use-perfInsights/findingtab.png)

**注意事項**

-   紅色訊息是已知的設定問題，可能會導致效能問題。

-   黃色訊息是警告，表示不一定會造成效能問題的非最佳化設定。

-   藍色訊息僅為資訊聲明。

請檢閱所有紅色的錯誤訊息 HTTP 連結，以取得結果的詳細資訊，以及它們如何影響效能最佳化設定的效能或最佳做法。

### <a name="disk-configuration-tab"></a>[磁碟設定] 索引標籤

[概觀] 區段會顯示不同的儲存體設定檢視，包括來自 Diskpart 和儲存空間的資訊。

[DiskMap] 和 [VolumeMap] 兩區段說明邏輯磁碟區和實體磁碟如何在雙重檢視方塊上彼此相關。

在 [PhysicalDisk] 檢視方塊 ([DiskMap]) 中，資料表會顯示在磁碟上執行的所有邏輯磁碟區。 在下列範例中，PhysicalDrive2 執行 2 個建立在多個分割區的邏輯磁碟區 (J 和 H)：

![[資料] 索引標籤](media/how-to-use-perfInsights/disktab.png)

在 [磁碟區] 檢視方塊中 ([VolumeMap])，資料表會顯示每個邏輯磁碟區下的所有實體磁碟。 請注意，對於 RAID/動態磁碟，您可能會在多個實體磁碟上執行邏輯磁碟區。 在下面的範例中，*C:\\mount* 是在 PhysicalDisk \#2 和 \#3 上設定為 *SpannedDisk* 的掛接點：

![[磁碟區] 索引標籤](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-server-tab"></a>[SQL Server] 索引標籤

如果目標 VM 裝載任何 SQL Server 執行個體，您在報表中會看到名為 [SQL Server] 的其他索引標籤：

![[SQL] 索引標籤](media/how-to-use-perfInsights/sqltab.png)

本區段包含 [概觀] 和其他裝載在 VM 上的每個 SQL Server 執行個體的子索引標籤。

[概觀] 區段包含所有實體磁碟 (系統和資料磁碟) 內容摘要的資料表，這些實體磁碟都在執行中，且混合收納資料檔和交易記錄檔。

在下例中，*PhysicalDrive0* (執行 C 磁碟機) 會出現，因為 *modeldev* 和 *modellog* 檔案都位於 C 磁碟機上，且為不同的類型 (例如它們分別為資料檔和交易記錄檔)：

![loginfo](media/how-to-use-perfInsights/loginfo.png)

SQL Server 執行個體特定索引標籤的一般區段，會顯示所選執行個體的基本資訊，而其他區段會顯示進階資訊，包括 [設定]、[設定] 和 [使用者選項]。

## <a name="references-to-the-external-tools-used"></a>所用外部工具的參考

### <a name="diskspd"></a>Diskspd

DISKSPD 是 Windows 和 Windows Server 及雲端伺服器基礎架構工程小組提供的儲存體載入產生器及效能測試工具。 如需詳細資訊，請參閱 [Diskspd](https://github.com/Microsoft/diskspd)。

### <a name="xperf"></a>XPerf

Xperf 是命令列工具，會從 Windows 效能工具套件擷取追蹤。

如需詳細資訊，請參閱 [Windows 效能工具組 – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)。

## <a name="next-steps"></a>後續步驟

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>將診斷記錄和報告上傳至 Microsoft 支援服務供進一步檢閱

當您和 Microsoft 支援服務人員合作時，他們可能會要求您傳送 PerfInsights 產生的輸出來協助疑難排解程序。

支援專員會為您建立 DTM 工作區，您會收到電子郵件訊息，包含 DTM 入口網站連結 (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm) 以及唯一的使用者識別碼和密碼。

此訊息會從 **CTS 自動化診斷服務** (ctsadiag@microsoft.com) 傳送。

![範例訊息](media/how-to-use-perfInsights/supportemail.png)

為增加安全性，您需要在第一次使用時變更密碼。

登入 DTM 之後，您會發現一個對話方塊，可上傳 PerfInsights 收集的 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 檔案。

