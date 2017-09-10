---
title: "搭配使用 MapReduce 和 Curl 與 HDInsight 中的 Hadoop - Azure | Microsoft Docs"
description: "了解如何使用 Curl 從遠端搭配執行 MapReduce 工作與 HDInsight 上的 Hadoop。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/12/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 8238bb829df95dcb8c99c0b7fff53c627a56f47c
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>使用 REST 搭配 HDInsight 上的 Hadoop 執行 MapReduce 作業

學習如何使用 WebHCat REST API 在 HDInsight 叢集的 Hadoop 上執行 MapReduce 作業。 Curl 用來示範如何使用原始 HTTP 要求與 HDInsight 互動，以執行 MapReduce 工作。

> [!NOTE]
> 如果您已熟悉使用以 Linux 為基礎的 Hadoop 伺服器，但剛接觸 HDInsight，請參閱[在以 Linux 為基礎的 HDInsight 上安裝 Hadoop 的須知事項](hdinsight-hadoop-linux-information.md)文件。


## <a id="prereq"></a>必要條件

* 一個 Hadoop on HDInsight 叢集
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>使用 Curl 執行 MapReduce 工作

> [!NOTE]
> 在使用 Curl 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集管理員使用者名稱和密碼來驗證要求。 您必須使用叢集名稱，作為用來將要求傳送至伺服器之 URI 的一部分。
>
> 針對本節中的命令，將 **USERNAME** 取代為向叢集驗證的使用者，並將 **PASSWORD** 取代為使用者帳戶的密碼。 將 **CLUSTERNAME** 取代為您叢集的名稱。
>
> 使用 [基本存取驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。 您應該一律使用 HTTPS 提出要求，確保認證安全地傳送至伺服器。


1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    您應該會收到類似下列 JSON 的回應：

        {"status":"ok","version":"v1"}

    此命令中使用的參數如下：

   * **-u**：指出用來驗證要求的使用者名稱和密碼
   * **-G**：指出此作業是 GET 要求

     所有要求的 URI 開頭 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** 都相同。

2. 若要提交 MapReduce 工作，請使用下列命令：

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    URI 結尾 (/mapreduce/jar) 會告訴 WebHCat，此要求會從 jar 檔案中的類別啟動 MapReduce 作業。 此命令中使用的參數如下：

   * **-d**：未使用 `-G`，因此要求會依預設使用 POST 方法。 `-d` 可指定與要求一起傳送的資料值。
    * **user.name**：執行命令的使用者
    * **jar**：包含要執行之類別的 jar 檔案位置
    * **class**：包含 MapReduce 邏輯的類別
    * **arg**︰要傳遞到 MapReduce 作業的引數。 在此案例中，是用於輸出的輸入文字檔和目錄

     此命令應該會傳回可用來檢查工作狀態的工作識別碼：

       {"id":"job_1415651640909_0026"}

3. 若要檢查作業的狀態，請使用下列命令：

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    將 **JOBID** 取代為上一個步驟中所傳回的值。 例如，如果傳回值為 `{"id":"job_1415651640909_0026"}`，則 JOBID 會是 `job_1415651640909_0026`。

    如果作業已完成，傳回的狀態會是 `SUCCEEDED`。

   > [!NOTE]
   > 此 Curl 要求會傳回含有作業資訊的 JSON 文件。 jq 是用來只擷取狀態值。

4. 當作業狀態變更為 `SUCCEEDED` 之後，您就可以從 Azure Blob 儲存體擷取作業結果。 隨查詢一起傳遞的 `statusdir` 參數包含輸出檔案的位置。 在此範例中，位置是 `/example/curl`。 此位址會將作業的輸出儲存在叢集預設儲存體的 `/example/curl` 中。

您可以使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 列出並下載這些檔案。 如需從 Azure CLI 使用 Blob 的詳細資訊，請參閱[搭配 Azure 儲存體使用 Azure CLI 2.0](../storage/common/storage-azure-cli.md#create-and-manage-blobs) 文件。

## <a id="nextsteps"></a>接續步驟

如需 HDInsight 中 MapReduce 工作的一般資訊：

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)
* [搭配 HDInsight 上的 Hadoop 使用 Pig](hdinsight-use-pig.md)

如需本文中使用的 REST 介面的詳細資訊，請參閱 [WebHCat 參照](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。
