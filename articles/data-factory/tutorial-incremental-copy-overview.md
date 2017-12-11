---
title: "使用 Azure Data Factory 以累加方式複製資料 | Microsoft Docs"
description: "這些教學課程會說明，如何以累加方式將資料從來源資料存放區複製到目的地資料存放區。 第一個教學課程會從一個資料表複製資料。 "
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: 2ae6cb42685dfb227bd75f83e73dfdf646ab909f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>以累加方式將資料從來源資料存放區載入到目的地資料存放區

在資料整合解決方案中，我們經常會見到在初次載入完整資料之後，再以累加方式 (或差異) 載入資料的情形。 本節的教學課程會說明各種使用 Azure Data Factory 第 2 版來累加載入資料的方式。

## <a name="delta-data-loading-using-a-watermark"></a>使用水位線載入差異資料
在此案例中，您會在來源資料庫中定義水位線。 水位線這個資料行上會有上次更新時間戳記或遞增索引鍵。 差異載入解決方案會載入在舊水位線和新水位線之間有所變更的資料。 下圖會說明這種方法的工作流程： 

![使用水位線的工作流程](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

如需逐步指示，請參閱下列教學課程： 

- [以累加方式將資料從 Azure SQL Database 中的一個資料表複製到 Azure Blob 儲存體](tutorial-incremental-copy-powershell.md)
- [以累加方式將內部部署 SQL Server 中多個資料表的資料複製到 Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-using-the-change-tracking-technology"></a>使用變更追蹤技術載入差異資料
變更追蹤技術是 SQL Server 和 Azure SQL Database 中的輕量型解決方案，可為應用程式提供有效率的變更追蹤機制。 它可讓應用程式輕鬆地識別已插入、更新或刪除的資料。 

下圖會說明這種方法的工作流程：

![使用變更追蹤的工作流程](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

如需逐步指示，請參閱下列教學課程： <br/>
[使用變更追蹤技術，以累加方式將資料從 Azure SQL Database 複製到 Azure Blob 儲存體](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>後續步驟
前進到下列教學課程： 

> [!div class="nextstepaction"]
>[以累加方式將資料從 Azure SQL Database 中的一個資料表複製到 Azure Blob 儲存體](tutorial-incremental-copy-powershell.md)
