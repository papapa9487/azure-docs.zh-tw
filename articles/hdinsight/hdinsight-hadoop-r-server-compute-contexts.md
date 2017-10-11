---
title: "適用於 HDInsight 上 R 伺服器的計算內容選項 - Azure | Microsoft Docs"
description: "了解 HDInsight 上 R 伺服器之使用者可用的不同計算內容選項"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 47f4441612be4f363ba82cc22b09786a6f3bfdc3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>適用於 HDInsight 上 R 伺服器的計算內容選項

Azure HDInsight 上的 Microsoft R 伺服器控制如何透過設定計算內容來執行呼叫。 此文章概述可用於指定是否以及如何跨邊緣節點核心或 HDInsight 叢集將執行作業平行化的選項。

叢集的邊緣節點提供便利的地方，以便連線到叢集以及執行 R 指令碼。 有了邊緣節點之後，即可選擇跨邊緣節點伺服器的核心，執行 ScaleR 的平行分散式函數。 您也可以使用 ScaleR 的 Hadoop Map Reduce 或 Spark 計算內容，跨越叢集的節點來執行這些函數。

## <a name="microsoft-r-server-on-azure-hdinsight"></a>Azure HDInsight 上的 Microsoft R 伺服器
[Azure HDInsight 上的 Microsoft R 伺服器](hdinsight-hadoop-r-server-overview.md)可提供最新的 R 型分析功能。 它可以使用儲存在 [Azure Blob](../storage/common/storage-introduction.md "Azure Blob 儲存體") 儲存體帳戶、Data Lake Store 或本機 Linux 檔案系統上之 HDFS 容器中的資料。 R 伺服器是根據開放原始碼 R 所建置，因此您建置的 R 型應用程式可以套用 8000 多個開放原始碼 R 套件中的任何一個。 它們也可以使用 [RevoScaleR](https://msdn.microsoft.com/microsoft-r/scaler/scaler) (R 伺服器隨附的 Microsoft 巨量資料分析套件) 中的常式。  

## <a name="compute-contexts-for-an-edge-node"></a>邊緣節點的計算內容
一般而言，在邊緣節點上 R 伺服器中執行的 R 指令碼會在該節點上的 R 解譯器內執行。 但呼叫 ScaleR 函數的步驟則屬例外。 ScaleR 呼叫會在計算環境中執行，該環境是由您設定 ScaleR 計算內容的方式所決定。  當您從邊緣節點執行 R 指令碼時，可能的計算內容值為：

- 本機循序 (*‘local’*)
- 本機平行 (*‘localpar’*)
- Map Reduce
- Spark

*‘local’* 和 *‘localpar’* 選項的差別只在於執行 **rxExec** 呼叫的方式。 它們都會在所有可用的核心之間，以平行方式執行其他的 rx 函數呼叫，除非已指定，否則皆使用 ScaleR **numCoresToUse** 選項，例如 `rxOptions(numCoresToUse=6)`。 平行執行選項提供最佳效能。

下表摘要說明各種不同的計算內容選項來設定呼叫的執行方式：

| 計算內容  | 設定方式                      | 執行內容                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| 本機循序 | rxSetComputeContext(‘local’)    | 跨邊緣節點伺服器的核心平行執行，只有 rxExec 為循序執行 |
| 本機平行   | rxSetComputeContext(‘localpar’) | 跨邊緣節點伺服器的核心平行執行 |
| Spark            | RxSpark()                       | 跨 HDI 叢集的節點透過 Spark 平行處理分散式執行 |
| Map Reduce       | RxHadoopMR()                    | 跨 HDI 叢集的節點透過 Map Reduce 平行處理分散式執行 |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>用於決定計算內容的指導方針

在這三個提供平行執行的選項中，選擇哪個選項取決於資料的分析工作本質、大小與位置。 沒有任何簡單的公式可告訴您該使用哪個計算內容。 不過，有一些指導原則可協助您做出正確的選擇，或至少幫助您縮小選擇範圍，然後再執行效能評定。 這些指導原則包括︰

- 本機 Linux 檔案系統比 HDFS 還快。
- 如果資料位於本機，且是 XDF 格式，則重複分析會比較快。
- 建議您從文字資料來源串流少量資料。 如果資料量比較大，請在分析之前先將它轉換為 XDF。
- 對於非常大量的資料，將資料複製或串流至邊緣節點以進行分析的額外負荷會變得難以管理。
- Spark 在 Hadoop 中的分析速度較 Map Reduce 快。

在給定這些原則的情況下，以下各節提供一些有關選取計算內容的一般準則。

### <a name="local"></a>本機
* 如果要分析的資料量很小，而且不需要重複分析，請使用 *'local'* 或 *'localpar'* 直接將它串流到分析常式。
* 如果要分析的資料量很小或是中等大小，而且需要重複分析，請將它複製到本機檔案系統、匯入至 XDF，然後透過 *'local'* 或 *'localpar'* 分析。

### <a name="hadoop-spark"></a>Hadoop Spark
* 如果要分析的資料量很大，請使用 **RxHiveData** 或 **RxParquetData** 將它匯入到 Spark DataFrame，或匯入到 HDFS 中的 XDF (除非儲存體會是問題)，然後使用 Spark 計算內容分析。

### <a name="hadoop-map-reduce"></a>Hadoop Map Reduce
* 只有在您使用 Spark 計算內容發生無法克服的問題時才使用 Map Reduce 計算內容，因為它的速度通常會比較慢。  

## <a name="inline-help-on-rxsetcomputecontext"></a>rxSetComputeContext 的內嵌說明
如需 ScaleR 計算內容的詳細資訊和範例，請參閱 R 中有關 rxSetComputeContext 方法的內嵌說明，例如︰

    > ?rxSetComputeContext

您也可以參閱可從 [R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "MSDN 上的 R 伺服器") 文件庫取得的 [ScaleR 分散式計算指南](https://msdn.microsoft.com/microsoft-r/scaler-distributed-computing) \(英文\)。

## <a name="next-steps"></a>後續步驟
在此文章中，您可以了解可用於指定是否以及如何跨邊緣節點核心或 HDInsight 叢集將執行作業平行化的選項。 若要深入了解如何搭配 HDInsight 叢集使用 R 伺服器，請參閱下列主題：

* [適用於 Hadoop 的 R 伺服器概觀](hdinsight-hadoop-r-server-overview.md)
* [開始使用適用於 Hadoop 的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)
* [將 RStudio Server 新增至 HDInsight (若未在建立叢集期間新增)](hdinsight-hadoop-r-server-install-r-studio.md)
* [適用於 HDInsight 上 R 伺服器的 Azure 儲存體選項](hdinsight-hadoop-r-server-storage.md)

