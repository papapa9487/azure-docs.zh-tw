---
title: "匯入資料以用於 Azure Cosmos DB 資料表 API | Microsoft Docs"
description: "了解如何匯入資料以用於 Azure Cosmos DB 資料表 API。"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 984510b2dae99849507953163f94ad2f925651cd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>匯入資料以用於 Azure Cosmos DB 資料表 API

本教學課程提供如何匯入資料以用於 Azure Cosmos DB [資料表 API](table-introduction.md) 的指示。 如果您將資料儲存在 Azure 資料表儲存體中，您可以使用資料移轉工具或 AzCopy 將您的資料匯入到 Azure Cosmos DB。 資料匯入後，您就可以利用 Azure Cosmos DB 所提供的進階功能，例如周全的全域發佈、專用的輸送量、99 百分位數的單一數字毫秒延遲、保證高可用性，以及自動次要索引。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 使用資料移轉工具匯入資料
> * 使用 AzCopy 匯入資料

## <a name="data-migration-tool"></a>資料移轉工具

Azure Cosmos DB 的資料移轉工具是其中一個用來匯入現有 Azure 資料表儲存體資料的選項。 若要使用該工具匯入資料，您必須選取 Azure 資料表儲存體做為來源，並選取 Azure Cosmos DB 資料表 API 做為目標，然後依照工具的要求提供其餘資訊。 資料可以大量匯出，也可以使用循序記錄匯入來匯出。 

下列各節提供如何將 Azure 資料表儲存體定義為來源，以及將資料表 API 定義為目標的資訊：
- [使用 Azure 資料表儲存體做為資料移轉來源](import-data.md#AzureTableSource)。 
- [使用大量匯入來匯出到資料表 API](import-data.md#tableapibulkexport)
- [使用循序記錄匯入來匯出到資料表 API](import-data.md#tableapiseqtarget)。

## <a name="azcopy-command"></a>AzCopy 命令

使用 AzCopy 命令列公用程式是另一個用來將資料從 Azure 資料表儲存體移轉到 Azure Cosmos DB 資料表 API 的選項。 若要使用 AzCopy，您必須先將資料匯出到資訊清單檔案 (如[從資料表儲存體匯出資料](../storage/common/storage-use-azcopy.md#export-data-from-table-storage)所述)，然後將資料從資訊清單檔案匯入到 [Azure Cosmos DB 資料表 API](../storage/common/storage-use-azcopy.md#import-data-into-table-storage)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用資料移轉工具匯入資料
> * 使用 AzCopy 匯入資料

您現在可以繼續進行下一個教學課程，了解如何使用 Azure Cosmos DB 資料表 API 查詢資料。 

> [!div class="nextstepaction"]
>[如何查詢資料？](../cosmos-db/tutorial-query-table.md)



