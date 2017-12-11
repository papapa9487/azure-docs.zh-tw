---
title: "Hyper-V 到 Azure 的 Azure Site Recovery 部署規劃工具 | Microsoft Docs"
description: "本文說明執行 Hyper-V 到 Azure 的 Azure Site Recovery 部署規劃工具案例的模式。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: bb4ec5cfd455ab0cc22ab693c2a07eed9883dc76
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="run-azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>執行 Hyper-V 到 Azure 的 Azure Site Recovery 部署規劃工具

## <a name="modes-of-running-deployment-planner"></a>執行部署規劃工具的模式
您可以在下列任何模式中執行命令列工具 (ASRDeploymentPlanner.exe)： 
1.  [取得 VM 清單](#get-vm-list-for-profiling-hyper-v-vms)
2.  [剖析](#profile-hyper-v-vms)
3.  [報告產生](#generate-report)
4.  [取得輸送量](#get-throughput)

首先，執行此工具，從單一或多個 Hyper-V 主機取得 VM 清單。  然後在分析模式中執行此工具，以蒐集 VM 資料變換和 IOPS。 接著，執行此工具來產生報告，以找出網路頻寬和儲存體需求。

## <a name="get-vm-list-for-profiling-hyper-v-vms"></a>取得 VM 清單以便分析 Hyper-V VM
首先，您需有要分析的 VM 清單。 使用部署規劃工具的 GetVMList 模式，以單一命令產生多部 Hyper-V 主機上存在的 VM 清單。 一旦產生完整清單，您可以移除不想從輸出檔剖析的 VM。 然後使用所有其他作業 (分析、報告產生及取得輸送量) 的輸出檔。

您可以產生將工具指向 Hyper-V 叢集或獨立 Hyper-V 主機或所有組合的 VM 清單。

### <a name="command-line-parameters"></a>命令列參數
下表包含要在 GetVMList 模式執行之工具的必要和選用參數清單。 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```
| 參數名稱 | 說明 |
|---|---|
| -Operation | GetVMList |
| -User | 要連線到 Hyper-V 主機或 Hyper-V 叢集的使用者名稱。 使用者必須具有系統管理權限。|
|-ServerListFile | 包含要剖析之 VM 的伺服器清單檔案。 此檔案路徑可以是絕對或相對路徑。 此檔案應在每一行包含下列其中一項：<ul><li>Hyper-V 主機名稱或 IP 位址</li><li>Hyper-V 叢集名稱或 IP 位址</li></ul><br>例如，"ServerList.txt" 檔案包含下列伺服器︰<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(選用) 用來儲存此作業期間所產生之資料的通用命名慣例 (UNC) 或本機目錄路徑。 如果未指定，目前路徑下名為 'ProfiledData' 的目錄會作為預設目錄。|
|-OutputFile| (選用) 此檔案儲存從指定的 Hyper-V 伺服器擷取的 VM 清單。 如果未提及名稱，則詳細資料會儲存在 "VMList.txt"。  在移除不需要分析的 VM 之後，使用此檔案開始分析。|
|-Password|(選用) 用於連線到 Hyper-V 主機的密碼。   如果未將此密碼指定為一個參數，系統將會在命令執行時提示您稍後輸入密碼。|

### <a name="how-does-getvmlist-discovery-work"></a>GetVMList 探索的運作方式為何？
**Hyper-V 叢集**：在伺服器清單檔案中指定 Hyper-V 叢集名稱後，此工具會尋找叢集的所有 Hyper-V 節點，並取得每部 Hyper-V 主機上存在的 VM。

**Hyper-V 主機**：指定 Hyper-V 主機名稱後，此工具會先檢查它是否屬於叢集。 如果是，它會擷取屬於叢集的節點。 然後，它會從每部Hyper-V 主機取得 VM。 

您也可以選擇在檔案中列出您想要以手動方式剖析的好記 VM 名稱 / IP 位址。

在「記事本」中開啟輸出檔案，然後將您要剖析的所有 VM 名稱複製到另一個檔案 (例如 ProfileVMList.txt)，每一行一個 VM 名稱。 此檔案作為下列其他作業之工具 -VMListFile 參數的輸出：分析、報告產生及取得輸送量。

### <a name="example-1-to-store-the-list-of-vms-in-a-file"></a>範例 1：在檔案中儲存 VM 清單
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile “E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

### <a name="example-2-to-store-the-list-of-vms-at-the-default-location-ie--directory-path"></a>範例 2：在預設位置 (即 -Directory 路徑) 儲存 VM 清單
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>設定檔 Hyper-V VM
在分析模式中，部署規劃工具會連線到每部 Hyper-V 主機，以收集有關 VM 的效能資料。 

* 分析作業不會直接連線到生產 VM，所以不會影響其效能。 所有效能資料都是從 Hyper-V 主機收集而來。
* 此工具會每 15 秒查詢一次 Hyper-V 主機，以確保分析正確性，並儲存每分鐘效能計數器資料的平均值。
* 此工具會順暢地處理叢集中節點之間的 VM 移轉，以及主機內的儲存體移轉。

### <a name="get-vm-list-to-profile"></a>取得要剖析的 VM 清單
請參閱 [GetVMList](#get-vm-list-for-profiling-hyper-v-vms) 作業，以建立要剖析的 VM 清單

取得要剖析的 VM 清單之後，您可以在剖析模式中執行此工具。 

### <a name="command-line-parameters"></a>命令列參數
下表包含要在分析模式執行之工具的必要和選用參數清單。 此工具通用於 VMware 到 Azure 和 Hyper-V 到 Azure 案例。 下列參數適用於 Hyper-V。 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```
| 參數名稱 | 說明 |
|---|---|
| -Operation | StartProfiling |
| -User | 要連線到 Hyper-V 主機或 Hyper-V 叢集的使用者名稱。 使用者必須具有系統管理權限。|
| -VMListFile | 包含要剖析之 VM 清單的檔案。 此檔案路徑可以是絕對或相對路徑。 對於 Hyper-V，這個檔案是 GetVMList 作業的輸出檔。 如果以手動方式準備，此檔案的每一行應包含一個伺服器名稱或 IP 位址，後面接著以 \ 分隔的 VM 名稱。 檔案中指定的 VM 名稱應該與 Hyper-V 主機上的 VM 名稱相同。<ul>例如，"VMList.txt" 檔案包含下列 VM︰<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|要執行分析的分鐘數。 最小值為 30 分鐘。|
|-NoOfHoursToProfile|要執行分析的時數。|
|-NoOfDaysToProfile |要執行剖析的天數。 建議您執行分析 7 天以上，以確保觀察到指定期間內您環境中的工作負載模式，並用來提供精確的建議。|
|-Virtualization|指定虛擬化類型 (VMware 或 Hyper-V)。|
|-Directory|(選用) 用來儲存剖析期間所產生之剖析資料的通用命名慣例 (UNC) 或本機目錄路徑。 如果未指定，目前路徑下名為 'ProfiledData' 的目錄將會作為預設目錄。|
|-Password|(選用) 用於連線到 Hyper-V 主機的密碼。 如果現在未指定，系統稍後會在命令執行期間提示您輸入密碼。|
|-StorageAccountName|(選用) 儲存體帳戶名稱，用於找出從內部部署至 Azure 的資料複寫可達成的輸送量。 此工具會將測試資料上傳到此儲存體帳戶，以計算輸送量。|
|-StorageAccountKey|(選用) 用來存取儲存體帳戶的儲存體帳戶金鑰。 移至 Azure 入口網站 > 儲存體帳戶 > <Storage account name> > 設定 > 存取金鑰 > Key1 (或傳統儲存體帳戶的主要存取金鑰)。|
|-Environment|(選用) 這是您的目標 Azure 儲存體帳戶環境。 可以是下列三個值之一 - AzureCloud、AzureUSGovernment、AzureChinaCloud。 預設值為 AzureCloud。 當目標 Azure 區域是 Azure US Government 或 Azure China 雲端時，請使用此參數。|

我們建議至少剖析您的 VM 7 天以上。 如果變換模式在一個月內改變，我們建議在您看到最大變換的一週內進行剖析。 最好的方法是剖析 31 天，以取得更好的建議。 在剖析期間，ASRDeploymentPlanner.exe 會持續執行。 此工具會採用剖析階段輸入 (以天為單位)。 如需工具快速測試或概念證明，您可以剖析幾個小時或幾分鐘。 允許的最小剖析時間為 30 分鐘。 

在分析期間，您可以選擇性地傳送儲存體帳戶名稱和金鑰，以尋找 Azure Site Recovery 可在從 Hyper-V 伺服器複寫至 Azure 時達成的輸送量。 如果未在分析期間傳送儲存體帳戶名稱和金鑰，此工具就不會計算可達成的輸送量。

您可以針對不同組的 VM 執行多個工具執行個體。 確保 VM 名稱不會在任何剖析集中重複出現。 例如，若已剖析 10 部 VM (VM1 到 VM10)，而在幾天後您想要剖析另外 5 部 VM (VM11 到 VM15)，您可以針對第二組的 VM (VM11 到 VM15) 從另一個命令列主控台執行此工具。 確保第二組的 VM 沒有任何來自第一個剖析執行個體的 VM 名稱，或您使用不同的輸出目錄進行第二次執行。 如有兩個工具執行個體用於剖析相同的 VM 並使用相同的輸出目錄，所產生的報告則會不正確。 

在剖析作業開始時會擷取一次 VM 組態，並儲存在名為 VMDetailList.xml 的檔案。 產生報告時會使用此資訊。 從剖析開始到結尾，不會擷取任何 VM 組態變更 (例如，增加的核心、磁碟或 NIC 數目)。 如果已分析的 VM 組態在分析期間發生變更，以下是在產生報告時取得最新 VM 詳細資料的因應措施：

* 備份 VMdetailList.xml，然後從其目前的位置刪除此檔案。
* 在報告產生時傳遞 -User 和 -Password 引數

剖析命令會在剖析目錄中產生數個檔案。 請勿刪除任何檔案，因為這麼做會影響報告產生。

### <a name="examples"></a>範例
#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>範例 1︰剖析 VM 30 天，並找出從內部部署至 Azure 的輸送量
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>範例 2︰剖析 VM 15 天
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>範例 3：剖析 VM 60 分鐘以便快速測試工具
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>範例 4：剖析 VM 2 小時以取得概念證明
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

>[注意！]
>
* 如果此工具執行所在的伺服器已重新啟動或已當機，或如果您使用 Ctrl + C 關閉工具，則會保留剖析的資料。 不過，過去 15 分鐘的剖析資料有可能會遺失。 在這類情況下，請在伺服器開始備份之後，於分析模式中重新執行此工具。
* 傳遞儲存體帳戶名稱和金鑰時，此工具會在剖析的最後一個步驟測量輸送量。 如果此工具在剖析完成前關閉，則不會計算輸送量。 若要在產生報告之前找到輸送量，您可以從命令列主控台執行 GetThroughput 作業。 否則，產生的報告不會包含輸送量資訊。
* Azure Site Recovery 不支援具有 iSCSI 和傳遞磁碟的 VM。 不過，此工具無法偵測，以及剖析 VM 所連結的 iSCSI 和傳遞磁碟。

## <a name="generate-report"></a>產生報告
此工具會產生啟用巨集的 Microsoft Excel 檔案 (XLSM) 做為報告輸出，其中摘要說明所有的部署建議。 此報告的名稱為 DeploymentPlannerReport_<unique numeric identifier>.xlsm 且置於指定的目錄中。
剖析完成後，您可以在報告產生模式中執行工具。 

### <a name="command-line-parameters"></a>命令列參數
下表包含要在報告產生模式中執行之必要和選用工具參數的清單。 此工具通用於 VMware 到 Azure 和 Hyper-V 到 Azure 案例。 下列參數適用於 Hyper-V。
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```
| 參數名稱 | 說明 |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | 包含要產生報告之已剖析 VM 清單的檔案。 此檔案路徑可以是絕對或相對路徑。 對於 Hyper-V，這個檔案是 GetVMList 作業的輸出檔。 如果以手動方式準備，此檔案的每一行應包含一個伺服器名稱或 IP 位址，後面接著以 \ 分隔的 VM 名稱。 檔案中指定的 VM 名稱應該與 Hyper-V 主機上的 VM 名稱相同。<ul>例如，"VMList.txt" 檔案包含下列 VM︰<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|指定虛擬化類型 (VMware 或 Hyper-V)。|
|-Directory|(選用) 儲存分析資料 (分析期間產生的檔案) 的通用命名慣例 (UNC) 或本機目錄路徑。 產生報告時需要這項資料。 如果未指定名稱，目前路徑下名為 'ProfiledData' 的目錄將會作為預設目錄。|
| -User | (選用) 要連線到 Hyper-V 主機或 Hyper-V 叢集的使用者名稱。 使用者必須具有系統管理權限。<br>使用者名稱和密碼用來擷取 VM 的最新組態資訊 (如磁碟數目、核心數目、NIC 數目等)，以使用於報告中。 如未提供，則會使用分析期間收集的組態資訊。|
|-Password|(選用) 用於連線到 Hyper-V 主機的密碼。 如果現在未指定，系統稍後會在命令執行期間提示您輸入密碼。|
| -DesiredRPO | (選用) 以分鐘為單位的所需復原點目標。 預設值是 15 分鐘。|
| -Bandwidth | (選用) 以 Mbps 為單位的頻寬。 此參數用來計算指定的頻寬可達成的 RPO。 |
| -StartDate | (選用) 採用 MM-DD-YYYY:HH:MM 格式 (24 小時制) 的開始日期和時間。 StartDate 必須與 EndDate 一起指定。 若已指定 StartDate，則會針對在 StartDate 與 EndDate 之間收集的剖析資料產生報告。 |
| -EndDate | (選用) 採用 MM-DD-YYYY:HH:MM 格式 (24 小時制) 的結束日期和時間。 EndDate 必須與 StartDate 一起指定。 若已指定 EndDate，則會針對在 StartDate 與 EndDate 之間收集的剖析資料產生報告。 |
| -GrowthFactor | (選用) 以百分比表示的成長因子。 預設值為 30%。 |
| -UseManagedDisks | (選擇性) UseManagedDisks - 是/否。 預設值為 [是]。 計算可以放入單一儲存體帳戶的虛擬機器數目時，請考慮在受控磁碟而不是非受控磁碟上進行虛擬機器的容錯移轉/測試容錯移轉。 |
|-SubscriptionId |(選用) 訂用帳戶 GUID。 使用這個參數來產生成本估計報告，其中包含以您的訂用帳戶為基礎的最新價格 (以指定的貨幣計價)，與您的訂用帳戶相關聯的優惠，以及適用於您特定目標 Azure 區域的優惠。|
|-TargetRegion|(選用) 複寫的目標 Azure 區域。 因為 Azure 在每個區域有不同的成本，若要產生特定目標 Azure 區域的報告，請使用此參數。<br>預設值是 WestUS2 或上次使用的目標區域。<br>請參閱[支援的目標區域](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-target-regions)清單。|
|-OfferId|(選用) 與指定訂用帳戶相關聯的優惠。 預設值為 MS-AZR-0003P (預付型方案)。|
|-Currency|(選用) 在所產生的報告中用於顯示成本的貨幣。 預設值是美元 ($) 或上次使用的貨幣。<br>請參閱[支援的貨幣](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-currencies)清單。|

### <a name="examples"></a>範例
#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>範例 1︰當剖析的資料位於本機磁碟機時，使用預設值來產生報告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>範例 2︰當剖析的資料位於遠端伺服器時產生報告
您應具備遠端目錄的讀取/寫入存取權。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V - -Directory “\\PS1-W2K12R2\Hyper-V_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-that-you-will-be-provision-for-the-replication"></a>範例 3︰使用您將為了複寫而佈建的特定頻寬來產生報告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -Bandwidth 100
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>範例 4︰使用 5% (而非預設值 30%) 的成長因子來產生報告 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>範例 5︰使用剖析資料子集來產生報告
例如，您有 30 天的剖析資料，而只想要產生 20 天的報告。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minutes-rpo"></a>範例 6：針對 5 分鐘 RPO 產生報告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>範例 7：使用印度盧比和特定優惠識別碼，產生南印度 Azure 區域的報告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>用來計算的百分位數值
**工具在產生報告時，會使用剖析期間所收集之效能計量的哪些預設百分位數值？**

此工具預設為在所有 VM 的剖析期間收集之讀/寫 IOPS、寫入 IOPS 及資料變換的第 95 個百分位數值。 此計量可確保您的 VM 可以看到第 100 個百分位數尖峰，因為暫存事件不會用來判斷您的目標儲存體帳戶和來源頻寬需求。 例如，暫存事件可能是一天執行一次的備份作業、定期資料庫檢索或分析報告產生活動，或其他類似的短期時間點事件。

使用第 95 個百分位數值可提供實際工作負載特性的真實情況，並且讓您在 Azure 上執行這些工作負載時獲得最佳效能。 我們不希望您會需要變更這個數字。 如果您變更此數字 (例如，變更為第 90 個百分位數)，您可以更新預設資料夾中的組態檔 ASRDeploymentPlanner.exe.config 並加以儲存，以產生現有剖析資料的新報告。
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>成長因子考量
**規劃部署時為何應考量成長因子？**
假設使用量可能會隨著時間增加，請務必考量您的工作負載特性成長。 防護就緒之後，如果工作負載特性變更，在未停用並重新啟用保護的情況下，您無法切換到不同的儲存體帳戶進行保護。

例如，假設您的 VM 位於置於標準儲存體複寫帳戶中。 接下來的三個月，可能會發生幾項變更︰

* 在 VM 上執行之應用程式的使用者數目會增加。
* 結果在 VM 上增加的變換會要求 VM 移至進階儲存體，Azure Site Recovery 複寫才可以保持一致。
* 因此，您必須對進階儲存體帳戶停用並重新啟用保護。

我們強烈建議您在部署規劃期間規劃成長，而當預設值為 30% 時，您最瞭解您的應用程式使用量模式和成長預測，而且可以在產生報告時相應變更此數字。 此外，您可以使用不同的成長因子，針對相同的剖析資料產生多份報告，判斷哪些目標儲存體和來源頻寬建議最適合您。 

產生的 Microsoft Excel 報告包含下列資訊：

* [內部部署摘要](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [建議](site-recovery-hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VM<->儲存體放置](site-recovery-hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)]
* [相容的 VM](site-recovery-hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [不相容的 VM](site-recovery-hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [內部部署儲存體需求](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [IR 批次處理](site-recovery-hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [成本估計](site-recovery-hyper-v-deployment-planner-cost-estimation.md)

![部署規劃工具報告](media/site-recovery-hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>取得輸送量
若要預估 Azure Site Recovery 在複寫期間可以達成的輸送量 (從內部部署至 Azure)，請以 GetThroughput 模式執行工具。 此工具會計算來自工具執行所在伺服器的輸送量。 在理想情況下，此伺服器是要保護其 VM 的 Hyper-V 伺服器。 

### <a name="command-line-parameters"></a>命令列參數 
開啟命令列主控台，然後移至 Azure Site Recovery 部署規劃工具資料夾。 使用下列參數執行 ASRDeploymentPlanner.exe
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```
 參數名稱 | 說明 |
|---|---|
| -Operation | GetThroughtput |
|-Virtualization|指定虛擬化類型 (VMware 或 Hyper-V)。|
|-Directory|(選用) 儲存分析資料 (分析期間產生的檔案) 的通用命名慣例 (UNC) 或本機目錄路徑。 產生報告時需要這項資料。 如果未指定名稱，目前路徑下名為 'ProfiledData' 的目錄將會作為預設目錄。|
| -StorageAccountName | 儲存體帳戶名稱，用於找出從內部部署至 Azure 的資料複寫所耗用的頻寬。 此工具會將測試資料上傳到此儲存體帳戶，以找出所耗用的頻寬。 |
| -StorageAccountKey | 用來存取儲存體帳戶的儲存體帳戶金鑰。 移至 Azure 入口網站 > 儲存體帳戶 > <儲存體帳戶名稱> > 設定 > 存取金鑰 > Key1。|
| -VMListFile | 包含要剖析之 VM 清單的檔案，以便計算所耗用的頻寬。 此檔案路徑可以是絕對或相對路徑。 對於 Hyper-V，這個檔案是 GetVMList 作業的輸出檔。 如果以手動方式準備，此檔案的每一行應包含一個伺服器名稱或 IP 位址，後面接著以 \ 分隔的 VM 名稱。 檔案中指定的 VM 名稱應該與 Hyper-V 主機上的 VM 名稱相同。<ul>例如，"VMList.txt" 檔案包含下列 VM︰<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(選用) 這是您的目標 Azure 儲存體帳戶環境。 可以是下列三個值之一 - AzureCloud、AzureUSGovernment、AzureChinaCloud。 預設值為 AzureCloud。 當目標 Azure 區域是 Azure US Government 或 Azure China 雲端時，請使用此參數|


此工具會在指定的目錄中建立數個 64MB asrvhdfile<#>.vhd 檔案 (其中 # 是檔案數目)。 此工具會將這些檔案上傳至儲存體帳戶，以找出輸送量。 測量輸送量之後，此工具會從儲存體帳戶和本機伺服器中刪除所有這類檔案。 如果此工具在計算輸送量時因為任何原因而終止，它不會從儲存體或本機伺服器中刪除檔案。 您必須手動加以刪除。

輸送量會在指定的時間點進行測量，而這是 Azure Site Recovery 可以在複寫期間內達成的最大輸送量 (前提是所有其他因子維持不變)。 例如，如果任何應用程式開始在相同網路上耗用更多頻寬，則複寫期間的實際輸送量會有所不同。 如果 GetThroughput 作業在受保護的 VM 有高度資料變換時執行，則測量的輸送量結果會不同。 建議在剖析期間的各種時間點執行此工具，以了解可在不同時間達成的輸送量等級。 在報告中，此工具會顯示最後測量的輸送量。
    
### <a name="example"></a>範例
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory E:\Hyp-erV_ProfiledData -VMListFile E:\Hyper-V_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

[注意！]
>
> 在具有與 Hyper-V 伺服器相同之儲存體和 CPU 特性的伺服器上執行此工具。
>
> 如需複寫，請設定建議的頻寬，以符合當時的 100% RPO。 在設定適當的頻寬之後，如果您未看到此工具所報告的達成輸送量有任何增加，請執行下列作業︰
>
>  1. 檢查以判斷是否有任何網路服務品質 (QoS) 會限制 Azure Site Recovery 輸送量。
>
>  2. 檢查以判斷 Azure Site Recovery 保存庫是否位於支援的最近實體 Microsoft Azure 區域，以縮小網路延遲。
>
>  3. 檢查本機儲存體特性，以判斷是否可以改善硬體 (例如 HDD 變成 SSD)。
>

## <a name="next-steps"></a>後續步驟
* [分析已產生的報告](site-recovery-hyper-v-deployment-planner-analyze-report.md)。