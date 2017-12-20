---
title: "Azure HDInsight 工具 - 使用適用於 Hive、LLAP 或 pySpark 的 Visual Studio Code | Microsoft Docs"
description: "了解如何使用適用於 Visual Studio Code 的 Azure HDInsight 工具來建立及提交查詢和指令碼。"
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: d6ca3bcb91261a863444bc331c78adf44844be56
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>使用適用於 Visual Studio Code 的 Azure HDInsight 工具

了解如何使用適用於 Visual Studio Code (VS Code) 的 Azure HDInsight 工具來建立、提交 Hive 批次作業、互動式 Hive 查詢和 pySpark 指令碼。 Azure HDInsight 工具可以安裝在 VS Code 所支援的平台上。 這些包括 Windows、Linux 及 MacOS。 您可以找到不同平台的必要條件。


## <a name="prerequisites"></a>必要條件

需要有下列項目才能完成本文中的步驟：

- HDInsight 叢集。  若要建立叢集，請參閱[開始使用 HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md)。
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。
- [Mono](http://www.mono-project.com/docs/getting-started/install/)。 Linux 和 macOS 才需要 Mono。

## <a name="install-the-hdinsight-tools"></a>安裝 HDInsight 工具
   
安裝完必要條件之後，您就可以安裝適用於 VS Code 的 Azure HDInsight 工具。 

**安裝 Azure HDInsight 工具**

1. 開啟 Visual Studio Code。

2. 在左窗格中，選取 [擴充功能]。 在搜尋方塊中輸入 **HDInsight**。

3. 選取 [Azure HDInsight 工具] 旁邊的 [安裝]。 幾秒鐘後，[安裝] 按鈕會變為 [重新載入]。

4. 選取 [重新載入] 來啟動 **Azure HDInsight 工具**擴充功能。

5. 選取 [重新載入視窗] 進行確認。 您可以在 [擴充功能] 窗格中看到 **Azure HDInsight 工具**。

   ![HDInsight for Visual Studio Code Python 安裝](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>開啟 HDInsight 工作區

先在 VS Code 中建立工作區，才能連線到 Azure。

**開啟工作區**

1. 在 [檔案] 功能表上，選取 [開啟資料夾]。 然後將現有的資料夾指定為工作資料夾，或建立一個新資料夾。 該資料夾會出現在左窗格中。

2. 在左窗格上，選取工作資料夾旁邊的 [新增檔案] 圖示。

   ![新增檔案](./media/hdinsight-for-vscode/new-file.png)

3. 將新的檔案命名為 .hql (Hive 查詢) 或 .py (Spark 指令碼) 副檔名。 請注意工作資料夾中會自動新增 **XXXX_hdi_settings.json** 組態檔。

4. 從 [檔案總管] 開啟 **XXXX_hdi_settings.json**，或以滑鼠右鍵按一下指令碼編輯器以選取 [設定組態]。 您可以設定登入項目、預設叢集和作業提交參數，如檔案中的範例所示。 您也可以讓其餘參數保持空白。

## <a name="connect-to-azure"></a>連接到 Azure

您必須先連線到 Azure 帳戶，才可以從 VS Code 將指令碼提交到 HDInsight 叢集。

**連接到 Azure**

1. 建立新的工作資料夾和新的指令碼檔案 (如果您還沒有這兩個項目)。

2. 以滑鼠右鍵按一下指令碼編輯器，然後從快顯功能表選取 [HDInsight: 登入]。 您也可以輸入 **CTRL+SHIFT+P**，然後輸入 **HDInsight: 登入**。

    ![「適用於 Visual Studio Code 的 HDInsight 工具」登入](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. 若要登入，請遵循 [輸出] 窗格中的登入指示。

    **Azure：**![適用於 Visual Studio Code 的 HDInsight 工具登入資訊](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    連線之後，您的 Azure 帳戶名稱會顯示在 VS Code 視窗左下角的狀態列上。 

    > [!NOTE]
    > 因為已知的 Azure 驗證問題，所以您需要以私用模式或無痕模式開啟瀏覽器。 如果 Azure 帳戶已啟用雙因素驗證，建議使用電話驗證而不是 Pin 碼驗證。
  

4. 在指令碼編輯器上按一下滑鼠右鍵，以開啟操作功能表。 您可以從快顯功能表執行下列工作：

    - 登出
    - 列出叢集
    - 設定預設叢集
    - 提交互動式 Hive 查詢
    - 提交 Hive 批次指令碼
    - 提交互動式 PySpark 查詢
    - 提交 PySpark 批次指令碼
    - 設定組態

## <a name="list-hdinsight-clusters"></a>列出 HDInsight 叢集

若要測試連線，您可以列出 HDInsight 叢集：

**列出 Azure 訂用帳戶底下的 HDInsight 叢集**
1. 開啟工作區，然後連線到 Azure。 如需詳細資訊，請參閱[開啟 HDInsight 工作區](#open-hdinsight-workspace)和[連線到 Azure](#connect-to-azure)。

2. 以滑鼠右鍵按一下指令碼編輯器，然後從快顯功能表選取 [HDInsight: 列出叢集]。 

3. [輸出] 窗格中會出現 Hive 和 Spark 叢集。

    ![設定預設叢集組態](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>設定預設叢集
1. 開啟工作區，然後連線到 Azure。 請參閱[開啟 HDInsight 工作區](#open-hdinsight-workspace)和[連線到 Azure](#connect-to-azure)。

2. 以滑鼠右鍵按一下指令碼編輯器，然後選取 [HDInsight: 設定預設叢集]。 

3. 選取某個叢集來作為目前指令碼檔案的預設叢集。 工具會自動更新組態檔 **XXXX_hdi_settings.json**。 

   ![設定預設叢集組態](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>設定 Azure 環境 
1. 選取 **CTRL+SHIFT+P** 以開啟命令選擇區。

2. 輸入 **HDInsight: 設定 Azure 環境**。

3. 在 Azure 和 AzureChina 中擇一作為預設登入項目。

4. 同時，工具已將您的預設登入項目儲存在 **XXXX_hdi_settings.json** 中。 您也可以直接在這個組態檔內更新此登入項目。 

   ![設定預設的登入項目組態](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>提交互動式 Hive 查詢

使用適用於 VS Code 的 HDInsight Tools，您可以將互動式 Hive 查詢提交到 HDInsight 互動式查詢叢集。

1. 建立新的工作資料夾和新的 Hive 指令碼檔案 (如果您還沒有這兩個項目)。

2. 連線到 Azure 帳戶，然後設定預設叢集 (如果您還未設定)。

3. 複製以下程式碼並貼到 Hive 檔案中，然後儲存該檔案。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. 在指令碼編輯器上按一下滑鼠右鍵，然後選取 [HDInsight: Hive 互動式] 來提交查詢。 工具也可讓您使用快顯功能表來提交程式碼區塊，而非整個指令碼檔案。 不久之後，查詢結果就會顯示在新的索引標籤中。

   ![互動式 Hive 結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - [結果] 面板：您可以將整個結果以 CSV、JSON 或 Excel 檔案的形式儲存到本機路徑，或只選取多行。

    - [訊息] 面板：當您選取**行號**時，它就會跳至執行中指令碼的第一行。

相較於[執行 Hive 批次工作](#submit-hive-batch-scripts)，執行互動式查詢所需的時間少很多。

## <a name="submit-hive-batch-scripts"></a>提交 Hive 批次指令碼

1. 建立新的工作資料夾和新的 Hive 指令碼檔案 (如果您還沒有這兩個項目)。

2. 連線到 Azure 帳戶，然後設定預設叢集 (如果您還未設定)。

3. 複製以下程式碼並貼到 Hive 檔案中，然後儲存該檔案。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. 在指令碼編輯器上按一下滑鼠右鍵，然後選取 [HDInsight: Hive 批次] 來提交 Hive 作業。 

4. 選取您要提交至的叢集。  

    在提交 Hive 作業之後，[輸出] 面板就會顯示提交成功資訊和 jobid。 Hive 作業也會開啟**網頁瀏覽器**，其中顯示即時的作業記錄和狀態。

   ![提交 Hive 作業結果](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

相較於提交批次作業，[提交互動式 Hive 查詢](#submit-interactive-hive-queries)所需的時間少很多。

## <a name="submit-interactive-pyspark-queries"></a>提交互動式 PySpark 查詢
適用於 VS Code 的 HDInsight 工具也可讓您將互動式 PySpark 查詢提交給 Spark 叢集。
1. 建立新的工作資料夾和副檔名為 .py 的新指令碼檔案 (如果您還沒有這兩個項目)。

2. 連線到 Azure 帳戶 (如果您尚未這樣做)。

3. 複製以下程式碼並貼到指令碼檔案中：
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. 反白顯示這些指令碼。 然後在指令碼編輯器上按一下滑鼠右鍵，並選取 [HDInsight: PySpark 互動式]。

5. 如果您尚未在 VS Code 中安裝 **Python** 擴充功能，請選取 [安裝] 按鈕，如下圖所示：

    ![HDInsight for Visual Studio Code Python 安裝](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. 如果尚未安裝 Python 環境，請在您的系統中進行安裝。 
   - 針對 Windows，請下載並安裝 [Python](https://www.python.org/downloads/)。 然後確定 `Python` 和 `pip` 位於您的系統路徑中。

   - 如需適用於 macOS 和 Linux 的指示，請參閱[設定 Visual Studio Code 的 PySpark 互動式環境](set-up-pyspark-interactive-environment.md)。

7. 選取一個叢集來提交 PySpark 查詢。 不久之後，右邊的新索引標籤中就會顯示查詢結果：

   ![提交 Python 作業結果](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. 此工具也支援 **SQL 子句**查詢。

   ![提交 Python 作業結果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) 當您執行查詢時，提交狀態會顯示在底部狀態列的左邊。 當狀態為 [PySpark 核心 (忙碌)] 時，請勿提交其他查詢。 

>[!NOTE]
>叢集可以維護工作階段資訊。 已定義的變數、函式和對應值會保留在工作階段中，以便相同叢集的多個服務呼叫進行參考。 
 

## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批次作業

1. 建立新的工作資料夾和副檔名為 .py 的新指令碼檔案 (如果您還沒有這兩個項目)。

2. 連線至 Azure 帳戶 (如果您尚未這樣做)。

3. 複製以下程式碼並貼到指令碼檔案中：

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. 在指令碼編輯器上按一下滑鼠右鍵，然後選取 [HDInsight: PySpark 批次]。 

5. 選取一個叢集來提交 PySpark 作業。 

   ![提交 Python 作業結果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

在提交 Python 作業後，提交記錄會出現在 VS Code 的 [輸出] 視窗中。 此外也會顯示 **Spark UI URL** 和 **Yarn UI URL**。 您可以在網頁瀏覽器中開啟 URL 來追蹤作業狀態。


## <a name="additional-features"></a>其他功能

HDInsight for VS Code 支援下列功能︰

- **IntelliSense 自動完成**。 關鍵字、方法、變數等的建議快顯視窗。 不同圖示代表不同類型的物件。

    ![「適用於 Visual Studio Code 的 HDInsight 工具」IntelliSense 物件類型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 錯誤標記**。 語言服務會對 Hive 指令碼的編輯錯誤加上底線。     
- **語法醒目顯示**。 語言服務會使用不同顏色來區別變數、關鍵字、資料類型、函式等等。 

    ![「適用於 Visual Studio Code 的 HDInsight 工具」語法醒目顯示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>後續步驟

### <a name="demo"></a>示範
* HDInsight for VS Code：[影片](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和擴充功能

* [使用適用於 IntelliJ 的 Azure 工具組透過 VPN 對 Spark 應用程式進行遠端偵錯](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用適用於 IntelliJ 的 Azure 工具組透過 SSH 對 Spark 應用程式進行遠端偵錯](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具建立 Spark 應用程式](spark/apache-spark-eclipse-tool-plugin.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](spark/apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](spark/apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Hive 資料視覺化](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [設定 Visual Studio Code 的 PySpark 互動式環境](set-up-pyspark-interactive-environment.md)
* [使用 Zeppelin 在 Azure HDInsight 中執行 Hive 查詢](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>案例
* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](spark/apache-spark-use-bi-tools.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](spark/apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 進行網站記錄分析](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>建立和執行應用程式
* [使用 Scala 建立獨立應用程式](spark/apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行作業](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](spark/apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](spark/apache-spark-job-debugging.md)



