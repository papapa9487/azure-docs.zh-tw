---
title: "Azure 中的 Linux HPC Pack 叢集選項 | Microsoft Docs"
description: "了解在 Azure 雲端使用 Microsoft HPC Pack 建立及管理 Linux 高效能運算 (HPC) 叢集的選項"
services: virtual-machines-linux,cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: ae36e64c0261b1fe8d02d6dcb80df7cdee333db9
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>使用 HPC Pack 在 Azure 中建立及管理適用於 Linux HPC 工作負載之叢集的選項
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

本文著重於使用 HPC Pack 執行 Linux 的工作負載的選項。 另有[使用 HPC Pack 執行 Windows HPC 工作負載](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的選項。

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Azure VM 和 VM 擴展集中的 HPC Pack 叢集
### <a name="azure-templates"></a>Azure 範本
* (GitHub) [HPC Pack 2016 叢集範本](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2 叢集範本](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [HPC Pack cluster for Linux workloads (適用於 Linux 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (快速入門) [Create an HPC cluster with Linux compute nodes (使用 Linux 計算節點建立 HPC 叢集)](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>HPC Pack 2012 R2 的 PowerShell 部署指令碼
* [使用 HPC Pack IaaS 部署指令碼建立 Linux HPC 叢集](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>教學課程
* [教學課程：開始在 Azure 中的 HPC Pack 叢集使用 Linux 計算節點](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [教學課程：在 Azure 中的 Linux 計算節點以 Microsoft HPC Pack 執行 NAMD](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [教學課程：在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 OpenFOAM](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [教學課程：在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 STAR-CCM+](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>叢集管理
* [將作業送出至 Azure 的 HPC Pack 叢集](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack 中的作業管理](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>MPI 工作負載的 RDMA 叢集
* [教學課程：在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 OpenFOAM](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [設定 Linux RDMA 叢集以執行 MPI 應用程式](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

