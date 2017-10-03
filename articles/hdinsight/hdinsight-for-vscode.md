---
title: "Azure HDInsight 工具 - 使用適用於 Hive、LLAP 或 pySpark 的 Visual Studio Code | Microsoft Docs"
description: "了解如何使用適用於 Visual Studio Code 的 Azure HDInsight 工具來建立、提交查詢和指令碼。"
Keywords: "VScode,Azure HDInsight 工具,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,互動式 Hive,互動式查詢"
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
ms.date: 09/21/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 17900c88fcfccd9c56ac548d1e9167b16a92b433
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="use-azure-hdinsight-tool-for-visual-studio-code-hive-llap-or-pyspark"></a>使用適用於 Visual Studio Code 的 Azure HDInsight 工具 (Hive、LLAP 或 pySpark)

了解如何使用適用於 Visual Studio Code (VSCode) 的 Azure HDInsight 工具來建立、提交 Hive 批次作業、互動式 Hive 查詢和 pySpark 指令碼。 Azure HDInsight 工具可以安裝在 VSCode 支援的平台上，包括 Windows、Linux 和 MacOS。 您可以找到不同平台的必要條件。


## <a name="prerequisites"></a>必要條件

必須有下列項目才能完成本文章：

- HDInsight 叢集。  若要建立叢集，請參閱[開始使用 HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md)。
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。
- [Mono](http://www.mono-project.com/docs/getting-started/install/)。 Linux 和 MacOS 才需要 Mono。

## <a name="install-the-hdinsight-tools"></a>安裝 HDInsight 工具
   
安裝完必要條件之後，您就可以安裝適用於 VSCode 的 Azure HDInsight 工具。 

**安裝 Azure HDInsight 工具**

1. 開啟 **Visual Studio Code**。
2. 按一下左窗格的 [擴充功能]。 在搜尋方塊中輸入 **Azure HDInsight 工具**。
3. 按一下 [Azure HDInsight 工具] 旁邊的 [安裝]。 幾秒鐘後，[安裝] 按鈕就會變為 [重新載入]。
4. 按一下 [重新載入] 來啟動 **Azure HDInsight 工具**擴充功能。
5. 按一下 [重新載入視窗] 進行確認。 您會在 [擴充功能] 窗格中看到 **Azure HDInsight 工具**。

   ![HDInsight for Visual Studio Code Python 安裝](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

建議 Spark 使用者使用 Python 擴充功能以獲得更好的語言服務體驗。 Hive 使用者則不必使用 Python 擴充功能。 

**安裝 Python**

1. 開啟 **Visual Studio Code**。
2. 按一下左窗格的 [擴充功能]。 在搜尋方塊中輸入 **python**。 您會看到 python 擴充功能的清單。 其中一個擴充功能是 **Python**。
3. 按一下 **Python** 旁邊的 [安裝]。 幾秒鐘後，[安裝] 按鈕就會變為 [重新載入]。
4. 按一下 [重新載入] 來啟動 **Python** 擴充功能。
5. 按一下 [重新載入視窗] 進行確認。 您會在 [擴充功能] 窗格中看到 **Python**。

     ![HDInsight for Visual Studio Code Python 安裝](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

## <a name="open-hdinsight-workspace"></a>開啟 HDInsight 工作區

您必須先在 VSCode 中建立工作區，然後才能連線到 Azure。

**開啟工作區**

1. 從 [檔案] 功能表按一下 [開啟資料夾]，然後指定現有資料夾或建立新資料夾來作為工作資料夾。 該資料夾會出現在左窗格中。
2. 從左窗格中，按一下工作資料夾旁邊的 [新增檔案] 圖示。

   ![新增檔案](./media/hdinsight-for-vscode/new-file.png)
3. 將新的檔案命名為 .hql (Hive 查詢) 或 .py (Spark 指令碼) 副檔名。 請注意工作資料夾中會自動新增 **XXXX_hdi_settings.json** 組態檔。
4. 從**檔案總管**開啟 **XXXX_hdi_settings.json**，或以滑鼠右鍵按一下指令碼編輯器以選取 [設定組態]。 您可以設定登入項目、預設叢集和作業提交參數，如檔案中的範例所示。 您也可以讓其餘參數保持空白。

## <a name="connect-to-azure"></a>連接到 Azure

您必須先連線到 Azure 帳戶，才可以從 VSCode 將指令碼提交到 HDInsight 叢集。

**連接到 Azure**

1. 建立新的工作資料夾和新的指令碼檔案 (如果您還沒有這兩個項目)。
2. 以滑鼠右鍵按一下指令碼編輯器，然後從快顯功能表選取 [HDInsight: 登入]。 您也可以按 **CTRL+SHIFT+P** 並輸入 **HDInsight: 登入**。

    ![「適用於 Visual Studio Code 的 HDInsight 工具」登入](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. 按一下 [Azure] 或 [AzureChina] (視訂用帳戶而定)。

    ![「適用於 Visual Studio Code 的 HDInsight 工具」登入選項](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login-options.png)

3. 遵循 [輸出] 窗格中的登入指示來登入。

    **Azure：**![「適用於 Visual Studio Code 的 HDInsight 工具」登入資訊](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png) **AzureChina：**![「適用於 Visual Studio Code 的 HDInsight 工具」(中國) 登入資訊](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-AzureChinalogin-info.png)

    連線之後，您的 Azure 帳戶名稱會顯示在 VSCode 視窗左下角的狀態列上。 

    > [!NOTE] 
    > 如果 Azure 帳戶已啟用雙因素驗證，建議您使用電話驗證而不是 Pin 碼驗證。
    > 使用 Google Chrome 來登入 Azure 已知會發生問題。

4. 以滑鼠右鍵按一下指令碼編輯器以開啟快顯功能表：

    ![「適用於 Visual Studio Code 的 HDInsight 工具」指令碼編輯器快顯功能表](./media/hdinsight-for-vscode/hdinsight-for-vscode-context-menu.png)

    您可以從快顯功能表執行下列工作：

    - logout
    - 列出叢集
    - 設定預設叢集
    - 提交互動式 Hive 查詢
    - 提交 Hive 批次指令碼
    - 設定組態

## <a name="list-hdinsight-clusters"></a>列出 HDInsight 叢集

若要測試連線，您可以列出 HDInsight 叢集：

**列出 Azure 訂用帳戶底下的 HDInsight 叢集**
1. 開啟工作區，然後連線到 Azure。 請參閱[開啟 HDInsight 工作區](#open-hdinsight-workspace)和[連線到 Azure](#connect-to-azure)。
2. 以滑鼠右鍵按一下指令碼編輯器，然後從快顯功能表選取 [HDInsight: 列出叢集]。 
3. [輸出] 窗格中會出現 Hive 和 Spark 叢集。

    ![設定預設叢集組態](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>設定預設叢集
1. 開啟工作區，然後連線到 Azure。 請參閱[開啟 HDInsight 工作區](#open-hdinsight-workspace)和[連線到 Azure](#connect-to-azure)。
2. 以滑鼠右鍵按一下指令碼編輯器，然後按一下 [HDInsight: 設定預設叢集]。 
3. 選取某個叢集來作為目前指令碼檔案的預設叢集。 工具會自動更新組態檔 **XXXX_hdi_settings.json**。 

   ![設定預設叢集組態](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>設定 Azure 環境 
1. 按下 **CTRL+SHIFT+P** 開啟命令選擇區。
2. 輸入 **HDInsight: 設定 Azure 環境**。
3. 在 Azure 和 AzureChina 中擇一作為預設登入項目。
4. 同時，我們的工具已將您選取的預設登入項目儲存到 **XXXX_hdi_settings.json**。 您也可以直接在這個組態檔內更新此登入項目。 

   ![設定預設的登入項目組態](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>提交互動式 Hive 查詢

適用於 VSCode 的 HDInsight 工具可讓您將互動式 Hive 查詢提交到 HDInsight 互動式查詢叢集。

1. 建立新的工作資料夾和新的 Hive 指令碼檔案 (如果您還沒有這兩個項目)。
2. 連線到 Azure 帳戶，然後設定預設叢集 (如果您還未設定)。
3. 複製以下程式碼並貼到 Hive 檔案中，然後儲存該檔案。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. 以滑鼠右鍵按一下指令碼編輯器，然後按一下 [HDInsight: 互動式 Hive] 以提交查詢。 此工具也可讓您使用快顯功能表來提交程式碼區塊，而非整個指令碼檔案。 不久之後，系統就會在新的索引標籤中顯示查詢結果：

   ![互動式 Hive 結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - [結果] 面板：您可以將整個結果以 CSV、JSON、EXCEL 的形式儲存到本機路徑，或只選取多行。
    - [訊息] 面板：按一下**行號**，它就會跳至執行中指令碼的第一行。

相較於[執行 Hive 批次工作](#submit-hive-batch-scripts)，互動式查詢所需的時間會少很多。

## <a name="submit-hive-batch-scripts"></a>提交 Hive 批次指令碼

1. 建立新的工作資料夾和新的 Hive 指令碼檔案 (如果您還沒有這兩個項目)。
2. 連線到 Azure 帳戶，然後設定預設叢集 (如果您還未設定)。
3. 複製以下程式碼並貼到 Hive 檔案中，然後儲存該檔案。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. 以滑鼠右鍵按一下指令碼編輯器，然後按一下 [HDInsight: 提交 Hive 批次指令碼] 以提交 Hive 作業。 
4. 選取要提交到哪個叢集。  

    在提交 Hive 作業之後，[輸出] 面板中就會顯示提交成功資訊和 jobid。 並且會開啟**網頁瀏覽器**，在其中顯示作業即時記錄和狀態。

   ![提交 Hive 作業結果](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

相較於[提交互動式 Hive 查詢](#submit-interactive-hive-queries)，批次作業所需時間會長很多。


## <a name="submit-pyspark-job"></a>提交 PySpark 作業

1. 建立新的工作資料夾和副檔名為 .py 的新指令碼檔案 (如果您還沒有這兩個項目)。
2. 連線到 Azure 帳戶，然後設定預設叢集 (如果您還未設定)。
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
4. 以滑鼠右鍵按一下指令碼編輯器，然後按一下 [HDInsight: 提交 PySpark 作業]。 
5. 選取一個叢集來提交 PySpark 作業。 

   ![提交 python 作業結果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

提交 python 作業之後，VSCode 的 [輸出] 視窗中會顯示提交記錄。 此外也會顯示 **Spark UI URL** 和 **Yarn UI URL**。 您可以在網頁瀏覽器中開啟 URL 來追蹤作業狀態。


## <a name="additional-features"></a>其他功能

HDInsight for VSCode 支援下列功能︰

- **IntelliSense 自動完成**。 會在關鍵字、方法、變數等等周圍彈出建議。不同圖示代表不同類型的物件︰

    ![「適用於 Visual Studio Code 的 HDInsight 工具」IntelliSense 物件類型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 錯誤標記**。 語言服務會對 Hive 指令碼的編輯錯誤加上底線。     
- **語法醒目顯示**。 語言服務會使用不同顏色來區別變數、關鍵字、資料類型、函式等等。 

    ![「適用於 Visual Studio Code 的 HDInsight 工具」語法醒目顯示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>後續步驟

### <a name="demo"></a>示範
* HDInsight for VScode：[影片](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [使用適用於 IntelliJ 的 Azure 工具組透過 VPN 對 Spark 應用程式進行遠端偵錯](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用適用於 IntelliJ 的 Azure 工具組透過 SSH 對 Spark 應用程式進行遠端偵錯](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具建立 Spark 應用程式](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Hive 資料視覺化](./hdinsight-connect-hive-power-bi.md)。
* [使用 Zeppelin 在 Azure HDInsight 中執行 Hive 查詢](./hdinsight-connect-hive-zeppelin.md)。

### <a name="scenarios"></a>案例
* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>建立和執行應用程式
* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)




