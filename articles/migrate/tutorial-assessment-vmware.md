---
title: "使用 Azure Migrate 探索及評估要移轉到 Azure 的內部部署 VMware VM | Microsoft Docs"
description: "說明如何使用 Azure Migrate 服務，探索及評估要移轉到 Azure 的內部部署 VMware VM。"
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a2521630-730f-4d8b-b298-e459abdced46
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 1c21364c3ff5cfb61866c912a699b722f2668607
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>探索及評估要移轉到 Azure 的內部部署 VMware VM

[Azure Migrate](migrate-overview.md) 服務會評估要移轉至 Azure 的內部部署工作負載。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 Azure Migrate 專案。
> * 設定內部部署收集器虛擬機器 (VM)，以探索要評估的內部部署 VMware VM。
> * 將 VM 分組並建立評估。


如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。


## <a name="prerequisites"></a>必要條件

- **VMware**：您必須至少有一個 VMware VM 位於執行 5.0 版或更新版本的 ESXi 主機或叢集上。 主機或叢集必須是由執行 5.5、6.0 或 6.5 版的 vCenter Server 所管理。
- **vCenter 帳戶**：您必須有唯讀帳戶，且此帳戶具有 vCenter Server 的系統管理員認證。 Azure Migrate 會使用此帳戶來探索 VM。
- **權限**：在 vCenter Server 上，您必須有權限可藉由匯入 .OVA 格式的檔案來建立 VM。 
- **統計資料設定**：vCenter Server 的統計資料設定應該先設為層級 3，再開始部署。 如果低於層級 3，還是能進行評估，但不會收集儲存體和網路的效能資料。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-project"></a>建立專案

1. 在 Azure 入口網站中，按一下 [建立資源]。
2. 搜尋 **Azure Migrate**，然後在搜尋結果中選取服務 (**Azure Migrate (預覽版)**)。 然後按一下 [ **建立**]。
3. 指定專案名稱，以及專案的 Azure 訂用帳戶。
4. 建立新的資源群組。
5. 指定要在其中建立專案的區域，然後按一下 [建立]。 從內部部署 VM 收集來的中繼資料會儲存在此區域中。 在這個預覽版中，您只能在「美國中西部」區域建立 Azure Migrate 專案。 不過，您可以評估不同位置的 VM。

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>下載收集器設備

Azure Migrate 會建立稱為「收集器設備」的內部部署 VM。 此 VM 會探索內部部署 VMware VM，並將其相關中繼資料傳送至 Azure Migrate 服務。 若要設定收集器設備，您可以下載 .OVA 檔案，並將它匯入內部部署 vCenter Server，以建立 VM。

1. 在 Azure Migrate 專案中，按一下 [使用者入門] > [探索及評估] > [探索機器]。
2. 在 [探索機器] 中，按一下 [下載] 以下載 .OVA 檔案。
3. 在 [複製專案認證] 中，複製專案識別碼和金鑰。 在設定收集器時，您會需要這些資料。

    ![下載 .ova 檔案](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>確認收集器設備

請先確認 .OVA 檔案是安全的，再進行部署。

1. 在檔案下載所在的電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 OVA 的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 產生的雜湊應符合這些設定。
    
    若為 OVA 1.0.8.38 版
    **演算法** | **雜湊值**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    若為 OVA 1.0.8.40 版
    **演算法** | **雜湊值**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>建立收集器 VM

將下載的檔案匯入 vCenter Server。

1. 在 vSphere 用戶端主控台中，按一下 [檔案] > [部署 OVF 範本]。

    ![部署 OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. 在 [部署 OVF 範本精靈] > [來源] 中，指定 .ova 檔案的位置。
3. 在 [名稱] 和 [位置] 中，指定收集器 VM 的易記名稱，以及 VM 要裝載所在的清查物件。
5. 在 [主機/叢集] 中，指定收集器 VM 要執行所在的主機或叢集。
7. 在儲存體中，指定收集器 VM 的儲存目的地。
8. 在 [磁碟格式] 中，指定磁碟類型和大小。
9. 在 [網路對應] 中，指定收集器 VM 所要連線的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure。 
10. 檢閱並確認設定，然後按一下 [完成]。

## <a name="run-the-collector-to-discover-vms"></a>執行收集器來探索 VM

1. 在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 [VM] > [開啟主控台]。
2. 提供設備的語言、時區和密碼喜好設定。
3. 在桌面上，按一下 [執行收集器] 捷徑。
4. 在 Azure Migrate 收集器中，開啟 [設定必要條件]。
    - 接受授權條款，並閱讀第三方資訊。
    - 收集器會確認 VM 是否能夠存取網際網路。
    - 如果 VM 能夠透過 Proxy 存取網際網路，請按一下 [Proxy 設定]，然後指定 Proxy 位址和接聽連接埠。 如果 Proxy 需要驗證，請指定認證。
    - 收集器會確認 Windows 分析工具服務是否有執行。 根據預設，收集器 VM 上會安裝此服務。
    - 下載並安裝 VMware PowerCLI。
. 在 [探索機器] 中，執行下列動作：
    - 指定 vCenter Server 的名稱 (FQDN) 或 IP 位址。
    - 在 [使用者名稱] 和 [密碼] 中，指定收集器要用來探索 vCenter Server 上之 VM 的唯讀帳戶認證。
    - 在 [收集範圍] 中，選取 VM 的探索範圍。 收集器只能探索指定範圍內的 VM。 範圍可以設定為特定資料夾、資料中心或叢集。 它不應該包含超過 1000 個 VM。 
    - 在 [用來分組的標籤類別] 中，選取 [無]。
1. 在 [選取專案] 中，指定您從入口網站所複製的 Azure Migrate 專案識別碼和金鑰。 如果您之前未複製，請從收集器 VM 開啟 Azure 入口網站。 在專案的 [概觀] 頁面中，按一下 [探索機器]，然後複製值。  
2. 在 [完成探索] 中監視探索，然後確認從 VM 收集到的中繼資料位於範圍內。 收集器會提供概略的探索時間。

> [!NOTE]
> 收集器只支援以「英文 (美國)」作為作業系統語言和收集器介面語言。 其他語言的支援很快就會推出。


### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

探索時間取決於您要探索多少 VM。 一般來說，若是 100 個 VM，當收集器完成執行後，大約會使用一小時來完成探索。 

1. 在 Migration Planner 專案中，按一下 [管理] > [機器]。
2. 確認您想要探索的 VM 有出現在入口網站內。


## <a name="create-and-view-an-assessment"></a>建立和檢視評估

探索到 VM 之後，您可以將它們分組，並建立評估。 

1. 在專案的 [概觀] 頁面中，按一下 [+建立評估]。
2. 按一下 [檢視全部] 來檢閱評估設定。
3. 建立群組，並指定群組名稱。
4. 選取您想要新增至群組的機器。
5. 按一下 [建立評估] 以建立群組和評估。
6. 建立評估之後，在 [概觀] > [儀表板] 中檢視該評估。
7. 按一下 [匯出評估]，將其下載為 Excel 檔案。

### <a name="sample-assessment"></a>評估範例

以下是評估報告範例。 報告中包括 VM 是否與 Azure 相容的相關資訊，以及估計的每月成本。 

![評估報告](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure 移轉整備程度

此檢視會顯示每一部機器的整備狀態。

- 對於已準備好的 VM，Azure Migrate 會建議這些 VM 在 Azure 中該有的大小。
- 對於尚未準備好的 VM，Azure Migrate 會解釋原因，並提供補救步驟。
- Azure Migrate 會向您建議可用於移轉的工具。 如果機器適用於原形 (Lift and shift) 移轉，則建議使用 Azure Site Recovery 服務。 如果其為資料庫機器，則建議使用 Azure 資料庫移轉服務。

  ![評估整備](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>每月成本預估值

此檢視會顯示每一部機器的計算和儲存成本。 在計算成本估計值時，會使用以效能為基礎的大小建議來用於機器和其磁碟以及評估屬性。

系統會彙總群組內所有 VM 之計算和儲存的每月預估成本。 您可以在每部機器上按一下，以深入了解其詳細資料。 

![評估 VM 成本](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

您可以向下切入以查看特定機器的成本。

![評估 VM 成本](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>後續步驟

- [了解](how-to-scale-assessment.md)如何設定大量內部部署機器的評估。
- 了解如何使用[機器相依性對應](how-to-create-group-machine-dependencies.md)來建立更詳細的評估群組
- [深入了解](concepts-assessment-calculation.md)評估的計算方式。
