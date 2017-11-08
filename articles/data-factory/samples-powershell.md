---
title: "適用於 Azure Data Factory 的 Azure PowerShell 範例 | Microsoft Docs"
description: "Azure PowerShell 範例：協助您建立和管理 Data Factory 的指令碼。"
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: spelluru
ms.openlocfilehash: f223cd8efbff77b02598293afd3b30a662d3abf3
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>適用於 Azure Data Factory 的 Azure PowerShell 範例

下表包含適用於 Azure Data Factory 之範例 Azure PowerShell 指令碼的連結。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您是使用第 1 版的 Data Factory 服務 (正式運作版本 (GA))，請參閱 [Data Factory 第 1 版的範例](v1/data-factory-samples.md)。

| |  |
|---|---|
|**複製資料**||
|[在 Azure Blob 儲存體中將 Blob 從某個資料夾複製到另一個資料夾](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會將 Blob 從 Azure Blob 儲存體中的某個資料夾，複製到相同 Blob 儲存體中的另一個資料夾。 |
|[將資料從內部部署 SQL Server 複製到 Azure Blob 儲存體](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 這個 PowerShell 指令碼會將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體。 |
|[大量複製](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 這個範例 PowerShell 指令碼會將資料從 Azure SQL Database 中的多個資料表複製到 Azure SQL 資料倉儲。 |
|[增量備份](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 這個範例 PowerShell 指令碼會在初次將資料從來源完整複製到接收器之後，只將來源資料存放區中新的或已更新的記錄，載入到接收資料存放區。 |
|**轉換資料**||
|[使用 Spark 叢集轉換資料](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼可藉由在 Spark 叢集上執行程式來轉換資料。 |
|**將 SSIS 套件隨即轉移至 Azure**||
|[建立 Azure-SSIS 整合執行階段](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會佈建 Azure-SSIS 整合執行階段，以在 Azure 中執行 SQL Server Integration Services (SSIS) 套件。 |



