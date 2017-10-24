---
title: "Azure Linux VM 大小 - HPC | Microsoft Docs"
description: "列出 Azure 中可用的不同 Linux 高效能運算虛擬機器大小。"
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: jonbeck
ms.openlocfilehash: fecc0656264f1c1d44aa8ad3aea321aa8cc4a0c8
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="high-performance-compute-linux-vm-sizes"></a>高效能運算 Linux VM 大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>支援 RDMA 的執行個體
計算密集型執行個體 (H16r、H16mr、A8 與 A9) 的子集，包含用於遠端直接記憶體存取 (RDMA) 連線的網路介面。 這是可供其他 VM 大小使用之標準 Azure 網路介面的額外界面。 
  
這個介面允許支援 RDMA 的執行個體透過 InfiniBand 網路進行通訊，針對 H16r 與 H16mr 虛擬機器以 FDR 速率運作，以及針對 A8 與 A9 虛擬機器以 QDR 速率運作。 這些 RDMA 功能可以提高在 Intel MPI 5.x 或更新版本下執行之訊息傳遞介面 (MPI) 應用程式的延展性和效能。

在相同的可用性設定組 (如果您使用 Azure Resource Manager 部署模型) 或相同的雲端服務 (如果您使用傳統部署模型) 中部署支援 RDMA 的 VM。 使支援 RDMA 的 Linux VM 得以存取 Azure RDMA 網路的其他需求如下。

### <a name="distributions"></a>散發
 
從 Azure Marketplace 中支援 RDMA 連線的其中一個映像，部署計算密集的 VM：
  
* **Ubuntu**：Ubuntu Server 16.04 LTS。 設定 VM 上的 RDMA 驅動程式，並向 Intel 註冊以下載 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server**：SLES 12 SP3 for HPC、SLES 12 SP3 for HPC (Premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)。 已在 VM 上安裝 RDMA 驅動程式並散發 Intel MPI 套件。 執行下列命令來安裝 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **CentOS 型 HPC**：CentOS 型 7.3 HPC、CentOS 型 7.1 HPC、CentOS 型 6.8 HPC 或 CentOS 型 6.5 HPC (對於 H 系列，建議使用 7.1 版或更新版本)。 已在 VM 上安裝 RDMA 驅動程式和 Intel MPI 5.1。  
 
  > [!NOTE]
  > 在 CentOS 型 HPC 映像上， **yum** 組態檔中已停用核心更新。 這是因為 Linux RDMA 驅動程式以 RPM 封裝散發，如果更新核心，驅動程式更新可能無法運作。
  > 
 
### <a name="cluster-configuration"></a>叢集組態 
    
必須進行額外的系統設定，才能在叢集 VM 上執行 MPI 作業。 例如，在 VM 叢集上，您必須在計算節點之間建立信任關係。 如需了解一般設定，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

### <a name="network-topology-considerations"></a>網路拓撲考量
* 在 Azure 中具備 RDMA 功能的 Linux VM 上，Eth1 會保留給 RDMA 網路流量使用。 請勿變更任何 Eth1 設定或參考到此網路之組態檔中的任何資訊。 Eth0 會保留給一般 Azure 網路流量。

* 在 Azure 中，不支援透過 InfiniBand (IB) 的 IP。 僅支援透過 IB 的 RDMA。

## <a name="using-hpc-pack"></a>使用 HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)是 Microsoft 的免費 HPC 叢集和作業管理解決方案，提供您一個搭配 Linux 使用計算密集型執行個體的選項。 HPC Pack 的最新版本支援讓數個 Linux 散發套件在部署於 Azure VM 中、由 Windows Server 前端節點管理的計算節點上執行。 搭配支援 RDMA 且執行 Intel MPI 的 Linux 計算節點時，HPC Pack 可以排定及執行存取 RDMA 網路的 Linux MPI 應用程式。 請參閱[開始在 Azure 中的 HPC Pack 叢集使用 Linux 計算節點](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

## <a name="other-sizes"></a>其他大小
- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>後續步驟

- 若要開始在 Linux 上部署及使用具有 RDMA 的計算密集型大小，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。




