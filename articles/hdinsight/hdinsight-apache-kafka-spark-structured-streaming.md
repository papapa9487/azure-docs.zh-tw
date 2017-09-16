---
title: "搭配 Kafka 進行 Apache Spark 結構化串流 - Azure HDInsight | Microsoft Docs"
description: "了解如何使用 Apache Spark 串流 (DStream) 將資料傳入或傳出 Apache Kafka。 在此範例中，您使用 Jupyter Notebook 從 HDInsight 上的 Spark 串流資料。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 34c8e18e918221f0287b1078df750d8016e2529a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---

# <a name="use-spark-structured-streaming-with-kafka-preview-on-hdinsight"></a>搭配 HDInsight 上的 Kafka (預覽) 使用 Spark 結構化串流

了解如何使用 Spark 結構化串流，從 Azure HDInsight 上的 Apache Kafka 讀取資料。

Spark 結構化串流是建置在 Spark SQL 上的串流處理引擎。 它允許您進行與靜態資料批次計算相同的串流計算。 如需有關結構化串流的詳細資訊，請參閱 Apache.org 的[結構化串流程式設計手冊 [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) \(英文\)。

> [!IMPORTANT]
> 此範例使用 HDInsight 3.6 上的 Spark 2.1。 結構化串流在 Spark 2.1 上會視為 __alpha__。
>
> 本文件中的步驟建立 Azure 資源群組，其中包含 HDInsight 上的 Spark 和 HDInsight 叢集上的 Kafka。 這兩個叢集都位於 Azure 虛擬網路中，可讓 Spark 叢集直接與 Kafka 叢集通訊。
>
> 當您完成本文件中的步驟時，請記得刪除叢集，以避免產生過多的費用。

## <a name="create-the-clusters"></a>建立叢集

Apache Kafka on HDInsight 不提供透過公用網際網路存取 Kafka 訊息代理程式。 任何 Kafka 相關項目必須位於與 Kafka 叢集中節點相同的 Azure 虛擬網路。 例如，Kafka 和 Spark 叢集均位於 Azure 虛擬網路中。 下圖顯示叢集之間的通訊流動方式︰

![Azure 虛擬網路中的 Spark 和 Kafka 叢集圖表](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 服務僅限於虛擬網路內的通訊。 叢集上的其他服務 (例如 SSH 和 Ambari) 可以透過網際網路存取。 如需有關適用於 HDInsight 的公用連接埠詳細資訊，請參閱 [HDInsight 所使用的連接埠和 URI](hdinsight-hadoop-port-settings-for-services.md)。

雖然您可以手動建立 Azure 虛擬網路、Kafka 和 Spark 叢集，但使用 Azure Resource Manager 範本更輕鬆。 使用下列步驟將 Azure 虛擬網路、Kafka 和 Spark 叢集部署到 Azure 訂用帳戶。

1. 使用以下按鈕，在 Azure 入口網站中登入 Azure 並開啟範本。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager 範本位於 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**。

    此範本會建立下列資源：

    * HDInsight 3.5 叢集上的 Kafka。
    * HDInsight 3.6 叢集上的 Spark。
    * Azure 虛擬網路，其中包含 HDInsight 叢集。

    > [!IMPORTANT]
    > 此範例中使用的結構化串流 Notebook 需要 HDInsight 3.6 上的 Spark。 如果您在 HDInsight 上使用較早版本的 Spark，當使用 Notebook 時會收到錯誤。

2. 使用下列資訊來填入 [自訂部署] 區段上的項目︰
   
    ![HDInsight 自訂部署](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **資源群組**：建立群組或選取現有的群組。 此群組包含 HDInsight 叢集。

    * **位置**：選取在地理上靠近您的位置。

    * **基底叢集名稱**︰此值會做為 Spark 和 Kafka 叢集的基底名稱。 例如，輸入 **hdi** 可建立名為 spark-hdi__ 的 Spark 叢集以及名為 **kafka-hdi** 的 Kafka 叢集。

    * **叢集登入使用者名稱**：Spark 和 Kafka 叢集的系統管理員使用者名稱。

    * **叢集登入密碼**：Spark 和 Kafka 叢集的系統管理員使用者密碼。

    * **SSH 使用者名稱**︰建立 Spark 和 Kafka 叢集的 SSH 使用者。

    * **SSH 密碼**：Spark 和 Kafka 叢集的 SSH 使用者密碼。

3. 讀取**條款及條件**，然後選取 [我同意上方所述的條款及條件]。

4. 最後，核取 [釘選到儀表板]，然後選取 [購買]。 大約需要 20 分鐘的時間來建立叢集。

資源建立後，系統會顯示摘要頁面。

![VNet 及叢集的資源群組資訊](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> 請注意，HDInsight 叢集的名稱是 **spark-BASENAME** 和 **kafka-BASENAME**，其中 BASENAME 是您提供給範本的名稱。 連線到叢集時，您會在稍後步驟中使用這些名稱。

## <a name="get-the-kafka-brokers"></a>取得 Kafka 代理程式

在此範例中的程式碼會連線到 Kafka 叢集中的 Kafka 代理程式主機。 若要尋找這兩個 Kafka 代理程式主機的位址，請使用下列 PowerShell 或 Bash 範例：

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

出現提示時，輸入叢集登入 (admin) 帳戶的密碼

> [!NOTE]
> 這個範例預期 `$CLUSTERNAME` 會包含 Kafka 叢集的名稱。
>
> 這個範例會使用 [jq](https://stedolan.github.io/jq/) 公用程式來剖析 JSON 文件中的資料。

輸出大致如下：

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

儲存此資訊，因為它會在此文件中的以下各節中使用。

## <a name="get-the-notebooks"></a>取得 Notebook

在 [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming) 可取得此文件所述範例的程式碼。

## <a name="upload-the-notebooks"></a>上傳 Notebook

使用下列步驟，將專案中的 Notebook 上傳到您 HDInsight 叢集上的 Spark：

1. 在網頁瀏覽器中，連線到您 Spark 叢集上的 Jupyter Notebook。 在下列 URL 中，使用您的 Kafka 叢集名稱取代 `CLUSTERNAME`：

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    出現提示時，輸入您在建立叢集時所使用的叢集登入 (admin) 和密碼。

2. 從頁面右上角，使用 [上傳] 按鈕將 __Stream-Tweets-To_Kafka.ipynb__ 檔案上傳到叢集。 選取 [開啟] 以開始上傳。

    ![使用上傳按鈕來選取和上傳 Notebook](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![選取 KafkaStreaming.ipynb 檔案](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. 在 Notebook 清單中尋找 __Stream-Tweets-To_Kafka.ipynb__ 項目，並選取項目旁邊的 [上傳] 按鈕。

    ![若要上傳 Notebook，請對 KafkaStreaming.ipynb 項目使用 [上傳] 按鈕](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)

4. 重複步驟 1-3 以上傳 __Spark-Structured-Streaming-From-Kafka.ipynb__ Notebook。

## <a name="load-tweets-into-kafka"></a>將推文載入到 Kafka

一旦上傳檔案，選取 __Stream-Tweets-To_Kafka.ipynb__ 項目以開啟 Notebook。 請依照 Notebook 中的步驟，將推文載入到 Kafka。

## <a name="process-tweets-using-spark-structured-streaming"></a>使用 Spark 結構化串流處理推文

從 Jupyter Notebook 首頁，選取 __Spark-Structured-Streaming-From-Kafka.ipynb__ 項目。 請依照 Notebook 中的步驟，使用 Spark 結構化串流從 Kafka 載入推文。

## <a name="next-steps"></a>後續步驟

既然您已經學會如何使用 Spark 結構化串流，請參閱下列文件，以深入了解有關使用 Spark 與 Kafka 的方式：

* [如何搭配 Kafka 使用 Spark 串流 (DStream)](hdinsight-apache-spark-with-kafka.md)。
* [開始使用 Jupyter Notebook 與 HDInsight 上的 Spark](hdinsight-apache-spark-jupyter-spark-sql.md)
