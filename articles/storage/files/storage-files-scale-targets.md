---
title: "Azure 檔案服務延展性和效能目標 | Microsoft Docs"
description: "了解 Azure 檔案服務的延展性和效能目標，包括容量、要求率以及輸入和輸出頻寬限制。"
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/17/2017
ms.author: wgries
ms.openlocfilehash: 35d430b683224d1035cccdfb58b9db415d47ea3b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure 檔案服務延展性和效能目標
[Azure 檔案服務](storage-files-introduction.md)可提供在雲端中受到完整管理的檔案共用，可透過業界標準 SMB 通訊協定加以存取。 本文討論 Azure 檔案服務和 Azure 檔案同步 (預覽) 的延展性和效能目標。

此處所列的延展性和效能目標是高階的目標，但可能會受到部署中的其他變數影響。 例如，檔案的輸送量可能也受限於可用的網路頻寬，而不只是裝載 Azure 檔案服務的伺服器。 我們強烈建議您測試您的使用模式，以判斷 Azure 檔案服務的延展性和效能是否符合您的需求。 我們也保證會隨時間提高這些限制。 歡迎您在底下留言或前往 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\)，提供想要我們提高哪些限制的意見反應。

## <a name="azure-storage-account-scale-targets"></a>Azure 儲存體帳戶擴展目標
Azure 檔案共用的父資源是 Azure 儲存體帳戶。 儲存體帳戶代表 Azure 中可供多個儲存體服務 (包括 Azure 檔案服務) 儲存資料的儲存體集區。 將資料儲存在儲存體帳戶的其他服務有 Azure Blob 儲存體、Azure 佇列儲存體和 Azure 資料表儲存體。 下列目標適用於在儲存體帳戶中儲存資料的所有儲存體服務：

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> 其他儲存體服務的儲存體帳戶使用情況，會影響您儲存體帳戶中的 Azure 檔案共用。 比方說，如果達到 Azure Blob 儲存體的最大儲存體帳戶容量，您將無法在 Azure 檔案共用上建立新檔案，即使您的 Azure 檔案共用低於最大共用大小也一樣。

## <a name="azure-files-scale-targets"></a>Azure 檔案服務擴展目標
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure 檔案同步擴展目標
藉由 Azure 檔案同步，我們已儘可能嘗試設計沒有限制的使用方式，但並非總是可行。 下表指出我們測試的界限，及哪些目標實際上是固定限制：

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>另請參閱
- [規劃 Azure 檔案部署](storage-files-planning.md)
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [其他儲存體服務的延展性和效能目標](../common/storage-scalability-targets.md)