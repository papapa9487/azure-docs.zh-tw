---
title: "Azure Blob 儲存體簡介 | Microsoft Docs"
description: "Azure Blob 儲存體簡介"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: tamram
ms.openlocfilehash: 6059ce809e7a8b2115e391d9db5b5b0a8626109e
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="introduction-to-blob-storage"></a>Blob 儲存體簡介

Azure Blob 儲存體是一項儲存大量非結構化物件資料的服務 (例如文字或二進位資料)，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。 您可以使用 Blob 儲存體向全球公開資料，或私下儲存應用程式資料。

Blob 儲存體的一般用途包括：

* 直接提供映像或文件給瀏覽器
* 儲存檔案供分散式存取
* 串流傳輸視訊和音訊
* 儲存備份和還原、災害復原和封存資料
* 儲存資料供內部部署或 Azure 託管服務進行分析

## <a name="blob-service-concepts"></a>Blob 服務概念

Blob 服務包含下列元件：

![Blob 架構](./media/storage-blobs-introduction/blob1.png)

* **儲存體帳戶：** 一律透過儲存體帳戶來存取 Azure 儲存體。 此儲存體帳戶可以是**一般用途的儲存體帳戶**或專門用於儲存物件/Blob 的 **Blob 儲存體帳戶**。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

* **容器：** 容器提供一組 Blob 的群組。 所有 Blob 都必須放在容器中。 一個帳戶可以包含的容器不限數量。 容器可以儲存無限制的 Blob。 請注意，容器名稱必須是小寫。

* **Blob：** 任何類型和大小的檔案。 Azure 儲存體提供三種類型的 Blob：區塊 Blob、分頁 Blob 及附加 Blob。
  
    *區塊 Blob* 很適合儲存文字或二進位檔案，例如文件和媒體檔案。 *附加 Blob* 和區塊 Blob 相似，但已針對附加作業最佳化，因此適合用於記錄的情況。 單一區塊 blob 可包含高達 50,000 個區塊，每個區塊最大為 100 MB，大小總計稍高於 4.75 TB (100 MB X 50,000)。 單一附加 blob 可包含高達 50,000 個區塊，每個區塊最大為 4 MB，大小總計稍高於 195 GB (4 MB X 50,000)。
  
    「分頁 Blob」大小可達 8 TB，且在頻繁進行讀寫作業時更有效率。 Azure 虛擬機器會使用分頁 Blob 作為作業系統和資料磁碟。
  
    如需有關命名容器和 Blob 的詳細資訊，請參閱 [命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)。

## <a name="next-steps"></a>後續步驟

* [建立儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [透過 .NET 開始使用 Blob 儲存體](storage-dotnet-how-to-use-blobs.md)
* [使用 .NET 的 Azure 儲存體範例](../common/storage-samples-dotnet.md)
* [使用 Java 的 Azure 儲存體範例](../common/storage-samples-java.md)
