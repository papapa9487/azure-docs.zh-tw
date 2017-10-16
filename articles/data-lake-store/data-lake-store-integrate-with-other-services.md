---
title: "將 Data Lake Store 與其他 Azure 服務整合 | Microsoft Docs"
description: "了解 Data Lake Store 如何與其他 Azure 服務整合"
documentationcenter: 
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: de7aff6b31d937576da65498c5fcce2ae9abdbf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>整合 Data Lake Store 與其他 Azure 服務
Azure Data Lake Store 可以與其他 Azure 服務一起使用，以啟用更廣泛的案例。 下列文章列出 Data Lake Store 可以整合的服務。

## <a name="use-data-lake-store-with-azure-hdinsight"></a>搭配 Azure HDInsight 使用 Data Lake Store
您可以佈建 [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 叢集，其使用 Data Lake Store 做為 HDFS 相容儲存體。 對於此版本，Windows 和 Linux 上的 Hadoop 和 Storm 叢集，您只能使用 Data Lake Store 做為額外的儲存體。 這類叢集仍會使用 Azure 儲存體 (WASB) 做為預設儲存體。 但是，對於 Windows 和 Linux 上的 HBase 叢集，您可以使用 Data Lake Store 做為預設儲存體或額外的儲存體，或同時做為兩者。

如需如何使用 Data Lake Store 佈建 HDInsight 叢集的指示，請參閱：

* [使用 Azure 入口網站佈建 HDInsight 叢集與 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [使用 Azure PowerShell 以 Data Lake Store 佈建 HDInsight 叢集作為預設儲存體](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [使用 Azure PowerShell 以 Data Lake Store 佈建 HDInsight 叢集作為額外儲存體](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>搭配 Azure Data Lake Analytics 使用 Data Lake Store
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) 可讓您在雲端規模使用巨量資料。 它以動態方式佈建資源，讓您能夠進行分析 TB 或甚至是 EB 的資料，這些資料可以儲存在許多支援的資料來源，其中一個就是 Data Lake Store。 Data Lake Analytics 已特別最佳化以使用 Azure Data Lake Store - 提供最高層級的效能、輸送量和您的巨量資料工作負載的平行處理。

如需有關如何搭配 Data Lake Store 使用 Data Lake Analytics 的指示，請參閱 [使用 Data Lake Store 開始使用 Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。

## <a name="use-data-lake-store-with-azure-data-factory"></a>搭配 Azure Data Factory 使用 Data Lake Store
您可以使用 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 從 Azure 資料表、Azure SQL Database、Azure SQL 資料倉儲、Azure 儲存體 Blob 和內部部署資料庫擷取資料。 Azure 生態系統中的首選，Azure Data Factory 可以用來協調從這些來源到 Azure Data Lake Store 的資料擷取。

如需有關如何搭配 Data Lake Store 使用 Azure Data Factory 的指示，請參閱 [使用 Data Factory 移動 Data Lake Store 的資料](../data-factory/connector-azure-data-lake-store.md)。

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>將資料從 Azure 儲存體 Blob 複製到 Data Lake Store 中
Azure Data Lake Store 提供命令列工具 AdlCopy，可讓您將資料從 Azure Blob 儲存體複製到 Data Lake Store 帳戶。 如需詳細資訊，請參閱 [將資料從 Azure 儲存體 Blob 複製到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)。

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>在 Azure SQL Database 和 Data Lake Store 之間複製資料
您可以使用 Apache Sqoop 在 Azure SQL Database 和 Data Lake Store 之間匯入及匯出資料。 如需詳細資訊，請參閱 [使用 Sqoop 在 Data Lake Store 和 Azure SQL Database 之間複製資料](data-lake-store-data-transfer-sql-sqoop.md)。

## <a name="use-data-lake-store-with-stream-analytics"></a>使用 Data Lake Store 搭配串流分析
您可以使用 Data Lake Store 做為其中一個使用 Azure 串流分析儲存串流資料的輸出。 如需詳細資訊，請參閱 [使用 Azure 串流分析將來自 Azure 儲存體 Blob 的資料串流處理至 Data Lake Store](data-lake-store-stream-analytics.md)。

## <a name="use-data-lake-store-with-power-bi"></a>使用 Data Lake Store 搭配 Power BI
您可以使用 Power BI 從 Data Lake Store 匯入資料以分析和視覺化資料。 如需詳細資訊，請參閱 [在 Data Lake Store 中使用 Power BI 分析資料](data-lake-store-power-bi.md)。

## <a name="use-data-lake-store-with-data-catalog"></a>使用 Data Lake Store 搭配資料目錄
您可以從 Data Lake Store 將資料註冊至 Azure 資料目錄，讓資料可在整個組織找到。 如需詳細資訊，請參閱[在 Azure 資料目錄中註冊來自 Data Lake Store 的資料](data-lake-store-with-data-catalog.md)。

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>將 Data Lake Store 搭配 SQL Server Integration Services (SSIS) 使用
您可以使用 SSIS 中的 Azure Data Lake Store 連接管理員以使用 Azure Data Lake Store 連接 SSIS 封裝。 如需詳細資訊，請參閱[將 Data Lake Store 搭配 SSIS 使用](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)。

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>將 Data Lake Store 搭配 SQL 資料倉儲使用
您可以使用 PolyBase 將 Azure Data Lake Store 的資料載入到 SQL 資料倉儲。 如需詳細資訊，請參閱[將 Data Lake Store 搭配 SQL 資料倉儲使用](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)。

## <a name="use-data-lake-store-with-azure-event-hubs"></a>搭配 Azure 事件中樞使用 Data Lake Store
您可以使用 Azure Data Lake Store 來封存和擷取 Azure 事件中樞所收到的資料。 如需詳細資訊，請參閱[搭配 Azure 事件中樞使用 Data Lake Store](data-lake-store-archive-eventhub-capture.md)。

## <a name="see-also"></a>另請參閱
* [Azure Data Lake Store 概觀](data-lake-store-overview.md)
* [使用入口網站開始使用 Data Lake Store](data-lake-store-get-started-portal.md)
* [使用 PowerShell 開始使用 Data Lake Store](data-lake-store-get-started-powershell.md)  

