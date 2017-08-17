---
title: "使用 Apache Kafka 搭配 Storm on HDInsight - Azure | Microsoft Docs"
description: "Apache Kafka 會隨著 Apache Storm on HDInsight 一起安裝。 了解如何使用 Storm 隨附的 KafkaBolt 和 KafkaSpout 元件來寫入 Kafka，然後從中讀取。 並且了解如何使用 Flux 架構來定義及提交 Storm 拓撲。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/21/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 823ff810ffc0b65ec1bb38e4dc314da1d0f9a64a
ms.contentlocale: zh-tw
ms.lasthandoff: 07/22/2017

---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>使用 Apache Kafka (預覽) 搭配 Storm on HDInsight

了解如何使用 Apache Storm 讀取和寫入 Apache Kafka。 本例也示範如何將資料從 Storm 拓撲儲存到 HDInsight 所使用的 HDFS 相容檔案系統。

> [!NOTE]
> 本文件中的步驟建立 Azure 資源群組，其中包含 Storm on HDInsight 和 Kafka on HDInsight cluster 叢集。 這兩個叢集都位於 Azure 虛擬網路中，可讓 Storm 叢集直接與 Kafka 叢集通訊。
> 
> 當您完成本文件中的步驟時，請記得刪除叢集，以避免產生過多的費用。

## <a name="get-the-code"></a>取得程式碼

在 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) 可取得本文件所使用的範例程式碼。

若要編譯此專案，您需要開發環境的下列設定：

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或更新版本。 HDInsight 3.5 或更新版本需要 Java 8。

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH 用戶端 (您需要 `ssh` 和 `scp` 命令) - 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

* 文字編輯器或整合式開發環境 (IDE)。

當您在開發工作站上安裝 Java 和 JDK 時可能會設定下列環境變數。 不過，您應該檢查它們是否存在，以及它們是否包含您系統的正確值。

* `JAVA_HOME` - 應指向已安裝 JDK 的目錄。
* `PATH` - 應該包含下列路徑：
  
    * `JAVA_HOME` (或對等的路徑)。
    * `JAVA_HOME\bin` (或對等的路徑)。
    * 已安裝 Maven 的目錄。

## <a name="create-the-clusters"></a>建立叢集

Apache Kafka on HDInsight 不提供透過公用網際網路存取 Kafka 訊息代理程式。 任何 Kafka 相關項目必須位於與 Kafka 叢集中節點相同的 Azure 虛擬網路。 例如，Kafka 和 Storm 叢集均位於 Azure 虛擬網路中。 下圖顯示叢集之間的通訊流動方式︰

![Azure 虛擬網路中的 Storm 和 Kafka 叢集圖表](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> 叢集上的其他服務 (例如 SSH 和 Ambari) 可以透過網際網路存取。 如需有關適用於 HDInsight 的公用連接埠詳細資訊，請參閱 [HDInsight 所使用的連接埠和 URI](hdinsight-hadoop-port-settings-for-services.md)。

雖然您可以手動建立 Azure 虛擬網路、Kafka 和 Storm 叢集，但使用 Azure Resource Manager 範本更輕鬆。 使用下列步驟將 Azure 虛擬網路、Kafka 和 Storm 叢集部署到 Azure 訂用帳戶。

1. 使用以下按鈕，在 Azure 入口網站中登入 Azure 並開啟範本。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager 範本位於 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet-v1.json**。 它會建立下列資源︰
    
    * Azure 資源群組
    * Azure 虛擬網路
    * Azure 儲存體帳戶
    * HDInsight 版本 3.6 上的 Kafka (三個背景工作角色節點)
    * HDInsight 版本 3.6 上的 Storm (三個背景工作角色節點)

  > [!WARNING]
  > 若要保證 Kafka 在 HDInsight 上的可用性，您的叢集必須包含至少三個背景工作角色節點。 此範本會建立包含三個背景工作角色節點的 Kafka 叢集。

2. 使用下列指引來填入 [自訂部署] 刀鋒視窗上的項目︰
   
    ![HDInsight 自訂部署](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **資源群組**：建立群組或選取現有的群組。 此群組包含 HDInsight 叢集。
   
    * **位置**：選取在地理上靠近您的位置。

    * **基底叢集名稱**︰此值會做為 Storm 和 Kafka 叢集的基底名稱。 例如，輸入 **hdi** 可建立名為 **storm-hdi** 的 Storm 叢集以及名為 **kafka-hdi** 的 Kafka 叢集。
   
    * **叢集登入使用者名稱**：Storm 和 Kafka 叢集的系統管理員使用者名稱。
   
    * **叢集登入密碼**：Storm 和 Kafka 叢集的系統管理員使用者密碼。
    
    * **SSH 使用者名稱**︰建立 Storm 和 Kafka 叢集的 SSH 使用者。
    
    * **SSH 密碼**：Storm 和 Kafka 叢集的 SSH 使用者密碼。

3. 讀取**條款及條件**，然後選取 [我同意上方所述的條款及條件]。

4. 最後，核取 [釘選到儀表板]，然後選取 [購買]。 大約需要 20 分鐘的時間來建立叢集。

一旦建立資源，即會顯示 [資源群組] 刀鋒視窗。

![Vnet 和叢集的資源群組刀鋒視窗](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> 請注意，HDInsight 叢集的名稱是 **storm-BASENAME** 和 **kafka-BASENAME**，其中 BASENAME 是您提供給範本的名稱。 連接到叢集時，您會在稍後步驟中使用這些名稱。

## <a name="understanding-the-code"></a>了解程式碼

此專案包含兩種拓撲︰

* **KafkaWriter**︰由 **writer.yaml** 檔案定義，此拓撲會使用 Apache Storm 隨附的 KafkaBolt 將隨機句子寫入 Kafka。

    此拓撲使用自訂 **SentenceSpout** 元件來產生隨機句子。

* **KafkaReader**︰由 **reader.yaml** 檔案定義，此拓撲會使用 Apache Storm 隨附的 KafkaSpout 來讀取 Kafka 中的資料，然後將資料記錄至 stdout。

    此拓撲使用 Storm HdfsBolt 將資料寫入 Storm 叢集的預設儲存體。
### <a name="flux"></a>Flux

拓撲是使用 [Flux](https://storm.apache.org/releases/1.1.0/flux.html) 來定義的。 Storm 0.10.x 引進了 Flux，可讓您區隔拓撲組態與程式碼。 若為使用 Flux 架構的拓撲，拓撲定義於 YAML 檔案中。 YAML 檔案可以納入為拓撲的一部分。 它也可以是您提交拓撲時使用的獨立檔案。 Flux 也支援執行階段的變數替代 (在此範例中使用)。

在執行階段會針對這些拓撲設定下列參數：

* `${kafka.topic}`：拓樸讀取/寫入的 Kafka 主題名稱。

* `${kafka.broker.hosts}`：Kafka 訊息代理程式執行所在的主機。 KafkaBolt 在寫入 Kafka 時會使用訊息代理程式資訊。

* `${kafka.zookeeper.hosts}`：Kafka 叢集中 Zookeeper 執行所在的主機。

如需 Flux 拓撲的詳細資訊，請參閱 [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html)。

## <a name="download-and-compile-the-project"></a>下載並編譯專案

1. 在開發環境中，從 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) 下載專案，開啟命令列，並將目錄變更為您下載專案的位置。

2. 從 **hdinsight-storm-java-kafka** 目錄，使用下列命令來編譯專案並建立部署套件︰

  ```bash
  mvn clean package
  ```

    封裝程序會在 `target` 目錄中建立名為 `KafkaTopology-1.0-SNAPSHOT.jar` 的檔案。

3. 使用下列命令將套件複製到 Storm on HDInsight 叢集。 將 **USERNAME** 替換為叢集的 SSH 使用者名稱。 將 **BASENAME** 替換為您在建立叢集時使用的基底名稱。

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    出現提示時，請輸入您在建立叢集時所使用的密碼。

## <a name="configure-the-topology"></a>設定拓撲

1. 請使用下列方法之一探索 Kafka 訊息代理程式主機：

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'
    ```

    > [!IMPORTANT]
    > Bash 範例假設 `$CLUSTERNAME` 包含 HDInsight 叢集的名稱。 它也假設已安裝 [jq](https://stedolan.github.io/jq/)。 出現提示時，輸入叢集登入帳戶的密碼。

    傳回的值類似下列文字︰

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    儲存這個值以便稍後使用。

2. 請使用下列方法之一探索 Kafka Zookeeper 主機：

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    ```

    > [!IMPORTANT]
    > Bash 範例假設 `$CLUSTERNAME` 包含 HDInsight 叢集的名稱。 它也假設已安裝 [jq](https://stedolan.github.io/jq/)。 出現提示時，輸入叢集登入帳戶的密碼。

    傳回的值類似下列文字︰

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk3-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    儲存這個值以便稍後使用。

3. 編輯專案根目錄中的 `dev.properties` 檔案。 將訊息代理程式和 Zookeeper 主機資訊新增至本檔案相符的行。 下例是使用先前步驟中的範例值所設定：

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk3-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. 儲存 `dev.properties` 檔案，然後使用下列命令將它上傳至 Storm 叢集：

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
    ```

    將 **USERNAME** 替換為叢集的 SSH 使用者名稱。 將 **BASENAME** 替換為您在建立叢集時使用的基底名稱。

## <a name="start-the-writer"></a>開始寫入器

1. 使用下列命令來透過 SSH 連接到 Storm 叢集。 將 **USERNAME** 替換為建立叢集時所使用的 SSH 使用者名稱。 將 **BASENAME** 替換為建立叢集時使用的基底名稱。

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    出現提示時，請輸入您在建立叢集時所使用的密碼。
   
    如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 從 SSH 連線，使用下列命令來建立拓撲使用的 Kafka 主題：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    以您在上一節擷取的 Zookeeper 主機資訊取代 `$KAFKAZKHOSTS`。

2. 在連往 Storm 叢集的 SSH 連線中，使用下列命令來啟動寫入器拓撲：

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    此命令使用的參數如下：

    * `org.apache.storm.flux.Flux`︰使用 Flux 來設定及執行此拓撲。

    * `--remote`：將拓撲提交至 Nimbus。 拓撲會分散於叢集中的背景工作節點。

    * `-R /writer.yaml`︰使用 `writer.yaml` 檔案來設定拓撲。 `-R` 表示此資源包含在 jar 檔案中。 剛檔案位於 jar 的根目錄中，所以 `/writer.yaml` 是它的路徑。

    * `--filter`：使用 `dev.properties` 檔案的值填入 `writer.yaml` 拓撲的項目。 例如，使用檔案的 `kafka.topic` 項目值取代拓撲定義的 `${kafka.topic}` 項目。

5. 一旦啟動拓撲，請使用下列命令確認它將資料寫入 Kafka 主題：

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    以您在上一節擷取的 Zookeeper 主機資訊取代 `$KAFKAZKHOSTS`。

    此命令會使用 Kafka 隨附的指令碼來監視主題。 稍後，它應該會開始傳回已寫入主題的隨機句子。 輸出類似於下列範例：

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    使用 Ctrl + c 來停止指令碼。

## <a name="start-the-reader"></a>開始讀取器

1. 在 Storm 叢集的 SSH 工作階段中，使用下列命令來啟動讀取器拓撲：

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml -e
  ```

2. 拓撲啟動後，請開啟 Storm UI。 此 Web UI 位於 https://storm-BASENAME.azurehdinsight.net/stormui。 將 __BASENAME__ 替換為建立叢集時使用的基底名稱。 

    出現提示時，使用建立叢集時所用的系統管理員登入名稱 (預設為 `admin`) 和密碼。 您會看到類似下圖的網頁︰

    ![Storm UI](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. 從 Storm UI 選取 [拓樸摘要] 區段中的 __kafka-reader__ 連結，以顯示 __kafka-reader__ 拓樸相關資訊。

    ![Storm Web UI 的拓撲摘要區段](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. 選取 [Bolt (所有時間)] 區段中的 __logger-bolt__ 連結，以顯示 logger-bolt 元件的執行個體相關資訊。

    ![Bolt 區段中的 Logger-bolt 連結](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. 在 [執行程式] 區段中，選取 [連接埠] 欄中的連結，以顯示此元件執行個體的記錄資訊。

    ![執行程式連結](./media/hdinsight-apache-storm-with-kafka/executors.png)

    記錄檔包含從 Kafka 主題讀取的資料記錄檔。 記錄檔中的資訊類似下列文字︰

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>停止拓撲

在連往 Storm 叢集的 SSH 工作階段中，使用下列命令來停止 Storm 拓撲：

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

因為本文件中的步驟會在相同的 Azure 資源群組中建立兩個叢集，您可以在 Azure 入口網站中刪除資源群組。 刪除資源群組，會移除依照本文指示建立的所有資源。

## <a name="next-steps"></a>後續步驟

如需更多可搭配 Storm on HDInsight 使用的拓撲範例，請參閱 [Storm 拓撲和元件範例](hdinsight-storm-example-topology.md)。

如需部署和監視以 Linux 為基礎的 HDInsight 上的拓撲相關資訊，請參閱[部署和管理以 Linux 為基礎的 HDInsight 上的 Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)
