---
title: "適用於 Windows 的 Azure N 系列驅動程式設定 | Microsoft Docs"
description: "如何針對 Azure 中執行 Windows 的 N 系列 VM 設定 NVIDIA GPU 驅動程式"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/07/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b480d10df777a2757c073ff77e1845d33d63163a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>為執行 Windows Server 的 N 系列 VM 設定 GPU 驅動程式
若要利用 Azure N 系列 VM (執行 Windows Server 2016 或 Windows Server 2012 R2) 的 GPU 功能，請安裝支援的 NVIDIA 圖形驅動程式。 本文提供您在部署 N 系列 VM 後的驅動程式安裝步驟。 驅動程式設定資訊也適用於 [ VM](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如需基本規格、儲存體容量與磁碟的詳細資料，請參閱 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 


[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]



## <a name="driver-installation"></a>驅動程式安裝

1. 由遠端桌面連接至每個 N 系列 VM。

2. 下載、擷取及安裝 Windows 作業系統支援的驅動程式。

在 Azure NV VM 上，安裝驅動程式之後必須重新啟動。 在 NC VM 上，則不需要重新啟動。

## <a name="verify-driver-installation"></a>確認驅動程式安裝

您可以在 [裝置管理員] 中確認驅動程式安裝。 下列範例會顯示 Azure NC VM 上成功的 Tesla K80 卡組態。

![GPU 驅動程式屬性](./media/n-series-driver-setup/GPU_driver_properties.png)

若要查詢 GPU 裝置狀態，請執行與驅動程式一起安裝的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface)命令列公用程式。

1. 開啟命令提示字元然後變更位置到 **C:\Program Files\NVIDIA Corporation\NVSMI** 目錄中。

2. 執行 **nvidia-smi**。 如果已安裝驅動程式，您會看到輸出如下。 請注意，除非您正在 VM上執行 GPU 工作負載，否則 [GPU-Util] 會顯示 **0%**。

![NVIDIA 裝置狀態](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>適用於 NC24r VM 的 RDMA 網路

可以在部署於相同可用性設定組中的 NC24r VM 上啟用 RDMA 網路連線能力。 在具備 RDMA 功能的 VM 上，HpcVmDrivers 擴充必須新增以安裝 Windows 網路裝置驅動程式，該驅動程式會啟用 RDMA 連線能力。 若要將 VM 擴充功能新增至 NC24r VM，請針對 Azure Resource Manager 使用 [Azure PowerShell](/powershell/azure/overview) Cmdlet。

> [!NOTE]
> 目前只有 Windows Server 2012 R2 在 NC24r VM 上支援 RDMA 網路。
> 

若要在美國西部區域中名為 myVM 的現有具備 RDMA 功能的 VM 上安裝最新版本 1.1 HpcVMDrivers 延伸模組：
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  如需詳細資訊，請參閱[適用於 Windows 的虛擬機器擴充功能和功能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

RDMA 網路可針對使用 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) 或 Intel MPI 5.x 執行的應用程式，支援訊息傳遞介面 (MPI) 流量。 


## <a name="next-steps"></a>後續步驟

* 如需 N 系列 VM 上 NVIDIA GPU 的詳細資訊，請參閱︰
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (適用於 Azure NC VM)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (適用於 Azure NV VM)

* 針對 NVIDIA Tesla GPU 建置 GPU 加速應用程式的開發人員也可以下載及安裝適用於 [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) 或 [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe) 的 CUDA Toolkit 8。 如需詳細資訊，請參閱 [CUDA 安裝指南](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)。


