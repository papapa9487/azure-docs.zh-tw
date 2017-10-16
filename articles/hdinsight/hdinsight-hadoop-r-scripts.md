---
title: "在 HDInsight 中使用 R 來自訂叢集 - Azure | Microsoft Docs"
description: "了解如何使用指令碼動作安裝 R，以及在 HDInsight 叢集上使用 R。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 5b9b793d49217acd9f0c6c518596a7afb5600d69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>在 HDInsight Hadoop 叢集上安裝和使用 R

了解如何使用指令碼動作來以 R 自訂以 Windows 為基礎的 HDInsight 叢集，以及如何在 HDInsight 叢集上使用 R。 [HDInsight 供應項目](https://azure.microsoft.com/pricing/details/hdinsight/)包括隨附於 HDInsight 叢集的 R 伺服器。 這可讓 R 指令碼使用 MapReduce 和 Spark 來執行分散式計算。 如需詳細資訊，請參閱[開始使用 HDInsight 上的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)。 如需搭配以 Linux 為基礎的叢集使用 R 的詳細資訊，請參閱[在 HDInsight Hadoop 叢集上安裝和使用 R (Linux)](hdinsight-hadoop-r-scripts-linux.md)。

您也可以使用「指令碼動作」，在 Azure HDInsight 的任一類型的叢集 (Hadoop、Storm、HBase、Spark) 上安裝 R。 您可以從一個唯讀的 Azure 儲存體 Blob 取得在 HDInsight 叢集上安裝 R 的範例指令碼，網址為 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)。

**相關文章**

* [在 HDInsight Hadoop 叢集上安裝和使用 R (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [在 HDInsight 中建立 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)：有關建立 HDInsight 叢集的一般資訊
* [使用指令碼動作來自訂 HDInsight 叢集][hdinsight-cluster-customize]：有關使用指令碼動作來自訂 HDInsight 叢集的一般資訊。
* [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>什麼是 R？
<a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a> 是一個用於統計計算的開放原始碼語言和環境。 R 提供數百個內建的統計函數及它自己的程式設計語言，此語言結合了函數型和物件導向程式設計的層面。 它也提供廣泛的圖形功能。 R 是各種不同廣泛領域中，大多數專業統計人員和科學家慣用的程式設計環境。

R 與 Azure Blob 儲存體 (WASB) 相容，因此便可在 HDInsight 上使用 R 來處理儲存在該處的資料。  

## <a name="install-r"></a>安裝 R
您可以從 Azure 儲存體中的唯讀 Blob，取得用以在 HDInsight 叢集上安裝 R 的[範例指令碼](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)。 本節提供有關如何在使用 Azure 入口網站建立叢集時使用範例指令碼的指示。

> [!NOTE]
> 範例指令碼是在 HDInsight 叢集版本 3.1 中所推出。 如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。
>
>

1. 當您從入口網站建立 HDInsight 叢集時，請按一下 選擇性組態，然後按一下指令碼動作。
2. 在 [指令碼動作] 頁面上，輸入下列值：

    ![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "使用指令碼動作以自訂叢集")

    <table border='1'>
        <tr><th>屬性</th><th>值</th></tr>
        <tr><td>名稱</td>
            <td>指定指令碼動作的名稱，例如<b>安裝 R</b>。</td></tr>
        <tr><td>指令碼 URI</td>
            <td>指定為了自訂叢集所叫用之指令碼的 URI，例如 <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>節點類型</td>
            <td>指定執行自訂指令碼的節點。 您可以選擇 [所有節點]<b></b>、[僅限前端節點]<b></b> 或 [僅限背景工作節點]<b></b>。
        <tr><td>參數</td>
            <td>如果指令碼要求，請指定參數。 不過，用來安裝 R 的指令碼不需要任何參數，因此可以將此欄位留白。</td></tr>
    </table>

    您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。 加入指令碼之後，請按一下核取記號以開始建立叢集。

您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，使用指令碼在 HDInsight 上安裝 R。 此文章稍後會提供這些程序的指示。

## <a name="run-r-scripts"></a>執行 R 指令碼
本節說明如何使用 HDInsight 在 Hadoop 叢集上執行 R 指令碼。

1. **建立與叢集的遠端桌面連線**：從入口網站，針對您所建立且已安裝 R 的叢集啟用遠端桌面，然後連線到叢集。 如需指示，請參閱[使用 RDP 連接至 HDInsight 叢集](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。
2. **開啟 R 主控台**：R 安裝會在前端節點的桌面上放置 R 主控台的連結。 按一下該連結以開啟 R 主控台。
3. **執行 R 指令碼**：您可以透過貼上、選取然後按 Enter 的方式，直接從 R 主控台執行 R 指令碼。 以下是一個簡單的範例指令碼，此指令碼會產生數字 1 到 100，然後將它們乘以 2。

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

前兩行會呼叫與 R 一起安裝的 RHadoop 程式庫。最後一行會將結果列印到主控台。 輸出看起來應該會像下面這樣：

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>使用 Azure PowerShell 安裝 R
請參閱[使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)。  此範例示範如何使用 Azure PowerShell 安裝 Spark。 您需要自訂指令碼以使用 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)。

## <a name="install-r-using-net-sdk"></a>使用 .NET SDK 安裝 R
請參閱[使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)。 此範例示範如何使用 .NET SDK 安裝 Spark。 您需要自訂指令碼以使用 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11)。

## <a name="see-also"></a>另請參閱
* [在 HDInsight Hadoop 叢集上安裝和使用 R (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [在 HDInsight 中建立 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)：有關建立 HDInsight 叢集的一般資訊
* [使用指令碼動作來自訂 HDInsight 叢集][hdinsight-cluster-customize]：有關使用指令碼動作來自訂 HDInsight 叢集的一般資訊。
* [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions.md)
* [在 HDInsight 叢集上安裝和使用 Spark][hdinsight-install-spark]：有關安裝 Spark 的指令碼動作範例
* [在 HDInsight 叢集上安裝 Giraph](hdinsight-hadoop-giraph-install.md)：有關安裝 Giraph 的指令碼動作範例
* [在 HDInsight 叢集上安裝 Solr](hdinsight-hadoop-solr-install-linux.md)：有關安裝 Solr 的指令碼動作範例。

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
