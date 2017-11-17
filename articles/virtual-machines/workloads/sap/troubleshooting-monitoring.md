---
title: "針對 SAP HANA on Azure (大型執行個體) 進行疑難排解和監視 | Microsoft Docs"
description: "針對 SAP HANA on Azure (大型執行個體) 進行疑難排解和監視。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5583f3d1949614dbba4d2f91d72e4ac6b4d03d1c
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="how-to-troubleshoot-and-monitor-sap-hana-large-instances-on-azure"></a>如何針對 Azure 上 SAP HANA (大型執行個體) 進行疑難排解和監視


## <a name="monitoring-in-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體) 中的監視

SAP HANA on Azure (大型執行個體) 與任何其他 IaaS 部署並無不同 - 您必須監視 OS 和應用程式在執行什麼作業，以及這些作業如何耗用下列資源：

- CPU
- 記憶體
- 網路頻寬
- 磁碟空間

與使用「Azure 虛擬機器」一樣，您必須了解上述資源類別是否足夠，或它們是否已耗盡。 以下提供每個這些不同類別的更多詳細資料：

**CPU 資源耗用量：**系統會強制執行 SAP 為針對 HANA 的特定工作負載定義的比例，以確保有足夠的 CPU 資源可用來處理儲存在記憶體中的資料。 不過，可能會有因為遺漏索引或類似的問題，而導致 HANA 耗用許多執行查詢的 CPU 的情況。 這意謂著您不僅應該監視特定 HANA 服務所耗用的 CPU 資源，也應該監視 HANA 大型執行個體單位的 CPU 資源耗用量。

**記憶體耗用量：**從 HANA 內部監視與從 HANA 外部在單位上監視一樣重要。 在 HANA 內，您可以監視資料如何耗用 HANA 已配置的記憶體，以便保持在 SAP 所要求的大小指導方針限制內。 您也可以監視「大型執行個體」層級的記憶體耗用量，以確保所安裝的額外非 HANA 軟體不會耗用太多記憶體而與 HANA 爭用記憶體。

**網路頻寬：**Azure VNet 閘道對於將資料移入 Azure VNet 的頻寬方面有所限制，因此監視 VNet 內所有 Azure VM 所接收的資料相當有用，可了解您有多接近所選 Azure 閘道 SKU 的限制。 在「HANA 大型執行個體」單位上，監視連入和連出網路流量，並隨著時間記錄所處理的磁碟區，是有意義的。

**磁碟空間：**磁碟空間耗用量通常會隨著時間增加。 其原因有許多，但最重要的包括：資料量增加、執行交易記錄備份、儲存追蹤檔案，以及執行儲存體快照。 因此，監視磁碟空間使用量並管理與「HANA 大型執行個體」單位關聯的磁碟空間相當重要。

如果是**類型 II SKU** 的 HANA 大型執行個體，伺服器會隨附預先載入的系統診斷工具。 您可以利用這些診斷工具執行系統健康情況檢查。 執行下列命令可在 /var/log/health_check 產生健康情況檢查記錄檔。
```
/opt/sgi/health_check/microsoft_tdi.sh
```
當您使用 Microsoft 支援服務小組對問題進行疑難排解時，可能也會要求您提供使用這些診斷工具的記錄檔。 您可以使用下列命令壓縮檔案。
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```


## <a name="monitoring-and-troubleshooting-from-hana-side"></a>從 HANA 端進行監視和疑難排解

為了有效分析與 SAP HANA on Azure (大型執行個體) 相關的問題，縮小問題的根本原因範圍會相當有用。 SAP 已發佈大量文件來協助您。

您可以在下列 SAP 附註中找到與 SAP HANA 效能相關的適用常見問題集：

- [SAP 附註 #2222200 - 常見問題集：SAP HANA 網路](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP 附註 #2100040 - 常見問題集：SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP 附註 #199997 - 常見問題集：SAP HANA 記憶體](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP 附註 #200000 - 常見問題集：SAP HANA 效能最佳化](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP 附註 #199930 - 常見問題集：SAP HANA I/O 分析](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP 附註 #2177064 - 常見問題集：SAP HANA 服務重新啟動和當機](https://launchpad.support.sap.com/#/notes/2177064)

**SAP HANA 警示**

第一個步驟是查看目前的 SAP HANA 警示記錄。 在 SAP HANA Studio 中，移至 [Administration Console: Alerts: Show: all alerts] \(管理主控台：警示：顯示：所有警示)。 此索引標籤會顯示在所設定最小和最大臨界值範圍外之特定值的所有 SAP HANA 警示 (可用實體記憶體、CPU 使用率等)。 根據預設，檢查會每隔 15 分鐘自動重新整理一次。

![在 SAP HANA Studio 中，移至 [Administration Console: Alerts: Show: all alerts] \(管理主控台：警示：顯示：所有警示)。](./media/troubleshooting-monitoring/image1-show-alerts.png)

**CPU**

對於因不適當的臨界值設定而觸發的警示，解決方式是重設為預設值或更合理的臨界值。

![重設為預設值或更合理的臨界值](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

下列警示可能表示有 CPU 資源問題：

- Host CPU Usage (Alert 5) (主機 CPU 使用率 (警示 5))
- Most recent savepoint operation (Alert 28) (最新的儲存點作業 (警示 28))
- Savepoint duration (Alert 54) (儲存點持續時間 (警示 54))

您可以從下列其中一種情況察覺到 SAP HANA 資料庫的 CPU 耗用量過高：

- 針對目前和過去的 CPU 使用率，引發了「警示 5」(主機 CPU 使用率)
- 概觀畫面上顯示的 CPU 使用率

![概觀畫面上顯示的 CPU 使用率](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Load (負載) 圖表可能會顯示 CPU 耗用量過高，或過去耗用量過高：

![Load (負載) 圖表可能會顯示 CPU 耗用量過高，或過去耗用量過高](./media/troubleshooting-monitoring/image4-load-graph.png)

因 CPU 使用率過高而觸發的警示可能由數個原因造成，包括但不限於：執行特定交易、載入資料、作業死當、長時間執行的 SQL 陳述式，以及查詢效能太差 (例如使用 BW on HANA Cube 時)。

如需詳細的疑難排解步驟，請參考 [SAP HANA 疑難排解：CPU 相關的原因和解決方案](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false)網站。

**作業系統**

SAP HANA on Linux 的其中一項最重要檢查就是要確保停用 Transparent Huge Pages (透明大型頁面)，請參閱 [SAP 附註 #2131662 - SAP HANA 伺服器上的 Transparent Huge Pages (THP)](https://launchpad.support.sap.com/#/notes/2131662)。

- 您可以透過下列 Linux 命令來檢查是否已啟用 Transparent Huge Pages：**cat /sys/kernel/mm/transparent\_hugepage/enabled**
- 如果將 _always_ 依下列方式包含在括弧中，即表示啟用 Transparent Huge Pages：[always] madvise never；如果將 _never_ 依下列方式包含在括弧中，則表示停用 Transparent Huge Pages：always madvise [never]

以下 Linux 命令應該不會傳回任何項目：**rpm -qa | grep ulimit**。 如果顯示安裝的是 _ulimit_，請立即將它解除安裝。

**記憶體**

您可能會注意到 SAP HANA 資料庫所配置的記憶體數量高於預期。 下列警示表示有記憶體使用量過高的問題：

- Host physical memory usage (Alert 1) (主機實體記憶體使用量 (警示 1))
- Memory usage of name server (Alert 12) (名稱伺服器的記憶體使用量 (警示 12))
- Total memory usage of Column Store tables (Alert 40) (資料行存放區資料表的總記憶體使用量 (警示 40))
- Memory usage of services (Alert 43) (服務的記憶體使用量 (警示 43))
- Memory usage of main storage of Column Store tables (Alert 45) (資料行存放區資料表的主要儲存體記憶體使用量 (警示 45))
- Runtime dump files (Alert 46) (執行階段傾印檔案 (警示 46))

如需詳細的疑難排解步驟，請參考 [SAP HANA 疑難排解：記憶體問題](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false)網站。

**網路**

請參考 [SAP HANA #2081065 - 針對 SAP HANA 網路進行疑難排解](https://launchpad.support.sap.com/#/notes/2081065)，並執行此 SAP 附註中的網路疑難排解步驟。

1. 分析伺服器與用戶端之間的來回時間。
  A. 執行 SQL 指令碼 [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700)_。_
  
2. 分析節點間的通訊。
  A. 執行 SQL 指令碼 [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700)_。_

3. 執行 Linux 命令 **ifconfig** (輸出會顯示是否有發生任何封包遺失的情況)。
4. 執行 Linux 命令 **tcpdump**。

此外，請使用開放原始碼 [IPERF](https://iperf.fr/) 工具 (或類似的工具) 來測量實際的應用程式網路效能。

如需詳細的疑難排解步驟，請參考 [SAP HANA 疑難排解：網路效能和連線問題](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false)網站。

**儲存體**

從使用者的觀點來看，當發生 I/O 效能問題時，應用程式 (或系統整體) 會執行緩慢、沒有回應，或甚至看似死當。 在 SAP HANA Studio 的 [Volumes] \(磁碟區) 索引標籤中，您可以看到連接的磁碟區，以及每個服務使用哪些磁碟區。

![在 SAP HANA Studio 的 [Volumes] \(磁碟區) 索引標籤中，您可以看到連接的磁碟區，以及每個服務使用哪些磁碟區。](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

在畫面下半部中有連接的磁碟區，您可以看到磁碟區的詳細資料，例如檔案和 I/O 統計資料。

![在畫面下半部中有連接的磁碟區，您可以看到磁碟區的詳細資料，例如檔案和 I/O 統計資料](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

如需詳細的疑難排解步驟，請參考 [SAP HANA 疑難排解：I/O 相關的根本原因和解決方案](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false)和 [SAP HANA 疑難排解：磁碟相關的根本原因和解決方案](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false)網站。

**診斷工具**

您可以透過 HANA\_Configuration\_Minichecks 執行「SAP HANA 健康情況檢查」。 此工具會傳回應該已在 SAP HANA Studio 中引發成警示的潛在重大技術問題。

請參考 [SAP 附註 #1969700 - SAP HANA 的 SQL 陳述式集合](https://launchpad.support.sap.com/#/notes/1969700)，並下載該附註隨附的 SQL Statements.zip 檔案。 請將這個 .zip 檔案儲存在本機硬碟。

在 SAP HANA Studio 的 [System Information] \(系統資訊) 索引標籤上，於 [Name] \(名稱) 資料行上按一下滑鼠右鍵，然後選取 [Import SQL Statements] \(匯入 SQL 陳述式)。

![在 SAP HANA Studio 的 [System Information] \(系統資訊) 索引標籤上，於 [Name] \(名稱) 資料行上按一下滑鼠右鍵，然後選取 [Import SQL Statements] \(匯入 SQL 陳述式)](./media/troubleshooting-monitoring/image7-import-statements-a.png)

選取儲存在本機的 SQL Statements.zip 檔案，將會匯入含有對應的 SQL 陳述式的資料夾。 此時，可以使用這些 SQL 陳述式來執行許多不同的診斷檢查。

例如，若要測試「SAP HANA 系統複寫」頻寬需求，請在 [Replication: Bandwidth] \(複寫：頻寬) 底下的 [Bandwidth] \(頻寬) 陳述式上按一下滑鼠右鍵，然後在 SQL Console (SQL 主控台) 中選取 [Open] \(開啟)。

將會開啟完整的 SQL 陳述式，讓您變更輸入參數 (modification 區段)，然後加以執行。

![將會開啟完整的 SQL 陳述式，讓您變更輸入參數 (modification 區段)，然後加以執行](./media/troubleshooting-monitoring/image8-import-statements-b.png)

另一個範例是在 [Replication: Overview] \(複寫：概觀) 底下的陳述式上按一下滑鼠右鍵。 從操作功能表中，選取 [Execute] \(執行)：

![另一個範例是在 [Replication: Overview] \(複寫：概觀) 底下的陳述式上按一下滑鼠右鍵。 從操作功能表中，選取 [Execute] \(執行)](./media/troubleshooting-monitoring/image9-import-statements-c.png)

這會產生可協助進行疑難排解的資訊：

![這會產生可協助進行疑難排解的資訊](./media/troubleshooting-monitoring/image10-import-statements-d.png)

針對 HANA\_Configuration\_Minichecks 進行相同的操作，然後檢查 [C] (重大) 資料行中是否有任何 _X_ 標記。

範例輸出：

**HANA\_Configuration\_MiniChecks\_Rev102.01+1**：適用於一般 SAP HANA 檢查。

![HANA\_Configuration\_MiniChecks\_Rev102.01+1：適用於一般 SAP HANA 檢查](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_Services\_Overview**：適用於 SAP HANA 服務目前執行情況的概觀。

![HANA\_Services\_Overview：適用於 SAP HANA 服務目前執行情況的概觀](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_Services\_Statistics**：適用於 SAP HANA 服務資訊 (CPU、記憶體等)。

![HANA\_Services\_Statistics：適用於 SAP HANA 服務資訊 ](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_Configuration\_Overview\_Rev110+**：適用於 SAP HANA 執行個體的一般相關資訊。

![HANA\_Configuration\_Overview\_Rev110+：：適用於 SAP HANA 執行個體的一般相關資訊](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_Configuration\_Parameters\_Rev70+**：用來檢查 SAP HANA 參數。

![HANA\_Configuration\_Parameters\_Rev70+：用來檢查 SAP HANA 參數](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

