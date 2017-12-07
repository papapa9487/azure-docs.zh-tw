---
title: "使用 Azure Migrate 進行規模探索和評定 | Microsoft Docs"
description: "說明如何利用 Azure Migrate 服務評定大量的內部部署機器。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dde0d07f-94b7-4b6a-a158-a89aa9324a35
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 930ec182cf329e7dda072dc49bd7f70abb413f2d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>探索及評定大型 VMware 環境

本文章說明如何利用 [Azure Migrate](migrate-overview.md) 評定大量的內部部署機器。 Azure Migrate 會評定機器，以確認機器是否適合移轉至 Azure，並提供在 Azure 中執行機器的大小調整建議和成本估計。

## <a name="prerequisites"></a>必要條件

- **VMware**：您必須至少有一個 VMware VM 位於執行 5.0 版或更新版本的 ESXi 主機或叢集上。 主機或叢集必須是由執行 5.5 或 6.0 版的 vCenter Server 所管理。
- **vCenter 帳戶**：您必須有唯讀帳戶，且此帳戶具有 vCenter Server 的系統管理員認證。 Azure Migrate 會使用此帳戶來探索 VM。
- **權限**：在 vCenter Server 上，您必須有權限方可藉由匯入 .OVA 格式的檔案來建立 VM。
- **統計資料設定**：vCenter Server 的統計資料設定應先設為層級 2 或更高層級，再開始部署。

## <a name="plan-azure-migrate-projects"></a>規劃 Azure Migrate 專案

一個 Azure Migrate 專案最多可以評定 1500 部機器。 專案中的每次探索最多可以探索 1000 部機器。

- 如果您要探索的機器少於 1000 部，您需要一個執行一次探索的專案。
- 如果您的機器數量介於 1000 到 1500 部，您需要一個執行兩次探索的專案。
- 如果您的機器數量超過 1500 部，您需要根據需求建立多個專案及執行多次探索。 例如：
    - 如果您有 3000 部機器，可以設定兩個執行兩次探索的專案，或三個執行一次探索的專案。
    - 如果您有 5000 部機器，可以設定四個專案。 兩個探索 1500 部機器的專案，再加上一個探索 500 部機器的專案。 您也可以設定五個分別執行一次探索的專案。 
- 當您在 Azure Migrate 中進行探索時，可以將探索範圍設定為 VMware 資料夾、資料中心或叢集。
- 若要執行一次以上的探索，請在 vCenter 中確認您要探索的 VM，是位於支援 1000 部機器限制的資料夾、資料中心或叢集內。
- 基於評定目的，我們建議您將相依的機器保留在同個專案和同次評估內。 因此在 vCenter 中，請確認相依的機器位在同一個資料夾、資料中心或叢集內，以利評定。


## <a name="create-a-project"></a>建立專案

根據需求建立 Azure Migrate 專案。

1. 在 Azure 入口網站中，按一下 [建立資源]。
2. 搜尋 **Azure Migrate**，然後在搜尋結果中選取服務 (**Azure Migrate (預覽)**)。 然後按一下 [ **建立**]。
3. 指定專案名稱，以及專案的 Azure 訂用帳戶。
4. 建立新的資源群組。
5. 指定要建立專案的所在區域，然後按一下 [建立]。 從內部部署 VM 收集來的中繼資料會儲存在此區域中。

## <a name="set-up-the-collector-appliance"></a>設定收集器設備

Azure Migrate 會建立稱為「收集器設備」的內部部署 VM。 此 VM 會探索內部部署 VMware VM，並將其相關中繼資料傳送至 Azure Migrate 服務。 若要設定收集器設備，您可以下載 .OVA 檔案，並將其匯入內部部署 vCenter Server，以建立 VM。

### <a name="download-the-collector-appliance"></a>下載收集器設備

如果您有多個專案，只需要將收集器設備下載到 vCenter Server 一次。 下載和設定設備之後，您可以針對每個專案執行設備，並指定唯一的專案識別碼和索引鍵。

1. 在 Azure Migrate 專案中，按一下 [開始使用] > [探索及評定] > [探索機器]。
2. 在 [探索機器] 中，按一下 [下載] 以下載 .OVA 檔案。
3. 在 [複製專案認證] 中，複製專案識別碼和索引鍵。 在設定收集器時，您會需要這些資料。

   
### <a name="verify-the-collector-appliance"></a>確認收集器設備

請先確認 .OVA 檔案是安全的，再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 OVA 的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 產生的雜湊應符合這些設定。

    **演算法** | **雜湊值**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>建立收集器 VM

將下載的檔案匯入 vCenter Server。

1. 在 vSphere 用戶端主控台中，按一下 [檔案] > [部署 OVF 範本]。

    ![部署 OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. 在 [部署 OVF 範本精靈] > [來源] 中，指定 .ova 檔案的位置。
3. 在 [名稱] 和 [位置] 中，指定收集器 VM 的易記名稱，以及要裝載 VM 的所在清查物件。
5. 在 [主機/叢集] 中，指定收集器 VM 的執行所在主機或叢集。
7. 在儲存體中，指定收集器 VM 的儲存目的地。
8. 在 [磁碟格式] 中，指定磁碟類型和大小。
9. 在 [網路對應] 中，指定收集器 VM 所要連線的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure。 
10. 檢閱並確認設定，然後按一下 [完成]。

## <a name="identify-the-key-and-id-for-each-project"></a>找出每個專案的索引鍵和識別碼

如果您有多個專案，請務必區分每個專案的識別碼和索引鍵。 當您執行收集器來探索 VM 時，將會需要索引鍵。

1. 在專案中，按一下 [開始使用] > [探索及評定] > [探索機器]。
2. 在 [複製專案認證] 中，複製專案識別碼和索引鍵。 
    ![專案識別碼](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>收集效能計數器的 vCenter 統計資料層級
以下是在探索期間收集的計數器清單。 這些計數器預設可在 vCenter Server 的各個層級取用。 我們建議您為統計資料層級設定最高的一般層級 (層級 3)，以正確收集所有計數器。 如果您的 vCenter 設定為較低層級，只有幾個計數器能完整收集，其他會設定為 0。 因此評定可能會顯示不完整的資料。 下表也會列出不收集特定計數器時會受到影響的評定結果。

|計數器                                  |Level    |每個裝置層級  |評定影響                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |NA                |建議的 VM 大小和成本                    |
|mem.usage.average                        | 1       |NA                |建議的 VM 大小和成本                    |
|virtualDisk.read.average                 | 2       |2                 |磁碟大小、儲存成本和 VM 大小         |
|virtualDisk.write.average                | 2       |2                 |磁碟大小、儲存成本和 VM 大小         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |磁碟大小、儲存成本和 VM 大小         |
|virtualDisk.numberReadAveraged.average  | 1       |3                 |磁碟大小、儲存成本和 VM 大小         |
|net.received.average                     | 2       |3                 |VM 大小和網路成本                        |
|net.transmitted.average                  | 2       |3                 |VM 大小和網路成本                        |

> [!WARNING]
> 如果您剛剛設定較高的統計資料層級，可能需要一天的時間才能產生效能計數器。 因此，建議您在一天後再執行探索。

## <a name="run-the-collector-to-discover-vms"></a>執行收集器來探索 VM

針對每個需要執行的探索，您要執行收集器來探索所需範圍內的 VM。 請逐一執行探索。 系統不支援並行探索，且每個探索的範圍不得相同。

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
    - 在 [收集範圍] 中，選取 VM 探索的範圍。 收集器只能探索指定範圍內的 VM。 範圍可以設定為特定資料夾、資料中心或叢集。 其不應包含超過 1000 個 VM。 
    - n 在 [用來分組的標籤類別] 中，選取 [無]。

        ![選取範圍](./media/how-to-scale-assessment/select-scope.png)

1. 在 [選取專案] 中，指定專案的識別碼和索引鍵。 如果您之前未複製，請從收集器 VM 開啟 Azure 入口網站。 在專案的 [概觀] 頁面中，按一下 [探索機器]，然後複製值。  
在 [完成探索] 中監視探索流程，然後確認從 VM 收集到的中繼資料位於範圍內。 收集器會提供概略的探索時間。


### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

探索時間取決於您要探索多少 VM。 一般來說，若是 100 個 VM，當收集器完成執行後，大約會使用一小時來完成探索。 

1. 在 Migration Planner 專案中，按一下 [管理] > [機器]。
2. 確認您想要探索的 VM 出現在入口網站內。


## <a name="next-steps"></a>後續步驟

- 了解如何[建立評定群組](how-to-create-a-group.md)。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。