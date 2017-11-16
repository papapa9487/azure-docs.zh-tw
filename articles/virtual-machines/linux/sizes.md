---
title: "Azure 中的 Linux VM 大小 | Microsoft Docs"
description: "列出 Azure 中 Linux 虛擬機器的不同可用大小。"
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 7c6f2e83e5763f3cf8f305c73c0ad74fa8b37c00
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Azure 中的 Linux 虛擬機器大小
本文說明可用於執行 Linux 應用程式與工作負載之 Azure 虛擬機器的可用大小及選項。 同時也提供當您規劃使用這些資源時所需注意的部署考量。 本文也適用於 [Windows 虛擬機器](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


| 類型                     | 大小           |    說明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [一般用途](sizes-general.md)          | B (預覽)、Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0-7  | CPU 與記憶體的比例平均。 適用於測試和開發、小型至中型資料庫，以及低至中流量 Web 伺服器。 |
| [計算最佳化](sizes-compute.md)        | Fsv2、Fs、F             | CPU 與記憶體的比例高。 適用於中流量 Web 伺服器、網路設備、批次處理，以及應用程式伺服器。        |
| [記憶體最佳化](sizes-memory.md)         | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | 記憶體與 CPU 的比例高。 適用於關聯式資料庫伺服器、中型至大型快取，以及記憶體內部分析。                 |
| [儲存體最佳化](sizes-storage.md)        | Ls                | 高磁碟輸送量及 IO。 適用於巨量資料、SQL 及 NoSQL 資料庫。                                                         |
| [GPU](sizes-gpu.md)            | NV、NC            | 以大量圖形轉譯和視訊編輯為目標的特製化虛擬機器。 有單一或多個 GPU 可供使用。       |
| [高效能計算](sizes-hpc.md) | H、A8-11          | 速度最快、功能最強的 CPU 虛擬機器，搭載選配的高輸送量網路介面 (RDMA)。 

<br>

- 如需各式大小的定價，請參閱 [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)。 
- 如需了解 Azure 區域中的 VM 大小可用性，請參閱 [依區域提供的產品](https://azure.microsoft.com/regions/services/)。
- 若要查看 Azure VM 的一般限制，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../../azure-subscription-service-limits.md)。
- 深入了解 [Azure 計算單位 (ACU)](../windows/acu.md) 如何協助您比較各個 Azure SKU 的計算效能。


## <a name="rest-api"></a>Rest API

如需使用 REST API 來查詢 VM 大小的資訊，請參閱下列各項：

- [列出調整大小的可用虛擬機器大小](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing)
- [列出訂用帳戶的可用虛擬機器大小](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)
- [列出可用性設定組中可用的虛擬機器大小](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set)

## <a name="acu"></a>ACU

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。

## <a name="next-steps"></a>後續步驟

深入了解可用的不同 VM 大小：
- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)



