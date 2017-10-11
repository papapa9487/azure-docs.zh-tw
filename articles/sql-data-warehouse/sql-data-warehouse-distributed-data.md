---
title: "分散式資料如何在 Azure SQL 資料倉儲運作 | Microsoft Docs"
description: "了解大量平行處理 (MPP) 如何分散資料，以及在 Azure SQL 資料倉儲和平行資料倉儲中分散資料表的選項。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: bae494a6-7ac5-4c38-8ca3-ab2696c63a9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 07/12/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3c166acb17193caae32d7bad133ec510ff679353
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>大量平行處理 (MPP) 的分散式資料和分散式資料表
了解如何在 Microsoft 的大量平行處理 (MPP) 系統 Azure SQL 資料倉儲和平行資料倉儲中分散使用者資料。 設計您的資料倉儲以有效使用分散式資料，可協助您達成 MPP 架構的查詢處理優勢。 一些資料庫設計選擇可能會嚴重影響改善查詢效能。  

> [!NOTE]
> Azure SQL 資料倉儲和平行資料倉儲使用相同的大量平行處理 (MPP) 設計，但是由於基礎平台，它們會有一些差異。 SQL 資料倉儲是一種在 Azure 上執行的平台即服務 (PaaS)。 平行資料倉儲在分析平台系統 (AP) 上執行，也就是在 Windows Server 上執行的內部部署應用裝置。
> 
> 

## <a name="what-is-distributed-data"></a>什麼是分散式資料？
在 SQL 資料倉儲和平行資料倉儲中，分散式資料是指儲存在 MPP 系統多個位置的使用者資料。 每個位置可做為獨立的儲存體運作，和在其一部分資料上執行查詢的處理單位。 分散式資料是以平行方式執行查詢來達成高效能查詢的基礎。

若要散發資料，資料倉儲會將使用者資料表的每個資料列指派至一個分散式位置。  您可以使用雜湊發佈方法或循環配置資源方法來散發資料表。 發佈方法指定於 CREATE TABLE 陳述式中。 

## <a name="hash-distributed-tables"></a>雜湊分散式資料表
下圖說明完整 (非分散式資料表) 會如何儲存為雜湊分散式資料表。 具決定性的函式會將每個資料列指派給屬於一個發佈。 在資料表定義中，其中一個資料行會指定為發佈資料行。 雜湊函式會使用發佈資料行中的值將每個資料列指派給發佈。

選取發佈資料行有效能考量，例如區分、資料扭曲和在系統上執行的查詢類型。

![分散式資料表](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "分散式資料表")  

* 每個資料列屬於一種發佈。  
* 具決定性的雜湊演算法會將每個資料列指派給一個發佈。  
* 每個發佈的資料表資料列數目會隨著顯示不同資料表大小而有所不同。

## <a name="round-robin-distributed-tables"></a>循環配置資源分散式資料表
循環配置資源分散式資料表會發佈資料列，方法為以循序方式將每個資料列指派給發佈。 將資料載入循環配置資源資料表很快速，但查詢效能可能會變慢。  將循環配置資源資料表合併通常需要改組，以讓查詢產生準確的結果，這會很花時間。

## <a name="distributed-storage-locations-are-called-distributions"></a>分散式儲存體位置稱為發佈
每個分散式位置稱為發佈。 當以平行方式執行查詢時，每個發佈會在其一部分的資料上執行 SQL 查詢。 SQL 資料倉儲會使用 SQL 資料庫執行查詢。 平行資料倉儲會使用 SQL Server 執行查詢。 這種無共用架構設計是要達成向外延展平行運算的基礎。

### <a name="can-i-view-the-distributions"></a>可以檢視發佈嗎？
每個發佈有通訊群組 ID，且在有關 SQL 資料倉儲和平行資料倉儲的系統檢視中可以看見。 您可以使用發佈識別碼來疑難排解查詢效能及其他問題。 如需系統檢視的清單，請參閱 [MPP 系統檢視](sql-data-warehouse-reference-tsql-statements.md)。

## <a name="difference-between-a-distribution-and-a-compute-node"></a>發佈與計算節點之間的差異
發佈是儲存分散式資料和處理平行查詢的基本單位。 發佈會群組為計算節點。 每個計算節點會追蹤一或多個發佈。  

* 分析平台系統會使用節點做為硬體架構和向外擴充功能的中央元件。 它每個計算節點一律使用八個發佈，如雜湊分散式資料表圖中所示。 計算節點數目，因此發佈數目是由您為裝置購買的計算節點數目所決定。 例如，如果您購買八個計算節點，您會取得 64 個發佈 (8 個計算節點 x 8 個發佈/節點)。 
* SQL 資料倉儲的發佈數目固定為 60 個，而計算節點數目則為彈性。 會使用 Azure 計算和儲存體資源來實作計算節點。 計算節點的數目可能會根據後端服務工作負載和您針對資料倉儲指定的計算容量 (DWU) 而變更。 計算節點數目變更時，每個計算節點的發佈數目也會變更。 

### <a name="can-i-view-the-compute-nodes"></a>可以檢視計算節點嗎？
每個計算節點有節點 ID，且在有關 SQL 資料倉儲和平行資料倉儲的系統檢視中可以看見。  您可以在名稱開頭為 sys.pdw_nodes 的系統檢視中尋找 node_id 資料行表來看到計算節點。 如需系統檢視的清單，請參閱 [MPP 系統檢視](sql-data-warehouse-reference-tsql-statements.md)。

## <a name="Replicated"></a>複寫資料表
複寫資料表會在每個計算節點上都儲存一份完整的資料表複本。 複寫資料表可使在進行聯結或彙總之前，不需要在計算節點之間傳輸資料。 由於在每個計算節點上儲存完整資料表所需的額外儲存體，因此複寫資料表僅在小型資料表才可行。  

下圖顯示儲存在每個計算節點上的複寫資料表。 對於「SQL 資料倉儲」，會將複寫資料表完整複製到每個計算節點上的散發資料庫。 針對平行處理資料倉儲，複寫資料表會儲存在指派給計算節點的所有磁碟上。  此磁碟策略會使用 SQL Server 檔案群組來實作。  

![複寫的資料表](media/sql-data-warehouse-distributed-data/replicated-table.png "複寫的資料表") 

## <a name="next-steps"></a>後續步驟
若要有效使用分散式資料表，請參閱[在 SQL 資料倉儲中散發資料表](sql-data-warehouse-tables-distribute.md)  

