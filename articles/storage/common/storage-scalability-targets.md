---
title: "Azure 儲存體延展性和效能目標 | Microsoft Docs"
description: "了解 Azure 儲存體的延展性和效能目標，包括標準和進階儲存體帳戶的容量、要求率以及輸入和輸出頻寬。 了解每一項 Azure 儲存體服務內分割的效能目標。"
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 07/12/2017
ms.author: tamram
ms.openlocfilehash: abaad01bbf7a11ad078c79d7c192ef3f84812178
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2017
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Azure 儲存體延展性和效能目標
## <a name="overview"></a>Overview
本主題描述 Microsoft Azure 儲存體的延展性和效能。 如需其他 Azure 限制的摘要，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../../azure-subscription-service-limits.md)。

> [!NOTE]
> 所有儲存體帳戶都能在一般網路拓撲上執行，並支援下面說明的延展性和效能目標，無論它們在何時建立。 如需 Azure 儲存體平面網路架構及延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。
> 
> [!IMPORTANT]
> 列於此處的延展性和效能目標都是高階目標，但仍可達成。 在所有情況下，您的儲存體帳戶所達到的要求率和頻寬取決於已儲存物件的大小、使用的存取模式、應用程式執行的工作負載類型。 請務必測試您的服務，以判斷效能是否達到您的要求。 如果可能，請避免流量率突增，確保流量在不同分割之間妥善分散。
> 
> 當您的應用程式達到分割處理工作負載的限制時，Azure 儲存體會開始傳回錯誤碼 503 (伺服器忙碌) 或錯誤碼 500 (作業逾時) 回應。 當發生這種情況時，應用程式應該使用指數輪詢重試原則。 指數輪詢讓分割的負載減少，也能減輕該分割流量的尖峰。
> 
> 

如果您的應用程式需求超出單一儲存體帳戶的延展性目標，您可以建置可使用多個儲存體帳戶的應用程式，並將資料物件分割到那些儲存體帳戶中。 如需批量價格的詳細資訊，請參閱 [Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/) 。

## <a name="scalability-targets-for-a-storage-account"></a>儲存體帳戶的延展性目標
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 儲存體擴展目標
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure 檔案擴展目標
如需 Azure 檔案和 Azure 檔案同步的擴展目標與效能目標的詳細資訊，請參閱 [Azure 檔案延展性和效能目標](../files/storage-files-scale-targets.md)。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure 檔案同步擴展目標
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure 佇列儲存體擴展目標
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure 資料表儲存體擴展目標
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
## <a name="scalability-targets-for-virtual-machine-disks"></a>虛擬機器磁碟的延展性目標
[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

如需詳細資訊，請參閱 [Windows VM 大小](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或 [Linux VM 大小](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="managed-virtual-machine-disks"></a>受管理的虛擬機器磁碟

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>未受管理的虛擬機器磁碟
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>另請參閱
* [儲存體定價詳細資料](https://azure.microsoft.com/pricing/details/storage/)
* [Azure 訂用帳戶和服務限制、配額與限制](../../azure-subscription-service-limits.md)
* [Premium 儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage-premium-storage.md)
* [Azure 儲存體複寫](../storage-redundancy.md)
* [Microsoft Azure 儲存體效能與延展性檢查清單](../storage-performance-checklist.md)
* [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務。](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

