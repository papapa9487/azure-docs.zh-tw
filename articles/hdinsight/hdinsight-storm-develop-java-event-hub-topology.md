---
title: "使用 Java 在事件中樞內透過 Storm on HDInsight 處理事件 | Microsoft Docs"
description: "了解如何使用 Maven 建立的 Java Storm 拓撲處理事件中樞資料。"
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/13/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: db278f2ecd025257a969e3a9f05f5269a659999d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/13/2017

---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>使用 Storm on HDInsight 處理 Azure 事件中樞的事件 (Java)

了解如何透過 Storm on HDInsight 使用 Azure 事件中樞。 此範例使用 Java 架構的元件，在 Azure 事件中樞讀取和寫入資料。

Azure 事件中樞可讓您從網站、應用程式和裝置處理巨量資料。 事件中樞 Spout 可讓您輕鬆地使用 Apache Storm on HDInsight 進行即時分析資料。 您也可以使用事件中樞 Bolt 將資料從 Storm 寫入事件中樞。

## <a name="prerequisites"></a>必要條件

* Apache Storm on HDInsight 叢集 3.6 版。 如需詳細資訊，請參閱[開始使用 Storm on HDInsight cluster](hdinsight-apache-storm-tutorial-get-started-linux.md)。

    > [!IMPORTANT]
    > Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* [Azure 事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)。

* [Oracle Java Developer Kit (JDK) 第 8 版](http://www.oracle.com/technetwork/java/javase/downloads/index.html)或同等版本，例如 [OpenJDK](http://openjdk.java.net/)。

* [Maven](https://maven.apache.org/download.cgi)：Maven 是 Java 專案的專案建置系統。

* 文字編輯器或整合開發環境 (IDE)。

    > [!NOTE]
    > 您的編輯器或 IDE 可能具有處理 Maven 的特定功能，但未記載在這份文件中。 如需編輯環境功能的詳細資訊，請參閱所使用產品的文件。

    * SSH 用戶端。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

* `ssh` 和 `scp` 命令。 這些命令用來將檔案複製到 HDInsight 叢集。 在 Windows 上，您可以透過 Windows 10 的 Bash 執行這些命令。

## <a name="understanding-the-example"></a>了解範例

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) 範例包含兩個拓撲：

`resources/writer.yaml` 拓撲會將隨機資料寫入 Azure 事件中樞。 資料由 `DeviceSpout` 產生，而且是隨機裝置識別碼和裝置值。 所以它是模擬會發出字串識別碼和數值的部分硬體。

有三個 `resources/reader.yaml` 拓撲會從事件中樞讀取資料 (由 EventHubWriter 寫入的資料)、剖析 JSON 資料，然後記錄 `deviceId` 和 `deviceValue` 資料。

資料會在寫入事件中樞之前格式化為 JSON 文件，當讀取器讀取時會從 JSON 剖析，並且進入 Tuple。 JSON 格式如下所示：

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>專案組態

`POM.xml` 檔案包含此 Maven 專案的組態資訊。 有趣的部分是：

#### <a name="event-hub-components"></a>事件中樞元件

讀取和寫入至 Azure 事件中樞的元件位於 [HDInsight 儲存機制](https://github.com/hdinsight/mvn-repo)。 `POM.xml` 檔案的下列各區段可從這個儲存機制載入元件

```xml
<repositories>
    <repository>
        <id>hdinsight-examples</id>
        <url>http://raw.github.com/hdinsight/mvn-repo/master</url>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>EventHubs Storm Spout 相依性

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>${storm.eventhub.version}</version>
</dependency>
```

這個 xml 會定義 eventhubs 套件的相依性，當中包含用以從「事件中樞」讀取的 Spout 和寫入「事件中樞」的 Bolt。

```xml
</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

這個 XML 會將專案設定為產生 Java 8 的輸出，而輸出將由 HDInsight 3.5 或更新版本使用。

#### <a name="the-maven-shade-plugin"></a>maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
    </transformers>
    <!-- Keep us from getting a bad signature error -->
    <filters>
    <filter>
        <artifact>*:*</artifact>
        <excludes>
            <exclude>META-INF/*.SF</exclude>
            <exclude>META-INF/*.DSA</exclude>
            <exclude>META-INF/*.RSA</exclude>
        </excludes>
    </filter>
    </filters>
</configuration>
<executions>
    <execution>
    <phase>package</phase>
    <goals>
        <goal>shade</goal>
    </goals>
    </execution>
</executions>
</plugin>
```

這個 XML 會將解決方案設定為把輸出封裝成 uber jar 。 jar 包含專案程式碼和必要的相依性。 它也用來：

* 重新命名相依性的授權檔案。
* 排除安全性/簽章。
* 請確定介面相同的多個實作會合併成一個項目。

這些組態設定可防止執行階段錯誤。

#### <a name="topology-definitions"></a>拓撲定義

這個範例會使用 [Flux](https://storm.apache.org/releases/1.1.0/flux.html) 架構。 此架構則使用 YAML 來定義拓撲。 主要優點為您不是將拓撲寫入 Java 程式碼。 由於定義是 YAML，因此您可以在提交拓撲之前變更它，而不必重新編譯所有項目。

__writer.yaml__：

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubwriter"

components:
  # Configure the Event Hub spout
  - id: "eventhubbolt-config"
    className: "org.apache.storm.eventhubs.bolt.EventHubBoltConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.write.policy.name}"
      - "${eventhub.write.policy.key}"
      - "${eventhub.namespace}"
      - "servicebus.windows.net"
      - "${eventhub.name}"

spouts:
  - id: "device-emulator-spout"
    className: "com.microsoft.example.DeviceSpout"
    parallelism: ${eventhub.partitions}

bolts:
  - id: "eventhub-bolt"
    className: "org.apache.storm.eventhubs.bolt.EventHubBolt"
    constructorArgs:
      - ref: "eventhubbolt-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

# How data flows through the components
streams:
  - name: "spout -> eventhub" # just a string used for logging
    from: "device-emulator-spout"
    to: "eventhub-bolt"
    grouping:
        type: SHUFFLE

  - name: "spout -> logger"
    from: "device-emulator-spout"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

__reader.yaml__：

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubreader"

components:
  # Configure the Event Hub spout
  - id: "eventhubspout-config"
    className: "org.apache.storm.eventhubs.spout.EventHubSpoutConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.read.policy.name}"
      - "${eventhub.read.policy.key}"
      - "${eventhub.namespace}"
      - "${eventhub.name}"
      - ${eventhub.partitions}

spouts:
  - id: "eventhub-spout"
    className: "org.apache.storm.eventhubs.spout.EventHubSpout"
    constructorArgs:
      - ref: "eventhubspout-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

bolts:
  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

  # Parses from JSON into tuples
  - id: "parser-bolt"
    className: "com.microsoft.example.ParserBolt"
    parallelism: ${eventhub.partitions}

# How data flows through the components
streams:
  - name: "spout -> parser" # just a string used for logging
    from: "eventhub-spout"
    to: "parser-bolt"
    grouping:
        type: SHUFFLE

  - name: "parser -> log-bolt"
    from: "parser-bolt"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

#### <a name="tell-the-topology-about-event-hub"></a>分辨事件中樞的相關拓撲

在執行階段，`dev.properties` 檔案用來將事件中樞設定傳遞至拓撲。 下列範例是檔案的預設內容：

```yaml
eventhub.write.policy.name: writer
eventhub.write.policy.key: your_key_here
eventhub.read.policy.name: reader
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: storm
eventhub.partitions: 2
```

## <a name="configure-environment-variables"></a>設定環境變數

當您在開發工作站上安裝 Java 和 JDK 時可能會設定下列環境變數。 不過，您應該檢查它們是否存在，以及它們是否包含您系統的正確值。

* **JAVA_HOME** - 應該指向已安裝 Java 執行階段環境 (JRE) 的目錄。 例如，在 Unix 或 Linux 散發套件上，它的值應該類似 `/usr/lib/jvm/java-7-oracle` 在 Windows 中，它的值應該類似 `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - 應該包含下列路徑：

  * **JAVA_HOME** (或對等的路徑)
  * **JAVA_HOME\bin** (或對等的路徑)
  * 已安裝 Maven 的目錄

## <a name="configure-event-hub"></a>設定事件中樞

事件中樞是此範例的資料來源。 使用下列步驟建立事件中樞。

1. 從 [Azure 傳統入口網站](https://manage.windowsazure.com)選取 [新增]  >  [服務匯流排]  >  [事件中樞]  > [自訂建立]。

2. 在 [新增事件中樞] 畫面上，輸入 [事件中樞名稱]。 選取要建立中樞的 [區域]，然後建立新的命名空間或選取現有的命名空間。 最後，按一下**箭頭**進行下一步。

    ![精靈頁面 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

   > [!NOTE]
   > 選取與 Storm on HDInsight 伺服器相同的 [位置]，可降低延遲和成本。

3. 在 [設定事件中樞] 畫面中，輸入 [資料分割計數] 及 [訊息保留] 值。 在此範例中，資料分割計數使用 10，訊息保留使用 1。 請記下資料分割計數，因為您稍後會用到這個值。

    ![精靈頁面 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. 建立事件中樞之後，依序選取命名空間、[事件中樞] ，然後選取您先前建立的事件中樞。
5. 選取 [設定]，然後使用下列資訊建立兩個新的存取原則：

    <table>
    <tr><th>名稱</th><th>權限</th></tr>
    <tr><td>寫入器</td><td>傳送</td></tr>
    <tr><td>讀取者</td><td>接聽</td></tr>
    </table>

    建立權限之後，在頁面底部選取 **儲存** 圖示。 這些共用的存取原則是用來讀取和寫入事件中樞。

    ![原則](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. 儲存原則之後，請使用頁面底部的 [共用存取金鑰產生器] 來擷取 [寫入器] 和 [讀取器] 原則的金鑰。 儲存這些金鑰。

## <a name="download-and-build-the-project"></a>下載和建置專案

1. 從 GitHub 下載專案： [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)。 您可以將封裝下載為 zip 封存，或使用 [git](https://git-scm.com/) 以在本機複製專案。

2. 使用事件中樞設定修改 `dev.properties` 檔案。

3. 使用下列項目建置和封裝專案：

        mvn package

    這個命令會下載必要的相依性、進行建置，然後封裝專案。 輸出會在 **/target** 目錄儲存為 **EventHubExample-1.0-SNAPSHOT.jar**。

## <a name="test-locally"></a>本機測試

由於這些拓撲中只會讀取和寫入至事件中樞，因此如果您有 [Storm 開發環境](http://storm.apache.org/releases/current/Setting-up-development-environment.html)，則可以在本機測試它們。 使用下列步驟，以在開發環境的本機中執行：

1. 執行寫入器：

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /writer.yaml --filter dev.properties

2. 執行讀取器：

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /reader.yaml --filter dev.properties

> [!TIP]
> * `--local`：以本機模式執行拓撲 (非分散式)。
> * `-R /writer.yaml`：從封裝在 Jar 中的 `resources` 載入拓撲定義。 如果拓撲為本機檔案系統上的檔案，請改為指定其路徑作為最後一個參數。
> * `--filter dev.properties`：使用 `dev.properties` 的內容在拓撲定義中填入值。 例如， `${eventhub.read.policy.name}`。

在本機執行時，輸出會記錄至主控台。 使用 __Ctrl+C__ 來停止拓撲。

## <a name="deploy-the-topologies"></a>部署拓撲

1. 使用 SCP 將 jar 封裝複製到您的 HDInsight 叢集。 將 USERNAME 取代為用於您的叢集的 SSH 使用者。 將 CLUSTERNAME 取代為 HDInsight 叢集的名稱：

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    如果您針對 SSH 帳戶使用密碼，系統會提示您輸入密碼。 如果您搭配帳戶使用 SSH 金鑰，可能需要使用 `-i` 參數來指定金鑰檔的路徑。 例如， `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    此命令會將檔案複製到叢集上 SSH 使用者的主目錄中。

2. 完成上傳檔案之後，使用 SSH 以連接到 HDInsight 叢集。 將 **USERNAME** 取代為您的 SSH 登入名稱。 將 **CLUSTERNAME** 取代為 HDInsight 叢集名稱：

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > 如果您針對 SSH 帳戶使用密碼，系統會提示您輸入密碼。 如果您搭配帳戶使用 SSH 金鑰，可能需要使用 `-i` 參數來指定金鑰檔的路徑。 下列範例會從 `~/.ssh/id_rsa` 載入私密金鑰：
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. 使用下列命令以啟動拓撲：

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties

    > [!TIP]
    > * `--remote`：將拓撲提交到 Nimbus 服務，該服務會在叢集中的背景工作節點上啟動拓撲。

4. 若要檢視記錄的資料，請移至 https://CLUSTERNAME.azurehdinsight.net/stormui，其中 __CLUSTERNAME__ 是 HDInsight 叢集的名稱。 選取拓撲，然後向下切入至元件。 選取元件執行個體的 __port__ 項目來檢視記錄的資訊。

5. 使用下列命令以停止拓撲：

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>後續步驟

* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

