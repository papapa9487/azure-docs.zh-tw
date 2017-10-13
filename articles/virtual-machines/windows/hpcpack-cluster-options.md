---
title: "Azure 中的 Windows HPC Pack 叢集選項 | Microsoft Docs"
description: "了解在 Azure 雲端使用 Microsoft HPC Pack 建立及管理 Windows 高效能運算 (HPC) 叢集的選項"
services: virtual-machines-windows,cloud-services,batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 08/25/2017
ms.author: danlep
ms.openlocfilehash: b82cb6cc3724af9306bc3b23125cf7cfa03df8ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>使用 HPC Pack 在 Azure 中建立及管理適用於 Windows HPC 工作負載之叢集的選項
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

本文著重於建立 HPC Pack 叢集以執行 Windows 工作負載的選項。 此外，也有建立 HPC Pack 叢集以執行 [Linux HPC 工作負載](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的選項。


## <a name="hpc-pack-cluster-in-azure-vms"></a>Azure VM 中的 HPC Pack 叢集
### <a name="azure-templates"></a>Azure 範本
* (GitHub) [HPC Pack 2016 叢集範本](https://github.com/MsHpcPack/HPCPack2016)
* (Marketplace) [HPC Pack cluster for Windows workloads (適用於 Windows 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [HPC Pack cluster for Excel workloads (適用於 Excel 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (快速入門) [Create an HPC cluster (建立 HPC 叢集)](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (快速入門) [Create an HPC cluster with custom compute node image (使用自訂的計算節點映像建立 HPC 叢集)](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM 映像
* [Windows Server 2016 上的 HPC Pack 2016 前端節點 (英文)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [Windows Server 2016 上的 HPC Pack 2016 計算節點 (英文)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [Windows Server 2012 R2 上的 HPC Pack 2016 前端節點 (英文)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [Windows Server 2012 R2 上的 HPC Pack 2016 計算節點 (英文)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [Windows Server 2012 R2 上的 HPC Pack 2012 R2 前端節點 (英文)](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Windows Server 2012 R2 上的 HPC Pack 2012 R2 計算節點 (英文)](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [Windows Server 2012 R2 上含 Excel 的 HPC Pack 運算節點](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>HPC Pack 2012 R2 的 PowerShell 部署指令碼
* [使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>教學課程
* [教學課程：在 Azure 中部署 HPC Pack 2016 叢集](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [教學課程：開始使用 Azure 中的 HPC Pack 叢集執行 Excel 和 SOA 工作負載](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>使用 Azure 入口網站手動部署
* [在 Azure VM 中設定 HPC Pack 叢集的前端節點](hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>叢集管理
* [在 Azure 中管理 HPC Pack 叢集的運算節點](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [增加及縮減 HPC Pack 叢集中的 Azure 運算資源](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [將作業送出至 Azure 的 HPC Pack 叢集](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack 中的作業管理](https://technet.microsoft.com/library/jj899585.aspx)
* [使用 Azure Active Directory 管理 Azure 中的 HPC Pack 2016 叢集](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-worker-role-nodes"></a>使用背景工作角色節點的高載 
* [使用 HPC Pack 將暴增的工作負載移至 Azure 背景工作執行個體](https://technet.microsoft.com/library/gg481749.aspx)
* [教學課程：在 Azure 中使用 HPC Pack 設定混合式叢集](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [將 Azure「高載」節點加入 Azure 中的 HPC Pack 前端節點](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-azure-batch"></a>使用 Azure Batch 的高載
* [使用 HPC Pack 將暴增的工作負載移至 Azure Batch](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>MPI 工作負載的 RDMA 叢集
* [使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

