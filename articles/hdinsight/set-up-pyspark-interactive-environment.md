---
title: "Azure HDInsight 工具 - 設定 Visual Studio Code 的 PySpark 互動式環境 | Microsoft Docs"
description: "了解如何使用適用於 Visual Studio Code 的 Azure HDInsight 工具來建立、提交查詢和指令碼。"
Keywords: "VScode,Azure HDInsight 工具,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,互動式 Hive,互動式查詢"
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 839f379228322eb2da0ff61609634bf1f86e4bb3
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="set-up-pyspark-interactive-environment-for-visual-studio-code"></a>設定 Visual Studio Code 的 PySpark 互動式環境

下列步驟將說明如何在執行 **HDInsight: PySpark Interactive** \(HDInsight: PySpark 互動式\) 時安裝 Python 套件。


## <a name="set-up-pyspark-interactive-environment-on-macos-and-linux"></a>在 MacOS 和 Linux 上設定 PySpark 互動式環境
如果是 **python 3.x**，則針對下列步驟，您需要使用 **pip3** 命令。
1. 確定已安裝 **Python** 和 **pip**。
 
    ![Python pip 版本](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  安裝 Jupyter
    ```
    sudo pip install jupyter
    ```
    +  下列錯誤訊息可能會出現在 Linux 和 MacOS 上：

        ![錯誤 1](./media/set-up-pyspark-interactive-environment/error1.png)
        ```Resolve:
        sudo pip uninstall asyncio
        sudo pip install trollies
        ```

    + 安裝 libkrb5-dev (僅適用於 Linux)，可能會出現下列錯誤訊息：

        ![錯誤 2](./media/set-up-pyspark-interactive-environment/error2.png)
        ```Resolve:
        sudo apt-get install libkrb5-dev 
        ```

3. 安裝 sparkmagic
   ```
   sudo pip install sparkmagic
   ```

4. 執行下列程式碼來確定已正確安裝 ipywidgets：
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![安裝包裝函式核心](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. 安裝包裝函式核心。 執行 **pip show sparkmagic**，就會顯示 sarkmagic 的安裝路徑。 

    ![sparkmagic 位置](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. 瀏覽至該位置並執行：

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec install](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. 檢查安裝狀態： 

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec list](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    針對可用的核心：**python2** 和 **pysparkkernel** 會對應至 **python 2.x****python3** 和 **pyspark3kernel** 會對應至 **python 3.x**。 

8. 重新啟動 VScode，然後返回執行 **HDInsight: PySpark Interactive** \(HDInsight: PySpark 互動式\) 的指令碼編輯器。

## <a name="next-steps"></a>後續步驟

### <a name="demo"></a>示範
* HDInsight for VScode：[影片](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [使用適用於 Visual Studio Code 的 Azure HDInsight 工具](hdinsight-for-vscode.md)
* [使用適用於 IntelliJ 的 Azure 工具組中來建立和提交 Spark Scala 應用程式](hdinsight-apache-spark-intellij-tool-plugin.md)
* [使用適用於 IntelliJ 的 Azure 工具組透過 SSH 對 Spark 應用程式進行遠端偵錯](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [使用適用於 IntelliJ 的 Azure 工具組透過 VPN 對 Spark 應用程式進行遠端偵錯](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具建立 Spark 應用程式](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Hive 資料視覺化](./hdinsight-connect-hive-power-bi.md)。
* [使用 Zeppelin 在 Azure HDInsight 中執行 Hive 查詢](./hdinsight-connect-hive-zeppelin.md)。
