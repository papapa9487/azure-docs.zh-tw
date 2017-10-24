---
title: "在 HDInsight 中搭配使用 Hadoop Hive 與 Curl - Azure | Microsoft Docs"
description: "了解如何使用 Curl 從遠端提交 Pig 工作到 HDInsight。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.openlocfilehash: 8c6877b923c6000abe3aece37a24b275462ba378
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>使用 REST 以 HDInsight 中的 Hadoop 執行 Hive 查詢

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

了解如何使用 WebHCat REST API 以 Azure HDInsight 叢集上的 Hadoop 執行 Hive 查詢。

[Curl](http://curl.haxx.se/) 用來示範如何使用原始 HTTP 要求與 HDInsight 互動。 [jq](http://stedolan.github.io/jq/) 公用程式用來處理從 REST 要求傳回的 JSON 資料。

> [!NOTE]
> 如果您已熟悉使用以 Linux 為基礎的 Hadoop 伺服器，但剛接觸 HDInsight，請參閱 [在以 Linux 為基礎的 HDInsight 上安裝 Hadoop 的須知事項](hdinsight-hadoop-linux-information.md)文件。

## <a id="curl"></a>執行 Hive 查詢

> [!NOTE]
> 在使用 cURL 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集系統管理員的使用者名稱和密碼來驗證要求。
>
> 針對本節中的命令，請將 **admin** 取代為要驗證叢集的使用者。 將 **CLUSTERNAME** 取代為您叢集的名稱。 出現提示時，請輸入使用者帳戶的密碼。
>
> 透過 [基本驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。 為確保認證安全地傳送至伺服器，請一律使用安全 HTTP (HTTPS) 提出要求。

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    您應該會收到類似以下文字的回應：

        {"status":"ok","version":"v1"}

    此命令中使用的參數如下：

    * **-u** - 用來驗證要求的使用者名稱和密碼。
    * **-G** - 指出此要求是 GET 作業。

   所有要求的 URL 開頭 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** 都相同。 路徑 **/status** 指出要求是要傳回伺服器之 WebHCat (也稱為 Templeton) 的狀態。 您也可以使用下列命令要求 Hive 的版本：

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    此要求會傳回類似以下文字的回應：

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. 使用下列命令建立名為 **log4jLogs** 的資料表：

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    下列參數可搭配此要求使用：

   * **-d** - 因為未使用 `-G`，要求會依預設使用 POST 方法。 `-d` 可指定與要求一起傳送的資料值。

     * **user.name** - 執行命令的使用者。
     * **execute** - 要執行的 HiveQL 陳述式。
     * **statusdir** - 要寫入此作業狀態的目錄。

   這些陳述式會執行下列動作：
   
   * **DROP TABLE** - 如果資料表已存在，則會刪除它。
   * **CREATE EXTERNAL TABLE** - 在 Hive 中建立新的「外部」資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。

     > [!NOTE]
     > 當您預期會由外部來源來更新基礎資料時，請使用外部資料表。 例如，自動化的資料上傳程序，或其他 MapReduce 作業。
     >
     > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。

   * **ROW FORMAT** - 設定資料格式的方式。 每個記錄中的欄位會以空格分隔。
   * **STORED AS TEXTFILE LOCATION**：資料的儲存位置 (example/data 目錄)，且資料儲存為文字。
   * **SELECT** - 選擇其資料欄 **t4** 包含值 **[ERROR]** 的所有資料列計數。 這個陳述式會傳回值 **3**，因為有三個資料列包含此值。

     > [!NOTE]
     > 請注意，在搭配 Curl 使用時，會以 `+` 字元取代 HiveQL 陳述式之間的空格。 加上引號的值若包含空格，例如分隔符號，則不應以 `+`取代。

   * **INPUT__FILE__NAME LIKE '%25.log'** - 這個陳述式會限制該搜尋僅能使用其檔名以 .log 結尾的檔案。

     > [!NOTE]
     > `%25` 是 % 的 URL 編碼格式，因此實際的條件是 `like '%.log'`。 % 必須為 URL 編碼，因為會將其視為在 URL 中的特殊字元。

   此命令會傳回可用來檢查作業狀態的作業識別碼。

       {"id":"job_1415651640909_0026"}

3. 若要檢查作業的狀態，請使用下列命令：

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    將 **JOBID** 取代為上一個步驟中所傳回的值。 例如，如果傳回值為 `{"id":"job_1415651640909_0026"}`，則 **JOBID** 會是 `job_1415651640909_0026`。

    如果作業已完成，則狀態會是 **SUCCEEDED**。

   > [!NOTE]
   > 此 Curl 要求會傳回含有作業資訊的 JavaScript 物件標記法 (JSON) 文件。 Jq 是用來只擷取狀態值。

4. 工作狀態變更為 [成功] 之後，即可從 Azure Blob 儲存體擷取工作結果。 與查詢一起傳遞的 `statusdir` 參數包含輸出檔案的位置；在此案例中為 **/example/curl**。 此位址會將輸出儲存在叢集預設儲存體的 **example/curl** 目錄中。

    您可以使用 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) 列出並下載這些檔案。 如需搭配 Azure 儲存體使用 Azure CLI 的詳細資訊，請參閱[搭配 Azure 儲存體使用 Azure CLI 2.0](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli#create-and-manage-blobs) 文件。

5. 使用下列陳述式來建立名為 **errorLogs**的新「內部」資料表：

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    這些陳述式會執行下列動作：

   * **CREATE TABLE IF NOT EXISTS** - 建立資料表 (如果不存在)。 這個陳述式會建立內部資料表，而內部資料表儲存在 Hive 資料倉儲中。 此資料表是由 Hive 所管理。

     > [!NOTE]
     > 與外部資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

   * **STORED AS ORC** - 以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 ORC 是高度最佳化且有效率的 Hive 資料儲存格式。
   * **INSERT OVERWRITE ...SELECT**- 從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。
   * **SELECT** - 從新的 **errorLogs** 資料表選取所有資料列。

6. 使用傳回的工作識別碼檢查工作的狀態。 成功之後，如先前所述使用 Azure CLI 來下載並檢視結果。 輸出應包含三行，其中都包含 **[ERROR]**。

## <a id="nextsteps"></a>接續步驟

Hive 與 HDInsight 搭配使用的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)
* [搭配 HDInsight 上的 Hadoop 使用 MapReduce](hdinsight-use-mapreduce.md)

如果您搭配使用 Tez 和 Hive，請參閱下列文件所提供的偵錯資訊：

* [在以 Linux 為基礎的 HDInsight 上使用 Ambari Tez 檢視](hdinsight-debug-ambari-tez-view.md)

如需本文件中使用之 REST API 的詳細資訊，請參閱 [WebHCat 參照 (英文)](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) 文件。

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


