---
title: "Azure PowerShell 指令碼範例 - 計算 Blob 容器大小 | Microsoft Docs"
description: "您可以加總 Azure Blob 儲存體中每個 Blob 的大小，計算出 Azure Blob 儲存體的容器大小。"
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>計算 Blob 儲存體容器的大小

此指令碼會加總容器中 Blob 的大小，計算出 Azure Blob 儲存體的容器大小。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>了解 Blob 儲存體容器的大小

Blob 儲存體容器的大小總計包含容器本身的大小和容器下所有 Blob 的大小。

以下描述對 Blob 容器和 Blob 儲存體容量的計算方式。 在下方的 Len(X) 表示字串中的字元數。

### <a name="blob-containers"></a>Blob 容器

以下是估計每個 Blob 容器所使用的儲存體數量的方式：

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

以下是分解：
* 每個容器 48 個位元組的額外負荷包含上次修改時間、權限、公用設定，以及一些系統中繼資料。
* 容器名稱會儲存為 Unicode，因此取得字元數目並乘以 2。
* 對於儲存的每個 Blob 容器中繼資料，我們會儲存名稱的長度 (儲存為 ASCII) 再加上字串值的長度。
* 每一簽署的識別碼 512 個位元組包括簽署的識別碼名稱、開始時間、到期時間和權限。

### <a name="blobs"></a>Blob

以下是估計每個 Blob 所使用的儲存體數量的方式：

* 區塊 Blob (基底 Blob 或快照集)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* 分頁 Blob (基底 Blob 或快照集)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

以下是分解：

* Blob 124 個位元組的額外負荷包含上次修改時間、快取控制、內容類型、內容語言、內容編碼、內容 MD5、權限、快照資訊、租用，以及一些系統中繼資料。
* Blob 名稱會儲存為 Unicode，因此取得字元數目並乘以 2。
* 然後針對儲存的每個中繼資料，儲存名稱的長度 (儲存為 ASCII) 再加上字串值的長度。
* 然後針對區塊 Blob
    * 區塊清單 8 個位元組
    * 區塊數目 x 依位元組的區塊識別碼大小
    * 加上所有認可及未認可的區塊中的資料大小。 請注意，使用快照集時，此大小只包含此基底或快照集 Blob 的獨特資料。 如果未認可的區塊在一個星期未使用之後，就會進行回收，然後在該時間之後不再計入計費。
* 然後針對分頁 Blob
    * 具有資料的非連續分頁範圍 x 12 個位元組。 這是呼叫 GetPageRanges API 時會看到的唯一分頁範圍的數目。
    * 加上所有儲存的分頁中資料的大小 (以位元組為單位)。 請注意，使用快照集時，此大小只包含要計算的基底 Blob 或快照集 Blob。

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>後續步驟

如需有關 Azure 儲存體計費的詳細資訊，請參閱[了解 Windows Azure 儲存體計費](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)。

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

如需其他的儲存體 PowerShell 指令碼範例，可參閱 [Azure 儲存體的 PowerShell 範例](../blobs/storage-samples-blobs-powershell.md)。
