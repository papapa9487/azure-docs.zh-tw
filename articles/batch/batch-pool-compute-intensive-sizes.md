---
title: "搭配 Batch 來使用需要大量計算的 Azure VM | Microsoft Docs"
description: "如何在 Azure Batch 集區中利用具備 RDMA 功能或已啟用 GPU 功能的 VM 大小"
services: batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: danlep
ms.openlocfilehash: c52a054e4fc8f61f871acd9f35b9a3e6247e48ef
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2017
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>在 Batch 集區中使用具備 RDMA 功能或已啟用 GPU 功能的執行個體

為了執行某些 Batch 作業，您可能會想利用專門設計來進行大規模計算的 Azure VM 大小。 例如，若要執行多執行個體的 [MPI 工作負載](batch-mpi.md)，您可以選擇 A8、A9 或 H 系列的大小，因為這些大小擁有的網路介面可供進行遠端直接記憶體存取 (RDMA)。 這些大小會連線到 InfiniBand 網路來進行節點間通訊，以加速 MPI 應用程式的運作。 如果是 CUDA 應用程式，則可以選擇 N 系列大小，因為這些大小包含 NVIDIA Tesla 圖形處理器 (GPU) 顯示卡。

本文會就如何在 Batch 集區中使用某些 Azure 特製大小提供指導方針與範例。 若要了解規格及背景，請參閱：

* 高效能計算 VM 大小 ([Linux](../virtual-machines/linux/sizes-hpc.md)、[Windows](../virtual-machines/windows/sizes-hpc.md)) 

* 已啟用 GPU 功能的 VM 大小 ([Linux](../virtual-machines/linux/sizes-gpu.md)、[Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>訂用帳戶與帳戶限制

* **配額** - 可以新增至 Batch 集區之節點的數量或類型可能會受到一或多個 Azure 配額所限制。 當您選擇具備 RDMA 功能、已啟用 GPU 功能或其他多核心 VM 大小時，更會受到限制。 根據您所建立的 Batch 帳戶類型，配額可能會套用於帳戶本身或套用於訂用帳戶。

    * 如果您在 **Batch 服務**組態中建立了您的 Batch 帳戶，您就會受到[每個 Batch 帳戶的專用核心配額](batch-quota-limit.md#resource-quotas)所限制。 根據預設，這個配額為 20 個核心。 如果您使用[低優先順序 VM](batch-low-pri-vms.md)，這些 VM 有另外的適用配額。 

    * 如果您在**使用者訂用帳戶**組態中建立了帳戶，您的訂用帳戶會受到每個區域的 VM 核心數目所限制。 請參閱 [Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md)。 您的訂用帳戶也會對某些 VM 大小 (包括 HPC 和 GPU 執行個體) 套用區域配額。 在使用者訂用帳戶組態中，Batch 帳戶沒有另外的適用配額。 

  當您在 Batch 中使用特製 VM 大小時，您可能必須增加一或多個配額。 若要要求增加配額，可免費[開啟線上客戶支援要求](../azure-supportability/how-to-create-azure-support-request.md)。

* **區域可用性** - 在您用來建立 Batch 帳戶的區域中，可能不會提供需要大量計算的 VM。 若要確認是否有提供某個大小，請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)。


## <a name="dependencies"></a>相依項目

只有特定作業系統會支援需要大量計算之大小的 RDMA 和 GPU 功能。 根據您的作業系統，您可能需要安裝或設定額外的驅動程式或其他軟體。 下表摘要說明這些相依性。 如需詳細資訊，請參閱連結的文章。 若要了解用來設定 Batch 集區的選項，請參閱本文稍後的內容。


### <a name="linux-pools---virtual-machine-configuration"></a>Linux 集區 - 虛擬機器組態

| 大小 | 功能 | 作業系統 | 必要的軟體 | 集區設定 |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | SUSE Linux Enterprise Server 12 HPC 或<br/>CentOS 型 HPC<br/>(Azure Marketplace) | Intel MPI 5 | 啟用節點間通訊、停用並行工作執行 |
| [NC 系列*](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms) | NVIDIA Tesla K80 GPU | Ubuntu 16.04 LTS。<br/>Red Hat Enterprise Linux 7.3，或<br/>CentOS 型 7.3<br/>(Azure Marketplace) | NVIDIA CUDA Toolkit 8.0 驅動程式 | N/A | 
| [NV 系列](../virtual-machines/linux/n-series-driver-setup.md#install-grid-drivers-for-nv-vms) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS<br/>Red Hat Enterprise Linux 7.3<br/>CentOS 型 7.3<br/>(Azure Marketplace) | NVIDIA GRID 4.3 驅動程式 | N/A |

*具有 Intel MPI 的 CentOS 型 7.3 HPC 可支援在 NC24r VM 上進行 RDMA 連線。



### <a name="windows-pools---virtual-machine-configuration"></a>Windows 集區 - 虛擬機器組態

| 大小 | 功能 | 作業系統 | 必要的軟體 | 集區設定 |
| -------- | ------ | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2 或<br/>Windows Server 2012 (Azure Marketplace) | Microsoft MPI 2012 R2 或更新版本，或<br/> Intel MPI 5<br/><br/>HpcVMDrivers Azure VM 擴充功能 | 啟用節點間通訊、停用並行工作執行 |
| [NC 系列*](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla K80 GPU | Windows Server 2016 或 <br/>Windows Server 2012 R2 (Azure Marketplace) | NVIDIA Tesla 驅動程式或 CUDA Toolkit 8.0 驅動程式| N/A | 
| [NV 系列](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 或<br/>Windows Server 2012 R2 (Azure Marketplace) | NVIDIA GRID 4.3 驅動程式 | N/A |

*具有 HpcVMDrivers 擴充功能和 Microsoft MPI 或 Intel MPI 的 Windows Server 2012 R2 可支援在 NC24r VM 上進行 RDMA 連線。

### <a name="windows-pools---cloud-services-configuration"></a>Windows 集區 - 雲端服務組態

> [!NOTE]
> 具有雲端服務組態的 Batch 集區不支援 N 系列大小。
>

| 大小 | 功能 | 作業系統 | 必要的軟體 | 集區設定 |
| -------- | ------- | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2、<br/>Windows Server 2012 或<br/>Windows Server 2008 R2 (Guest OS 系列) | Microsoft MPI 2012 R2 或更新版本，或<br/>Intel MPI 5<br/><br/>HpcVMDrivers Azure VM 擴充功能 | 啟用節點間通訊、<br/> 停用並行工作執行 |





## <a name="pool-configuration-options"></a>集區組態選項

為了對 Batch 集區設定特製 VM 大小，Batch API 及工具提供了數個選項供您安裝必要的軟體或驅動程式，包括：

* [啟動工作](batch-api-basics.md#start-task) - 將安裝套件作為資源檔來上傳到 Azure 儲存體帳戶 (與 Batch 帳戶位於相同區域)。 建立啟動工作命令列，以在集區啟動時以無訊息模式安裝資源檔。 如需詳細資訊，請參閱 [REST API 文件](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask)。

  > [!NOTE] 
  > 啟動工作必須以提升的 (系統管理員) 權限來執行，而且必須等候到成功。
  >

* [應用程式套件](batch-application-packages.md) - 將壓縮的安裝套件新增至您的 Batch 帳戶，並在集區中設定套件參考。 此設定會將套件上傳到集區中的所有節點，並將套件解壓縮。 如果該套件是安裝程式，請建立啟動工作命令列，以在所有集區節點上以無訊息方式安裝應用程式。 您也可以選擇在節點上有工作排定要執行時安裝套件。

* [自訂集區映像](batch-api-basics.md#pool) - 建立自訂的 Windows 或 Linux VM 映像，並在其中包含 VM 大小所需的驅動程式、軟體或其他設定。 如果您在使用者訂用帳戶組態中建立了 Batch 帳戶，請為 Batch 集區指定自訂映像  (Batch 服務組態中的帳戶不支援自訂映像)。只有虛擬機器組態中的集區可以使用自訂映像。

  > [!IMPORTANT]
  > 在 Batch 集區中，您目前無法使用以受控磁碟或進階儲存體建立的自訂映像。
  >



* [Batch Shipyard](https://github.com/Azure/batch-shipyard) 會自動將 GPU 和 RDMA 設定為對 Azure Batch 上的容器化工作負載透明地進行處理。 Batch Shipyard 完全是透過組態檔來驅動。 有許多可用的配方組態範例可啟用 GPU 和 RDMA 工作負載，例如 [CNTK GPU 配方](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI)，此配方會在 N 系列 VM 上預先設定 GPU 驅動程式，並以 Docker 映像的形式載入 Microsoft 辨識工具組。


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>範例：A8 VM 集區上的 Microsoft MPI

若要在 Azure A8 節點所構成的集區上執行 Windows MPI 應用程式，您必須安裝支援的 MPI 實作。 以下的步驟範例可供您使用 Batch 應用程式套件，在 Windows 集區上安裝 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx)。

1. 下載適用於最新版 Microsoft MPI 的[安裝套件](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe)。
2. 建立該套件的 zip 檔案。
3. 將套件上傳至您的 Batch 帳戶。 如需相關步驟，請參閱[應用程式套件](batch-application-packages.md)指引。 指定應用程式識別碼 (例如 MSMPI) 和版本 (例如 8.1)。 
4. 使用 Batch API 或 Azure 入口網站，在雲端服務組態中建立具有所需節點數目和規模大小的集區。 下表顯示使用啟動工作以自動安裝模式設定 MPI 的設定範例：

| 設定 | 值 |
| ---- | ----- | 
| **映像類型** | 雲端服務 |
| **作業系統系列** | Windows Server 2012 R2 (作業系統系列 4) |
| **節點大小** | A8 標準 |
| **已啟用節點間通訊** | True |
| **每個節點的工作數上限** | 1 |
| **應用程式套件參考** | MSMPI |
| **已啟用啟動工作** | True<br>**命令列** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**使用者身分識別** - 集區的自動使用者、系統管理員<br/>**等待成功** - True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>範例：NC VM 集區上的 NVIDIA Tesla 驅動程式

若要在 Linux NC 節點所構成的集區上執行 CUDA 應用程式，您必須在節點上安裝 CUDA Toolkit 8.0。 此工具組會安裝所需的 NVIDIA Tesla GPU 驅動程式。 以下的步驟範例可供您部署具有 GPU 驅動程式的自訂 Ubuntu 16.04 LTS 映像：

1. 部署執行 Ubuntu 16.04 LTS 的 Azure NC6 VM。 例如，在美國中南部區域建立 VM。 請確定您在建立 VM 時使用的是標準儲存體，而且「未使用」受控磁碟。
2. 遵循步驟來連線至 VM 並[安裝 CUDA 驅動程式](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms)。
3. 將 Linux 代理程式取消佈建，然後使用 Azure CLI 1.0 命令擷取 Linux VM 映像。 如需相關步驟，請參閱[擷取在 Azure 上執行的 Linux 虛擬機器](../virtual-machines/linux/capture-image-nodejs.md)。 記下映像的 URI。
  > [!IMPORTANT]
  > 請勿使用 Azure CLI 2.0 命令來擷取 Azure Batch 的映像。 CLI 2.0 命令目前只能擷取使用受控磁碟所建立的 VM。
  >
4. 在支援 NC VM 的區域中以使用者訂用帳戶組態建立 Batch 帳戶。
5. 使用 Batch API 或 Azure 入口網站，以自訂映像建立具有所需節點數目和規模大小的集區。 下表顯示該映像的集區設定範例：

| 設定 | 值 |
| ---- | ---- |
| **映像類型** | 自訂映像 |
| **自訂映像** | 下列格式的映像 URI：`https://yourstorageaccountdisks.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` |
| **節點代理程式 SKU** | batch.node.ubuntu 16.04 |
| **節點大小** | NC6 標準 |



## <a name="next-steps"></a>後續步驟

* 若要在 Azure Batch 集區上執行 MPI 作業，請參閱 [Windows](batch-mpi.md) 或 [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) 範例。

* 如需 Batch 上之 GPU 工作負載的範例，請參閱 [Batch Shipyard](https://github.com/Azure/batch-shipyard/) 配方。