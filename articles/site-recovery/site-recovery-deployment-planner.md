---
title: "VMware 到 Azure 的 Azure Site Recovery Deployment Planner | Microsoft Docs"
description: "這是 Azure Site Recovery Deployment Planner 使用者指南。"
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
ms.date: 08/28/2017
ms.author: nisoneji
ms.openlocfilehash: 60b0641076c2fa8ed2feb5c64e7b119519f46cf4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
本文是 VMware 到 Azure 生產部署的 Azure Site Recovery Deployment Planner 使用者指南。

## <a name="overview"></a>概觀

使用 Site Recovery 開始保護任何 VMware 虛擬機器之前，請根據每日資料變化率來配置足夠的頻寬，以符合您所需的復原點目標 (RPO)。 務必在內部部署環境中部署適當的組態伺服器和處理序伺服器數目。

您也需要建立正確的目標 Azure 儲存體帳戶類型和數目。 您可建立標準或進階儲存體帳戶，將使用量隨時間增加所造成的來源生產伺服器成長納入考量。 您可以根據工作負載特性 (例如，每秒的讀/寫 I/O 作業 [IOPS]，或資料變換) 和 Site Recovery 限制，選擇每部 VM 的儲存體類型。

Site Recovery Deployment Planner 公開預覽版本是一項命令列工具，目前僅適用於 VMware 到 Azure 案例。 您可以使用此工具從遠端剖析 VMware VM (完全不影響實際運作)，以了解成功複寫與測試容錯移轉的頻寬和 Azure 儲存體需求。 您不需安裝任何 Site Recovery 內部部署元件，即可執行此工具。 然而，若要取得精確的已達成輸送量結果，建議在以下 Windows Server 上執行 Planner：符合您在生產環境部署的前幾個步驟中最終需要部署之 Site Recovery 組態伺服器的最低需求。

此工具提供下列詳細資料︰

**相容性評估**

* 以磁碟數目、磁碟大小、IOPS、變換和開機類型 (EFI/BIOS) 為基礎的 VM 合適性評估
* 差異複寫所需的預估網路頻寬

**網路頻寬需求與 RPO 評估**

* 差異複寫所需的預估網路頻寬
* Site Recovery 可以從內部部署至 Azure 取得的輸送量
* 根據在給定時間量內完成初始複寫所需的預估頻寬，要劃分批次的 VM 數目

**Azure 基礎結構需求**

* 每部 VM 的儲存體類型 (標準或進階儲存體帳戶) 需求
* 為了複寫所要設定的標準和進階儲存體帳戶總數
* 以 Azure 儲存體指引為基礎的儲存體帳戶命名建議
* 所有 VM 的儲存體帳戶放置
* 在訂用帳戶上進行測試容錯移轉或容錯移轉之前所要設定的 Azure 核心數目
* 每個內部部署 VM 的 Azure VM 建議大小

**內部部署基礎結構需求**
* 要在內部部署環境中部署的必要組態伺服器和處理序伺服器數目

>[!IMPORTANT]
>
>因為使用量可能會隨時間增加，所以執行所有上述工具計算時，假設工作負載特性中的成長因子為 30%，並使用所有剖析計量 (讀/寫 IOPS、 變換等等) 的第 95 個百分位數值。 這兩個元素 (成長因子和百分位數計算) 皆可設定。 若要深入了解成長因子，請參閱「成長因子考量」一節。 若要深入了解百分位數值，請參閱「用於計算的百分位數值」一節。
>

## <a name="requirements"></a>需求
此工具有兩個主要階段：剖析和報告產生。 另外還有第三個選項：只計算輸送量。 下表列出起始剖析 / 輸送量測量之伺服器的需求：

| 伺服器需求 | 說明|
|---|---|
|剖析和輸送量測量| <ul><li>作業系統：Microsoft Windows Server 2012 R2<br>(最好至少符合[組態伺服器的大小建議](https://aka.ms/asr-v2a-on-prem-components))</li><li>機器組態︰8 個 vCPU、16 GB RAM、300 GB HDD</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[適用於 Visual Studio 2012 的 Microsoft Visual C++ 可轉散發套件](https://aka.ms/vcplusplus-redistributable)</li><li>透過網際網路從這部伺服器存取 Azure</li><li>Azure 儲存體帳戶</li><li>伺服器的系統管理員存取權</li><li>100 GB 的可用磁碟空間下限 (假設剖析平均各有 3 個磁碟的 1000 部 VM 30 天)</li><li>VMware vCenter 統計資料層級設定應該設定為 2 或高層級</li><li>允許連接埠 443：ASR 部署規劃工具使用此連接埠來連線至 vCenter 伺服器/ESXi 主機</ul></ul>|
| 報告產生 | 具有 Microsoft Excel 2013 和更新版本的 Windows PC 或 Windows Server |
| 使用者權限 | 使用者帳戶的唯讀權限，在剖析期間用來存取 VMware vCenter Server/VMware vSphere ESXi 主機 |

> [!NOTE]
>
>此工具只能剖析具有 VMDK 和 RDM 磁碟的 VM。 不能剖析具有 iSCSI 或 NFS 磁碟的 VM。 Site Recovery 支援 VMware 伺服器適用的 iSCSI 和 NFS 磁碟，但因為部署規劃工具不在客體內，而且只使用 vCenter 效能計數器進行剖析，所以此工具看不見這些磁碟類型。
>

## <a name="download-and-extract-the-public-preview"></a>下載並解壓縮公開預覽版本
1. 下載最新版的 [Site Recovery Deployment Planner 公開預覽版本](https://aka.ms/asr-deployment-planner)。  
此工具封裝在 .zip 資料夾中。 目前的工具版本僅支援 VMware 到 Azure 案例。

2. 從您要執行工具的位置，將 .zip 資料夾複製到 Windows Server。  
如果伺服器可存取網路以連線到保存要剖析之 VM 的 vCenter Server/vSphere ESXi 主機，您可以從 Windows Server 2012 R2 執行此工具。 不過，我們建議您在硬體設定符合[組態伺服器調整大小方針](https://aka.ms/asr-v2a-on-prem-components)的伺服器上執行此工具。 如果您已在內部部署環境部署 Site Recovery 元件，請從組態伺服器執行此工具。

 我們建議執行此工具的伺服器具有與組態伺服器 (具備內建處理序伺服器) 相同的硬體組態。 這種組態可確保工具報告的達成輸送量符合 Site Recovery 在複寫期間可達到的實際輸送量。 輸送量計算取決於伺服器可用的網路頻寬和伺服器的硬體組態 (CPU、儲存體等等)。 如果您從任何其他伺服器執行此工具，則會計算從該伺服器至 Microsoft Azure 的輸送量。 此外，因為該伺服器的硬體組態可能會與組態伺服器不同，因為工具報告的達成輸送量可能不正確。

3. 將 .zip 資料夾解壓縮。  
此資料夾包含多個檔案和子資料夾。 可執行檔是父資料夾中的 ASRDeploymentPlanner.exe。

    範例：  
    將 .zip 檔案複製到 E:\ 磁碟機並將它解壓縮。
   E:\ASR Deployment Planner-Preview_v1.2.zip

    E:\ASR Deployment Planner-Preview_v1.2\ ASR Deployment Planner-Preview_v1.2\ ASRDeploymentPlanner.exe

## <a name="capabilities"></a>功能
您可以在下列任何模式 (共三種) 中執行命令列工具 (ASRDeploymentPlanner.exe)：

1. 剖析  
2. 報告產生
3. 取得輸送量

首先，在剖析模式中執行此工具，以蒐集 VM 資料變換和 IOPS。 接著，執行此工具來產生報告，以找出網路頻寬和儲存體需求。

## <a name="profiling"></a>剖析
在剖析模式中，Deployment Planner 工具會連接到 vCenter Server/vSphere ESXi 主機，以收集有關 VM 的效能資料。

* 剖析作業不會直接連線到生產 VM，所以不會影響其效能。 所有效能資料都是從 vCenter Server/vSphere ESXi 主機收集而來。
* 為了確保剖析對伺服器造成的影響微不足道，工具會每隔 15 分鐘查詢一次 VCenter Server/vSphere EXSi 主機。 此查詢間隔並不會損及剖析精確度，因為此工具會儲存每分鐘的效能計數器資料。

### <a name="create-a-list-of-vms-to-profile"></a>建立要剖析的 VM 清單
首先，您需有要分析的 VM 清單。 您可以在下列程序中使用 VMware vSphere PowerCLI 命令，取得 vCenter Server 或 vSphere ESXi 主機上的所有 VM 名稱。 或者，您可以列出您想要以手動方式剖析的好記 VM 名稱 / IP 位址。

1. 登入已安裝 VMware vSphere PowerCLI 的 VM。
2. 開啟 VMware vSphere PowerCLI 主控台。
3. 確保已啟用指令碼的執行原則。 如果已停用，請在系統管理員模式中啟動 VMware vSphere PowerCLI 主控台，然後執行下列命令來啟用它︰

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. 如果 Connect-VIServer 無法辨識為 Cmdlet 名稱，您可能需要執行下列命令。
 
            Add-PSSnapin VMware.VimAutomation.Core 

5. 若要取得 vCenter Server/vSphere ESXi 主機的所有 VM 名稱並將此清單儲存在 .txt 檔案中，請執行此處所列的兩個命令。
以您的輸入取代 &lsaquo;server name&rsaquo;、&lsaquo;user name&rsaquo;、&lsaquo;password&rsaquo;、&lsaquo;outputfile.txt&rsaquo;。

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

6. 在「記事本」中開啟輸出檔案，然後將您要剖析的所有 VM 名稱複製到另一個檔案 (例如 ProfileVMList.txt)，每一行一個 VM 名稱。 此檔案可做為命令列工具之 -VMListFile 參數的輸入。

    ![Deployment Planner 中的 VM 名稱清單](./media/site-recovery-deployment-planner/profile-vm-list.png)

### <a name="start-profiling"></a>開始剖析
取得要剖析的 VM 清單之後，您可以在剖析模式中執行此工具。 以下是要在剖析模式中執行之工具的必要和選擇性參數清單。

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| 參數名稱 | 說明 |
|---|---|
| -Operation | StartProfiling |
| -Server | 要剖析其 VM 之 vCenter Server/vSphere ESXi 主機的完整網域名稱或 IP 位址。|
| -User | 用於連線至 vCenter Server/vSphere ESXi 主機的使用者名稱。 使用者必須至少具備唯讀存取權限。|
| -VMListFile | 包含要剖析之 VM 清單的檔案。 此檔案路徑可以是絕對或相對路徑。 此檔案的每一行應包含一個 VM 名稱/IP 位址。 檔案中指定的虛擬機器名稱應該與 vCenter Server/vSphere ESXi 主機上的 VM 名稱相同。<br>例如，VMList.txt 檔案包含下列 VM︰<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
| -NoOfDaysToProfile | 要執行剖析的天數。 建議您執行剖析 15 天以上，以確保觀察到指定期間內您環境中的工作負載模式，並用來提供精確的建議。 |
| -Directory | (選用) 用來儲存剖析期間所產生之剖析資料的通用命名慣例 (UNC) 或本機目錄路徑。 如果未指定目錄名稱，目前路徑下名為 'ProfiledData' 的目錄將會做為預設目錄。 |
| -Password | (選用) 用來連線至 vCenter Server/vSphere ESXi 主機的密碼。 如果現在未指定密碼，系統將會在命令執行時提示您輸入密碼。|
| -StorageAccountName | (選用) 儲存體帳戶名稱，用於找出從內部部署至 Azure 的資料複寫可達成的輸送量。 此工具會將測試資料上傳到此儲存體帳戶，以計算輸送量。|
| -StorageAccountKey | (選用) 用來存取儲存體帳戶的儲存體帳戶金鑰。 移至 Azure 入口網站 > 儲存體帳戶 > <儲存體帳戶名稱> > 設定 > 存取金鑰 > Key1 (或傳統儲存體帳戶的主要存取金鑰)。 |
| -Environment | (選擇性) 這是您的目標 Azure 儲存體帳戶環境。 可以是下列三個值之一 - AzureCloud、AzureUSGovernment、AzureChinaCloud。 預設值為 AzureCloud。 當目標 Azure 區域是 Azure US Government 或 Azure China 雲端時，請使用此參數。 |


我們建議至少剖析您的 VM 15 到 30 天。 在剖析期間，ASRDeploymentPlanner.exe 會持續執行。 此工具會採用剖析階段輸入 (以天為單位)。 如果您想要剖析數小時或數分鐘的時間，以便進行工具的快速測試，在公開預覽版本中，您必須將時間轉換成相等的天數量值。 例如，若要剖析 30 分鐘，輸入必須是 30/(60*24) = 0.021 天。 允許的最小剖析時間為 30 分鐘。

在剖析期間，您可以選擇性地傳送儲存體帳戶名稱和金鑰，以尋找 Site Recovery 可在從組態伺服器或處理序伺服器複寫至 Azure 時達成的輸送量。 如果未在剖析期間傳送儲存體帳戶名稱和金鑰，此工具就不會計算可達成的輸送量。

您可以針對不同組的 VM 執行多個工具執行個體。 確保 VM 名稱不會在任何剖析集中重複出現。 例如，若已剖析 10 部 VM (VM1 到 VM10)，而在幾天後您想要剖析另外 5 部 VM (VM11 到 VM15)，您可以針對第二組的 VM (VM11 到 VM15) 從另一個命令列主控台執行此工具。 確保第二組的 VM 沒有任何來自第一個剖析執行個體的 VM 名稱，或您使用不同的輸出目錄進行第二次執行。 如有兩個工具執行個體用於剖析相同的 VM 並使用相同的輸出目錄，所產生的報告則會不正確。

在剖析作業開始時會擷取一次 VM 組態，並儲存在名為 VMDetailList.xml 的檔案。 產生報告時會使用此資訊。 從剖析開始到結尾，不會擷取任何 VM 組態變更 (例如，增加的核心、磁碟或 NIC 數目)。 如果有剖析的 VM 組態在剖析過程中發生變更，在公開預覽版本中，以下是在產生報告時取得最新 VM 詳細資料的因應措施：

* 備份 VMdetailList.xml，然後從其目前的位置刪除此檔案。
* 在報告產生時傳遞 -User 和 -Password 引數。

剖析命令會在剖析目錄中產生數個檔案。 請勿刪除任何檔案，因為這麼做會影響報告產生。

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>範例 1︰剖析 VM 30 天，並找出從內部部署至 Azure 的輸送量
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>範例 2︰剖析 VM 15 天

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-1-hour-for-a-quick-test-of-the-tool"></a>範例 3：剖析 VM 1 小時以便快速測試工具
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  0.04  -User vCenterUser1
```

>[!NOTE]
>
>* 如果此工具執行所在的伺服器已重新啟動或已當機，或如果您使用 Ctrl + C 關閉工具，則會保留剖析的資料。 不過，過去 15 分鐘的剖析資料有可能會遺失。 在這種情況下，請在伺服器開始備份之後，於剖析模式中重新執行此工具。
>* 傳遞儲存體帳戶名稱和金鑰時，此工具會在剖析的最後一個步驟測量輸送量。 如果此工具在剖析完成前關閉，則不會計算輸送量。 若要在產生報告之前找到輸送量，您可以從命令列主控台執行 GetThroughput 作業。 否則，產生的報告不會包含輸送量資訊。


## <a name="generate-a-report"></a>產生報告
此工具會產生啟用巨集的 Microsoft Excel 檔案 (XLSM) 做為報告輸出，其中摘要說明所有的部署建議。 此報告的名稱為 DeploymentPlannerReport_<唯一數值識別碼>.xlsm 且置於指定的目錄中。

剖析完成後，您可以在報告產生模式中執行工具。 下表包含要在報告產生模式中執行之必要和選用工具參數的清單。

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|參數名稱 | 說明 |
|-|-|
| -Operation | GenerateReport |
| -Server |  將產生報告之已剖析 VM 所在的 vCenter/vSphere Server 完整網域名稱或 IP 位址 (使用您在剖析時所用的相同名稱或 IP 位址)。 請注意，如果您在剖析時使用 vCenter Server，則無法使用 vSphere Server 產生報告，反之亦然。|
| -VMListFile | 包含要產生報告之已剖析 VM 清單的檔案。 此檔案路徑可以是絕對或相對路徑。 此檔案的每一行應包含一個 VM 名稱或 IP 位址。 檔案中指定的 VM 名稱應該與 vCenter Server/vSphere ESXi 主機上的 VM 名稱相同，並符合剖析期間所用的名稱。|
| -Directory | (選用) 儲存剖析資料 (剖析期間產生的檔案) 的 UNC 或本機目錄路徑。 產生報告時需要這項資料。 如未指定，將會使用 ‘ProfiledData’ 目錄。 |
| -GoalToCompleteIR | (選用) 必須完成已剖析 VM 之初始複寫的時數。 所產生的報告會提供可以在指定的時間內完成初始複寫的 VM 數目。 預設值為 72 小時。 |
| -User | (選用) 用於連線至 vCenter/vSphere Server 的使用者名稱。 此名稱用來擷取 VM 的最新組態資訊，例如磁碟數目、核心數目和 NIC 數目)，以使用於報告中。 如未提供此名稱，則會使用在剖析開始時收集的組態資訊。 |
| -Password | (選用) 用來連線至 vCenter Server/vSphere ESXi 主機的密碼。 如果未將此密碼指定為一個參數，系統將會在命令執行時提示您稍後輸入密碼。 |
| -DesiredRPO | (選用) 以分鐘為單位的所需復原點目標。 預設值是 15 分鐘。|
| -Bandwidth | 頻寬 (以 Mbps 為單位)。 此參數用來計算指定的頻寬可達成的 RPO。 |
| -StartDate | (選用) 採用 MM-DD-YYYY:HH:MM 格式 (24 小時制) 的開始日期和時間。 StartDate 必須與 EndDate 一起指定。 若已指定 StartDate，則會針對在 StartDate 與 EndDate 之間收集的剖析資料產生報告。 |
| -EndDate | (選用) 採用 MM-DD-YYYY:HH:MM 格式 (24 小時制) 的結束日期和時間。 EndDate 必須與 StartDate 一起指定。 若已指定 EndDate，則會針對在 StartDate 與 EndDate 之間收集的剖析資料產生報告。 |
| -GrowthFactor | (選用) 以百分比表示的成長因子。 預設值為 30%。 |
| -UseManagedDisks | (選擇性) UseManagedDisks - 是/否。 預設值為 [是]。 計算可以放入單一儲存體帳戶的虛擬機器數目時，請考慮在受控磁碟而不是非受控磁碟上進行虛擬機器的容錯移轉/測試容錯移轉。 |

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>範例 1︰當剖析的資料位於本機磁碟機時，使用預設值來產生報告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>範例 2︰當剖析的資料位於遠端伺服器時產生報告
您應具備遠端目錄的讀取/寫入存取權。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>範例 3︰使用特定頻寬和目標來產生報告，以在指定的時間內完成 IR
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>範例 4︰使用 5% (而非預設值 30%) 的成長因子來產生報告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>範例 5︰使用剖析資料子集來產生報告
例如，您有 30 天的剖析資料，而只想要產生 20 天的報告。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>範例 6：針對 5 分鐘 RPO 產生報告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
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
* 結果在 VM 上增加的變換會要求 VM 移至進階儲存體，Site Recovery 複寫才可以保持一致。
* 因此，您必須對進階儲存體帳戶停用並重新啟用保護。

強烈建議您在規劃部署期間規劃成長，而預設值為 30%。 您最瞭解您的應用程式使用量模式和成長預測，而且可以在產生報告時相應變更此數字。 此外，您可以使用不同的成長因子，針對相同的剖析資料產生多份報告，判斷哪些目標儲存體和來源頻寬建議最適合您。

產生的 Microsoft Excel 報告包含下列資訊：

* [輸入](site-recovery-deployment-planner.md#input)
* [建議](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [建議頻寬輸入](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->儲存體放置](site-recovery-deployment-planner.md#vm-storage-placement)
* [相容的 VM](site-recovery-deployment-planner.md#compatible-vms)
* [不相容的 VM](site-recovery-deployment-planner.md#incompatible-vms)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)

## <a name="get-throughput"></a>取得輸送量

若要預估 Site Recovery 在複寫期間可以達成的輸送量 (從內部部署至 Azure)，請以 GetThroughput 模式執行工具。 此工具會計算來自工具執行所在伺服器的輸送量。 在理想情況下，此伺服器是以組態伺服器調整大小指南為基礎。 如果您已在內部部署環境部署 Site Recovery 基礎結構元件，請在組態伺服器上執行此工具。

開啟命令列主控台，然後移至 Site Recovery 部署規劃工具資料夾。 使用下列參數執行 ASRDeploymentPlanner.exe。

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|參數名稱 | 說明 |
|-|-|
| -Operation | GetThroughput |
| -Directory | (選用) 儲存剖析資料 (剖析期間產生的檔案) 的 UNC 或本機目錄路徑。 產生報告時需要這項資料。 如未指定目錄，則會使用 ‘ProfiledData’ 目錄。 |
| -StorageAccountName | 儲存體帳戶名稱，用於找出從內部部署至 Azure 的資料複寫所耗用的頻寬。 此工具會將測試資料上傳到此儲存體帳戶，以找出所耗用的頻寬。 |
| -StorageAccountKey | 用來存取儲存體帳戶的儲存體帳戶金鑰。 移至 Azure 入口網站 > 儲存體帳戶 > <儲存體帳戶名稱> > 設定 > 存取金鑰 > Key1 (或傳統儲存體帳戶的主要存取金鑰)。 |
| -VMListFile | 包含要剖析之 VM 清單的檔案，以便計算所耗用的頻寬。 此檔案路徑可以是絕對或相對路徑。 此檔案的每一行應包含一個 VM 名稱/IP 位址。 檔案中指定的 VM 名稱應該與 vCenter Server/vSphere ESXi 主機上的 VM 名稱相同。<br>例如，VMList.txt 檔案包含下列 VM︰<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | (選擇性) 這是您的目標 Azure 儲存體帳戶環境。 可以是下列三個值之一 - AzureCloud、AzureUSGovernment、AzureChinaCloud。 預設值為 AzureCloud。 當目標 Azure 區域是 Azure US Government 或 Azure China 雲端時，請使用此參數。 |

此工具會在指定的目錄中建立數個 64MB asrvhdfile<#>.vhd 檔案 (其中 # 是檔案數目)。 此工具會將這些檔案上傳至儲存體帳戶，以找出輸送量。 測量輸送量之後，此工具會從儲存體帳戶和本機伺服器中刪除所有這類檔案。 如果此工具在計算輸送量時因為任何原因而終止，它不會從儲存體或本機伺服器中刪除檔案。 您必須手動加以刪除。

輸送量會在指定的時間點進行測量，而這是 Site Recovery 可以在複寫期間內達成的最大輸送量 (前提是所有其他因子維持不變)。 例如，如果任何應用程式開始在相同網路上耗用更多頻寬，則複寫期間的實際輸送量會有所不同。 如果您是從組態伺服器執行 GetThroughput 命令，此工具不會留意任何受保護的 VM 和進行中的複寫作業。 如果 GetThroughput 作業在受保護的 VM 有高度資料變換時執行，則測量的輸送量結果會不同。 建議在剖析期間的各種時間點執行此工具，以了解可在不同時間達成的輸送量等級。 在報告中，此工具會顯示最後測量的輸送量。

### <a name="example"></a>範例
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> 在具有與組態伺服器相同之儲存體和 CPU 特性的伺服器上執行此工具。
>
> 如需複寫，請設定建議的頻寬，以符合當時的 100% RPO。 在設定適當的頻寬之後，如果您未看到此工具所報告的達成輸送量有任何增加，請執行下列作業︰
>
>  1. 檢查以判斷是否有任何網路服務品質 (QoS) 會限制 Site Recovery 輸送量。
>
>  2. 檢查以判斷 Site Recovery 保存庫是否位於支援的最近實體 Microsoft Azure 區域，以縮小網路延遲。
>
>  3. 檢查本機儲存體特性，以判斷是否可以改善硬體 (例如 HDD 變成 SSD)。
>
>  4. 變更處理序伺服器中的 Site Recovery 設定，以[增加用於複寫的網路頻寬](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)。

## <a name="recommendations-with-desired-rpo-as-input"></a>建議以所需的 RPO 做為輸入

### <a name="profiled-data"></a>剖析的資料

![Deployment Planner 中已剖析的資料檢視](./media/site-recovery-deployment-planner/profiled-data-period.png)

**剖析的資料期間**：執行剖析的期間。 根據預設，此工具會在計算中包含所有剖析的資料，除非在報告產生期間使用 StartDate 和 EndDate 選項來針對特定期間產生此報告。

**伺服器名稱**：為其產生 VM 報告之 VMware vCenter 或 ESXi 主機的名稱或 IP 位址。

**所需的 RPO**：您部署的復原點目標。 預設會計算 RPO 值 15、30 和 60 分鐘所需的網路頻寬。 根據選取項目，在工作表上更新受影響的值。 如果您在產生報告時使用了 DesiredRPOinMin 參數，該值會顯示於所需 RPO 結果中。

### <a name="profiling-overview"></a>剖析概觀

![Deployment Planner 中的剖析結果](./media/site-recovery-deployment-planner/profiling-overview.png)

**剖析的虛擬機器總數**：可取得其剖析資料的 VM 總數。 如果 VMListFile 包含任何未剖析的 VM 名稱，則這些 VM 不會納入報告產生考量並從剖析的 VM 總計數中排除。

**相容的虛擬機器**：可以使用 Site Recovery 受 Azure 保護的 VM 數目。 這是相容的 VM 總數，系統會計算其所需的網路頻寬、儲存體帳戶數目、Azure 核心數目以及組態伺服器和額外處理序伺服器數目。 在 [相容 VM] 區段中可取得每部相容 VM 的詳細資料。

**不相容的虛擬機器**：可使用 Site recovery 保護之不相容的剖析 VM 數目。 「不相容的 VM」一節會提到不相容的原因。 如果 VMListFile 包含任何未剖析 VM 的名稱，則這些 VM 會從不相容的 VM 計數中排除。 這些 VM 會在「不相容的 VM」區段結尾列為「找不到資料」。

**所需 RPO**：以分鐘為單位的所需復原點目標。 此報告會針對三個 RPO 值而產生：15 (預設值)、30 和 60 分鐘。 報告中的頻寬建議會隨著您在工作表右上方的 [所需的 RPO] 下拉式清單中選取的項目變更。 如果您已使用 -DesiredRPO 參數以自訂值產生報告，這個自訂值將會顯示為 [所需的 RPO] 下拉式清單中的預設值。

### <a name="required-network-bandwidth-mbps"></a>所需的網路頻寬 (Mbps)

![Deployment Planner 中所需的網路頻寬](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**符合當時的 100% RPO︰**所要配置的建議頻寬 (以 Mbps 為單位)，以符合當時所需的 100% RPO。 此頻寬量必須專用於您所有相容 VM 的穩定狀態差異複寫，以避免任何 RPO 違規。

**符合當時的 90% RPO**︰如果因為寬頻價格或任何其他原因，您無法設定符合當時所需 100% RPO 所需的頻寬，您可以選擇設定較低的頻寬，以符合當時所需的 90% RPO。 若要了解設定此低頻寬的含意，報告提供了可預期之 RPO 違規次數和持續時間的假設分析。

**達成的輸送量**：來自您已對儲存體帳戶所在 Microsoft Azure 區域執行 GetThroughput 命令之伺服器的輸送量。 此輸送量指出當您使用 Site Recovery 保護相容 VM 時可達成的預估等級，前提是您的組態伺服器或處理序伺服器儲存體和網路特性仍與您執行此工具的伺服器相同。

如需複寫，您應該設定建議的頻寬，以符合當時的 100% RPO。 在設定頻寬之後，如果您未看到此工具所報告的達成輸送量有任何增加，請執行下列作業︰

1. 查看是否有任何網路服務品質 (QoS) 會限制 Site Recovery 輸送量。

2. 查看 Site Recovery 保存庫是否位於支援的最近實體 Microsoft Azure 區域，以縮小網路延遲。

3. 檢查本機儲存體特性，以判斷是否可以改善硬體 (例如 HDD 變成 SSD)。

4. 變更處理序伺服器中的 Site Recovery 設定，以[增加用於複寫的網路頻寬](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)。

如果您在已有受保護 VM 的組態伺服器或處理序伺服器上執行此工具，請執行此工具數次。 視當時正在處理的變換數量而定，達成的輸送量數字會隨之改變。

對於所有企業 Site Recovery 部署，建議使用 [ExpressRoute](https://aka.ms/expressroute)。

### <a name="required-storage-accounts"></a>所需的儲存體帳戶
下列圖表顯示保護所有相容 VM 所需的儲存體帳戶 (標準和進階) 總數。 若要了解每個 VM 所要使用的儲存體帳戶，請參閱「VM 儲存體放置」一節。

![Deployment Planner 中所需的儲存體帳戶](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

### <a name="required-number-of-azure-cores"></a>所需的 Azure 核心數目
此結果是在所有相容 VM 的容錯移轉或測試容錯移轉之前所要設定的核心總數。 如果訂用帳戶中可用的核心太少，Site Recovery 便無法在測試容錯移轉或容錯移轉時建立 VM。

![Deployment Planner 中所需的 Azure 核心數目](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

### <a name="required-on-premises-infrastructure"></a>所需的內部部署基礎結構
此數字是為了足以保護所有相容的 VM，所要設定的組態伺服器與額外處理序伺服器總數。 視支援的[組態伺服器大小建議](https://aka.ms/asr-v2a-on-prem-components)而定，此工具可能會建議額外的伺服器。 建議是以每日變換或受保護 VM 數目上限中的較大者為基礎 (假設每部 VM 平均有三個磁碟)，無論在組態伺服器或額外處理序伺服器上何者先達到。 您可在「輸入」一節中找到每日變換總計和受保護磁碟總數的詳細資料。

![Deployment Planner 中所需的內部部署基礎結構](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

### <a name="what-if-analysis"></a>假設分析
此分析概述當您設定較低的頻寬，讓所需的 RPO 只符合當時的 90% 時，在剖析期間可發生多少次違規。 任何指定的日期都可能發生一或多個 RPO 違規。 此圖會顯示當天的尖峰 RPO。
根據這項分析，您可以判斷指定的較低頻寬是否可接受所有天數的 RPO 違規次數和每日的尖峰 RPO 目標達成。 如果可接受，您可以配置較低的頻寬進行複寫，否則依照建議配置較高的頻寬，以符合當時所需的 100% RPO。

![Deployment Planner 中的假設分析](./media/site-recovery-deployment-planner/what-if-analysis.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>初始複寫的建議 VM 批次大小
在這一節中，我們會建議可平行保護的 VM 數目，以使用建議的頻寬在 72 小時內完成初始複寫，進而符合設定階段所需的 100% RPO。 此值是可設定的值。 若要在產生報告階段變更此值，請使用 *GoalToCompleteIR* 參數。

下面的圖形顯示各種頻寬值和計算的 VM 批次大小計數，以根據在所有相容 VM 中偵測到的平均 VM 大小，在 72 小時內完成初始複寫。

在公開預覽版本中，報告不會指定哪些 VM 應包含在某個批次中。 您可以使用「相容的 VM」區段中顯示的磁碟大小，找出每部 VM 的大小並選取某個批次的 VM，或根據已知的工作負載特性選取 VM。 初始複寫的完成時間會根據實際 VM 磁碟大小、已用的磁碟空間和可用的網路輸送量按比例變更。

![建議的 VM 批次大小](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

### <a name="growth-factor-and-percentile-values-used"></a>使用的成長因子和百分位數值
位於工作表底部的這個區段會顯示用於剖析 VM 之所有效能計數器的百分位數值 (預設值為第 95 個百分位數)，以及所有計算中使用的成長因子 (預設值為 30%)。

![使用的成長因子和百分位數值](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>以可用頻寬做為輸入的建議

![以可用頻寬做為輸入的建議](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

您可能會遇到一下情況：您知道無法針對 Site Recovery 複寫設定超過 x Mbps 的頻寬。 此工具可讓您輸入可用的頻寬 (在報告產生期間使用 -Bandwidth 參數)，並取得可達成的 RPO (以分鐘為單位)。 使用此可達成的 RPO 值，您可以決定是否需要設定額外的頻寬，或可接受具有此 RPO 的災害復原解決方案。

![500 Mbps 頻寬可達成的 RPO](./media/site-recovery-deployment-planner/achievable-rpos.png)

## <a name="input"></a>輸入
[輸入] 工作表提供已剖析的 VMware 環境概觀。

![已剖析的 VMware 環境概觀](./media/site-recovery-deployment-planner/Input.png)

[開始日期] 和 [結束日期]：考慮用於產生報告之剖析資料的開始和結束日期。 根據預設，開始日期是剖析開始的日期，而結束日期是剖析停止的日期。 如果使用這些參數產生報告，這可以是 'StartDate' 和 'EndDate' 值。

**剖析總天數**：為其產生報告之開始和結束日期之間的剖析總天數。

**相同的虛擬機器數目**：相容的 VM 總數，系統會計算其所需的網路頻寬、所需的儲存體帳戶、Microsoft Azure 核心及組態伺服器和額外處理序伺服器的數目。

**所有相容虛擬機器的磁碟總數**：此數字可做為其中一個輸入值，以決定部署中所要使用的組態伺服器與額外處理序伺服器數目。

**每部相容虛擬機器的平均磁碟數**：在所有相容 VM 中計算的平均磁碟數。

**平均磁碟大小 (GB)**：在所有相容 VM 中計算的平均磁碟大小。

**所需的 RPO (分鐘)**：預設復原點目標，或在產生報告時針對 'DesiredRPO' 參數所傳遞的值，用來估計所需的頻寬。

**所需的頻寬 (Mbps)**：在產生報告時針對 ‘Bandwidth’ 參數所傳遞的值，用來估計可用的 RPO。

**每日觀察到的典型資料變換 (GB)**：在所有剖析天數中觀察到的平均資料變換。 此數字可做為其中一個輸入值，以決定部署中所要使用的組態伺服器與額外處理序伺服器數目。


## <a name="vm-storage-placement"></a>VM 儲存體放置

![VM 儲存體放置](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**磁碟儲存體類型**：標準或進階儲存體帳戶，用來複寫 [要放置的 VM] 資料行中提到的所有對應 VM。

**建議的前置詞**：建議的三個字元前置詞，可用來命名儲存體帳戶。 您可以使用自己的前置詞，但工具的建議會遵循[儲存體帳戶的磁碟分割命名慣例](https://aka.ms/storage-performance-checklist)。

**建議的帳戶名稱**：在您包含建議前置詞後的儲存體帳戶名稱。 以您的自訂輸入取代括弧內的名稱 (< 和 >)。

**記錄儲存體帳戶**︰所有複寫記錄都會儲存在標準儲存體帳戶中。 對於複寫至進階儲存體帳戶的 VM，請針對記錄儲存體設定額外的標準儲存體帳戶。 多個進階複寫儲存體帳戶可以使用單一標準記錄儲存體帳戶。 複寫到標準儲存體帳戶的 VM 會對記錄使用相同的儲存體帳戶。

**建議的記錄帳戶名稱**：在您包含建議前置詞後的儲存體記錄帳戶名稱。 以您的自訂輸入取代括弧內的名稱 (< 和 >)。

**放置摘要**：提供在複寫和測試容錯移轉或容錯移轉時，儲存體帳戶上載入的 VM 總數摘要。 其中包含對應到儲存體帳戶的 VM 總數、置於此儲存體帳戶中所有 VM 的讀/寫 IOPS 總計、寫入 (複寫) IOPS 總計、所有磁碟的設定大小總計，以及磁碟總數。

**要放置的虛擬機器**：應置於指定之儲存體帳戶的所有 VM 清單，以提供最佳效能和使用情況。

## <a name="compatible-vms"></a>相容的 VM
![相容 VM 的 Excel 試算表](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM 名稱**：產生報告時，使用於 VMListFile 的 VM 名稱或 IP 位址。 此資料行也會列出連結至 VM 的磁碟 (VMDK)。 為了區分具有重複名稱或 IP 位址的 vCenter VM，名稱中包含 ESXi 主機名稱。 所列的 ESXi 主機是此工具在剖析期間探索到 VM 時其所在位置的主機。

**VM 相容性**：值為 [是] 和 [是\*]。 **[是\*]** 適用於 VM 適合於 [Azure 進階儲存體](https://aka.ms/premium-storage-workload)的情況。 在此情況下，剖析的高變換 / IOPS 磁碟符合 P20 或 P30 類別，但磁碟大小會導致它向下對應至 P10 或 P20。 儲存體帳戶會根據磁碟大小，決定磁碟所要對應至的進階儲存體大小類型。 例如：
* <128 GB 為 P10。
* 128 GB 至 512 GB 為 P20。
* 512 GB 至 1024 GB 為 P30。
* 1025 GB 至 2048 GB 為 P40。
* 2049 GB 至 4095 GB 為 P50。

如果磁碟的工作負載特性讓它放在 P20 或 P30 類別中，但大小會將它對應到較低的進階儲存體磁碟類型，此工具會將該 VM 標示為 [是\*]。 此工具也建議您變更來源磁碟大小，以符合建議的進階儲存體磁碟類型，或變更容錯移轉後的目標磁碟類型。

**儲存體類型**：標準或進階。

**建議的前置詞**：三個字元的儲存體帳戶前置詞。

**儲存體帳戶**︰使用建議儲存體帳戶前置詞的名稱。

**R/W IOPS (含成長因子)**：磁碟上的尖峰工作負載讀/寫 IOPS (預設值為第 95 個百分位數)，包括未來的成長因子 (預設值為 30%)。 請注意，VM 的讀/寫 IOPS 總計不一定是 VM 個別磁碟的讀/寫 IOPS 總和，因為 VM 的尖峰讀/寫 IOPS 是其個別磁碟在剖析期間內每一分鐘之讀/寫 IOPS 總和的尖峰。

**資料變換 (Mbps) (含成長因子)**：磁碟上的尖峰變換率 (預設值為第 95 個百分位數)，包括未來的成長因子 (預設值為 30%)。 請注意，VM 的資料變換總計不一定是 VM 個別磁碟的資料變換總和，因為 VM 的尖峰資料變換是其個別磁碟在剖析期間每一分鐘之變換總和的尖峰。

**Azure VM 大小**：此內部部署 VM 理想的對應 Azure 雲端服務虛擬機器大小。 對應是以內部部署 VM 的記憶體、磁碟/核心/NIC 數目和讀/寫 IOPS 為基礎。 建議一律是符合所有內部部署 VM 特性的最低 Azure VM 大小。

**磁碟數目**：VM 上的虛擬機器磁碟 (VMDK) 總數。

**磁碟大小 (GB)**：VM 之所有磁碟的總設定大小。 此工具也會顯示 VM 中個別磁碟的磁碟大小。

**核心**：VM 上的 CPU 核心數目。

**記憶體 (MB)**：VM 上的 RAM。

**NIC**：VM 上的 NIC 數目。

**開機類型**︰這是 VM 的開機類型。 可以是 BIOS 或 EFI。 Azure Site Recovery 目前僅支援 BIOS 開機類型。 EFI 開機類型的所有虛擬機器會列在不相容的 VM 工作表中。

**OS 類型**：這是 VM 的 OS 類型。 可以是 Windows 或 Linux 或其他。

## <a name="incompatible-vms"></a>不相容的 VM

![不相容 VM 的 Excel 試算表](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM 名稱**：產生報告時，使用於 VMListFile 的 VM 名稱或 IP 位址。 此資料行也會列出連結至 VM 的磁 VMDK。 為了區分具有重複名稱或 IP 位址的 vCenter VM，名稱中包含 ESXi 主機名稱。 所列的 ESXi 主機是此工具在剖析期間探索到 VM 時其所在位置的主機。

**VM 相容性**：指出為何指定的 VM 不適合與 Site Recovery 搭配使用。 相關原因會針對 VM 的每個不相容磁碟進行說明，且根據發佈的[儲存體限制](https://aka.ms/azure-storage-scalbility-performance)，原因可能是下列其中一項：

* 磁碟大小 >4095 GB。 Azure 儲存體目前不支援大於 4095 GB 的資料磁碟大小。
* OS 磁碟 >2048 GB。 Azure 儲存體目前不支援大於 2048 GB 的 OS 磁碟大小。
* 啟動類型為 EFI。 Azure Site Recovery 目前僅支援 BIOS 開機類型虛擬機器。

* VM 大小總計 (複寫 + TFO) 超過支援的儲存體帳戶大小限制 (35 TB)。 當 VM 中單一磁碟的效能特性超過標準儲存體支援的最大 Azure 或 Site Recovery 限制，通常會發生此不相容情況。 這類情況會將 VM 推送到進階儲存體區域中。 不過，進階儲存體帳戶支援的大小上限為 35 TB，而單一的受保護 VM 無法跨多個儲存體帳戶受到保護。 也請注意，在受保護的 VM 上執行測試容錯移轉時，它會在正在進行複寫的相同儲存體帳戶中執行。 在此例中，設定 2 倍的磁碟大小，以便進行複寫並以平行方式繼續進行測試容錯移轉。
* 來源 IOPS 超過支援的儲存體 IOPS 限制 (每個磁碟 5000)。
* 來源 IOPS 超過支援的儲存體 IOPS 限制 (每個 VM 80,000)。
* 平均資料變換超出磁碟平均 IO 大小支援的 Site Recovery 資料變換限制 10 MBps。
* VM 上所有磁碟的資料變換總計超過每個 VM 支援的 Site Recovery 資料變換限制 54 MBps。
* 磁碟的平均有效寫入 IOPS 超過支援的 Site Recovery IOPS 限制 840。
* 計算的快照集儲存體超過支援的快照集儲存體限制 10 TB。

**R/W IOPS (含成長因子)**：磁碟上的尖峰工作負載 IOPS (預設值為第 95 個百分位數)，包括未來的成長因子 (預設值為 30%)。 請注意，VM 的讀/寫 IOPS 總計不一定是 VM 個別磁碟的讀/寫 IOPS 總和，因為 VM 的尖峰讀/寫 IOPS 是其個別磁碟在剖析期間內每一分鐘之讀/寫 IOPS 總和的尖峰。

**資料變換 (Mbps) (含成長因子)**：磁碟上的尖峰變換率 (預設值為第 95 個百分位數)，包括未來的成長因子 (預設值為 30%)。 請注意，VM 的資料變換總計不一定是 VM 個別磁碟的資料變換總和，因為 VM 的尖峰資料變換是其個別磁碟在剖析期間每一分鐘之變換總和的尖峰。

**磁碟數目**：VM 上的 VMDK 總數。

**磁碟大小 (GB)**：VM 之所有磁碟的總設定大小。 此工具也會顯示 VM 中個別磁碟的磁碟大小。

**核心**：VM 上的 CPU 核心數目。

**記憶體 (MB)**：VM 上的 RAM 數量。

**NIC**：VM 上的 NIC 數目。

**開機類型**︰這是 VM 的開機類型。 可以是 BIOS 或 EFI。 Azure Site Recovery 目前僅支援 BIOS 開機類型。 EFI 開機類型的所有虛擬機器會列在不相容的 VM 工作表中。

**OS 類型**：這是 VM 的 OS 類型。 可以是 Windows 或 Linux 或其他。


## <a name="site-recovery-limits"></a>Site Recovery 限制

**複寫儲存體目標** | **平均來源磁碟 I/O 大小** |**平均來源磁碟資料變換** | **每日的來源磁碟資料變換總計**
---|---|---|---
標準儲存體 | 8 KB | 2 MBps | 每個磁碟 168 GB
進階 P10 磁碟 | 8 KB | 2 MBps | 每個磁碟 168 GB
進階 P10 磁碟 | 16 KB | 4 MBps | 每個磁碟 336 GB
進階 P10 磁碟 | 32 KB 或更大 | 8 MBps | 每個磁碟 672 GB
進階 P20 或 P30 磁碟 | 8 KB  | 5 MBps | 每個磁碟 421 GB
進階 P20 或 P30 磁碟 | 16 KB 或更大 |10 MBps | 每個磁碟 842 GB

以上是採用 30% I/O 重疊時的平均數字。 Site Recovery 能夠處理更高的輸送量 (以重疊比為基礎)、較大的寫入大小和實際工作負載 I/O 行為。 先前數字採用大約五分鐘的典型積壓。 也就是說，資料上傳之後，便會進行處理並在五分鐘內建立復原點。

上述限制是以我們的測試為基礎，但無法涵蓋所有可能的應用程式 I/O 組合。 實際的結果會隨著您的應用程式 I/O 混合而有所不同。 為了獲得最佳結果，即使在部署規劃之後，仍一律建議使用測試容錯移轉來執行廣泛的應用程式測試，以了解真正的效能情況。

## <a name="updating-the-deployment-planner"></a>更新 Deployment Planner
若要更新 Deployment Planner，請執行下列作業︰

1. 下載最新版的 [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)。

2. 將 .zip 資料夾複製到您要執行所在的伺服器。

3. 將 .zip 資料夾解壓縮。

4. 執行下列其中一個動作：
 * 如果最新版本不包含剖析修正程式，並已在您目前的規劃工具版本上進行剖析，則會繼續執行剖析。
 * 如果最新版本包含剖析修正程式，則建議您在目前的版本上停止剖析，並使用新版本重新開始剖析。

  >[!NOTE]
  >
  >當您開始使用新版本進行剖析時，請傳遞相同的輸出目錄路徑，以便工具在現有檔案上附加剖析資料。 將使用一組完整的剖析資料來產生報告。 如果您傳遞不同的輸出目錄，則會建立新檔案，舊的剖析資料不會用來產生報告。
  >
  >每個新的 Deployment Planner 都是 .zip 檔的累積更新。 您不需要將最新的檔案複製到先前的資料夾。 您可以建立及使用新的資料夾。


## <a name="version-history"></a>版本歷程記錄

### <a name="131"></a>1.3.1
更新日期：2017 年 7 月 19 日

已新增下列新功能︰

* 在報告產生階段新增了大型磁碟 (> 1TB) 的支援。 您現在可以使用部署規劃工具，規劃磁碟大小大於 1 TB (最多 4095 GB) 之虛擬機器的複寫。
深入了解 [Azure Site Recovery 中的大型磁碟支援](https://azure.microsoft.com/en-us/blog/azure-site-recovery-large-disks/)


### <a name="13"></a>1.3
更新日期：2017 年 5 月 9 日

已新增下列新功能︰

* 在產生報告中新增了受控磁碟支援。 根據是否已針對容錯移轉/測試容錯移轉選取受控磁碟，計算可以放到單一儲存體帳戶的虛擬機器數目。        


### <a name="12"></a>1.2
更新日期：2017 年 4 月 7 日

已新增下列修正程式︰

* 已新增每部虛擬機器的開機類型 (BIOS 或 EFI) 檢查，用以判斷虛擬機器是否與保護相容。
* 已在相容的 VM 和不相容的 VM 工作表中新增每部虛擬機器的 OS 類型資訊。
* US Government 和 China Microsoft Azure 區域現在支援 GetThroughput 作業。
* 已針對 vCenter 和 ESXi 伺服器新增更多必要條件檢查。
* 地區設定若為非英文，則會產生不正確的報告。


### <a name="11"></a>1.1
更新日期：2017 年 3 月 9 日

修正下列問題：

* 如果 vCenter 在各種 ESXi 主機上有兩部或多部 VM 具有相同的名稱或 IP 位址，此工具就無法剖析 VM。
* 「相容的 VM」和「不相容的 VM」工作表已停用複製和搜尋。

### <a name="10"></a>1.0
更新日期：2017 年 2 月 23 日

Azure Site Recovery Deployment Planner 公開預覽版本 1.0 具有下列已知問題 (將會在即將推出的更新中獲得解決)：

* 此工具僅適用於 VMware 到 Azure 案例，而不適用於 Hyper-V 到 Azure 部署。 對於 Hyper-V 到 Azure 案例，使用 [Hyper-V 容量規劃工具](./site-recovery-capacity-planning-for-hyper-v-replication.md)。
* 美國政府和中國 Microsoft Azure 區域不支援 GetThroughput 作業。
* 如果 vCenter Server 在各種 ESXi 主機上有兩部或多部 VM 具有相同的名稱或 IP 位址，此工具就無法剖析 VM。 在此版本中，此工具會略過 VMListFile 中重複 VM 名稱或 IP 位址的剖析。 因應措施是使用 ESXi 主機 (而非 vCenter Server) 剖析 VM。 您必須為每部 ESXi 主機執行一個執行個體。
