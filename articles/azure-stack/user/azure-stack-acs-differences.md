---
title: "Azure Stack 儲存體：差異與考量"
description: "了解「Azure Stack 儲存體」與「Azure 儲存體」之間的差異，以及 Azure Stack 部署考量。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/08/2017
ms.author: jeffgilb
ms.openlocfilehash: 1dc099fa234e217b682c88f2214fe271c916eec2
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack 儲存體：差異與考量

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

「Azure Stack 儲存體」是 Microsoft Azure Stack 中的一組儲存體雲端服務。 「Azure Stack 儲存體」使用與 Azure 一致的語意來提供 Blob、資料表、佇列及帳戶管理功能。

此文章摘要說明「Azure Stack 儲存體」與「Azure 儲存體」的已知差異。 此外，也摘要說明您部署 Azure Stack 時要記住的其他考量。 若要深入了解 Azure Stack 與 Azure 之間的大致差異，請參閱[主要考量](azure-stack-considerations.md)主題。

## <a name="cheat-sheet-storage-differences"></a>速查表：儲存體差異

| 功能 | Azure (全域) | Azure Stack |
| --- | --- | --- |
|檔案儲存體|支援雲端式 SMB 檔案共用|尚不支援
|待用資料的 Azure 儲存體服務加密|256 位元 AES 加密|尚不支援
|儲存體帳戶類型|一般用途和 Azure Blob 儲存體帳戶|僅一般用途
|複寫選項|本地備援儲存體、異地備援儲存體、讀取權限異地備援儲存體，以及區域備援儲存體|本地備援儲存體
|進階儲存體|完全支援|可佈建，但無效能限制或保證
|受控磁碟|支援進階和標準|尚不支援
|Blob 名稱|1,024 個字元 (2,048 個位元組)|880 個字元 (1,760 個位元組)
|區塊 Blob 大小上限|4.75 TB (100 MB X 50,000 個區塊)|50,000 x 4 MB (約為 195 GB)
|分頁 blob 快照集複製|不支援對連結至執行中 VM 的 Azure 未受管理 VM 進行備份|尚不支援
|分頁 Blob 增量快照複製|支援進階和標準 Azure 分頁 Blob|尚不支援
|分頁 Blob 大小上限|8 TB|1 TB
|分頁 Blob 分頁大小|512 個位元組|4 KB
|資料表分割區索引鍵和資料列索引鍵大小|1,024 個字元 (2,048 個位元組)|400 個字元 (800 個位元組)

### <a name="metrics"></a>度量
儲存體度量也有一些差異：
* 儲存體度量中的交易資料並不區分內部或外部網路頻寬。
* 儲存體度量中的交易資料並不包含虛擬機器對所掛接磁碟的存取。

## <a name="api-version"></a>API 版本
使用「Azure Stack 儲存體」時支援下列版本：

* Azure 儲存體資料服務：[2015-04-05 REST API 版本](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Azure 儲存體管理服務：[2015-05-01-preview、2015-06-15 及 2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>後續步驟

* [開始使用 Azure Stack 儲存體開發工具](azure-stack-storage-dev.md)
* [Azure Stack 儲存體簡介](azure-stack-storage-overview.md)

