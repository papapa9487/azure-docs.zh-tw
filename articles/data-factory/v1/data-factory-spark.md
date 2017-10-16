---
title: "從 Azure Data Factory 叫用 Spark 程式 | Microsoft Docs"
description: "了解如何從 Azure Data Factory 使用 MapReduce 活動叫用 Spark 程式。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 3690b5f62d8384d255d420946f6ac1cfd47b9317
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>從 Azure Data Factory 叫用 Spark 程式管線

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 活動](data-factory-hive-activity.md)
> * [Pig 活動](data-factory-pig-activity.md)
> * [MapReduce 活動](data-factory-map-reduce.md)
> * [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
> * [Spark 活動](data-factory-spark.md)
> * [Machine Learning Batch 執行活動](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新資源活動](data-factory-azure-ml-update-resource-activity.md)
> * [預存程序活動](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活動](data-factory-usql-activity.md)
> * [.NET 自訂活動](data-factory-use-custom-activities.md)

## <a name="introduction"></a>簡介
Spark 活動是 Azure Data Factory 所支援的其中一個[資料轉換活動](data-factory-data-transformation-activities.md)。 此活動會在 Azure HDInsight 中 Apache Spark 叢集上執行指定的 Spark 程式。    

> [!IMPORTANT]
> - Spark 活動不支援 Azure Data Lake Store 作為主要儲存體的 HDInsight Spark 叢集。
> - Spark 活動僅支援現有 (您自己的) HDInsight Spark 叢集。 它不支援隨選 HDInsight 連結服務。

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>逐步解說：使用 Spark 活動建立管線
以下是使用 Spark 活動建立 Data Factory 管線的一般步驟。  

1. 建立資料處理站。
2. 建立 Azure 儲存體連結服務，將與 HDInsight Spark 叢集相關聯的 Azure 儲存體連結至 Data Factory。     
2. 建立 Azure HDInsight 連結服務，將 Azure HDInsight 中的 Apache Spark 叢集連結至 Data Factory。
3. 建立可參考 Azure 儲存體連結服務的資料集。 目前，您必須指定活動的輸出資料集，即使沒有產生任何輸出。  
4. 使用 Spark 活動建立管線，以參考 #2 中建立的 HDInsight 連結服務。 此活動已使用您在上一個步驟中建立的資料集設定為輸出資料集。 輸出資料集可以驅動排程 (每小時、每天等)。 因此，您必須指定輸出資料集，即使活動並未真的產生輸出。

### <a name="prerequisites"></a>必要條件
1. 依照逐步解說：[建立儲存體帳戶](../../storage/common/storage-create-storage-account.md#create-a-storage-account)中的指示，建立**一般用途的 Azure 儲存體帳戶**。  
2. 依照教學課程：[在 Azure HDInsight 中建立 Apache Spark 叢集](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)中的指示**在 Azure HDInsight 中建立 Apache Spark 叢集**。 將您在步驟 1 中建立的 Azure 儲存體帳戶與此叢集產生關聯。  
3. 下載並檢閱 python 指令碼檔案 **test.py**，該檔案位於：[https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py)。  
3.  將 **test.py** 上傳至您的Azure Blob 儲存體 **adfspark** 容器中的 **pyFiles** 資料夾。 建立容器和資料夾 (如果不存在)。

### <a name="create-data-factory"></a>建立資料處理站
讓我們在這個步驟中開始建立 Data Factory。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下左側功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。
3. 在 [新增 Data Factory] 刀鋒視窗中，輸入 **SparkDF** 作為 [名稱]。

   > [!IMPORTANT]
   > Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您看到此錯誤：**Data factory 名稱 "SparkDF" 無法使用**。 請變更 Data Factory 名稱 (例如 yournameSparkDFdate)，然後嘗試重新建立。 請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md) 主題，以了解 Data Factory 成品的命名規則。   
4. 選取您想要建立 Data Factory 的 [Azure 訂用帳戶]  。
5. 請選取現有的**資源群組**，或建立 Azure 資源群組。
6. 選取 [釘選到儀表板] 選項。  
6. 按一下 [新增 Data Factory] 刀鋒視窗上的 [建立]。

   > [!IMPORTANT]
   > 若要建立 Data Factory 執行個體，您必須是訂用帳戶/資源群組層級的 [Data Factory 參與者](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) 角色成員。
7. 您會看到 Data Factory 建立在 Azure 入口網站的「儀表板」中，如下所示：   
8. 在 Data Factory 成功建立後，您會看到 Data Factory 頁面，顯示 Data Factory 的內容。 如果看不到 Data Factory 頁面，請在儀表板上按一下您的 Data Factory 圖格。

    ![Data Factory 刀鋒視窗](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>建立連結服務
在此步驟中，您可以建立兩個連結服務，一個用來將您的 Spark 叢集連結至 Data Factory，以及另一個用來將您的 Azure 儲存體連結至 Data Factory。  

#### <a name="create-azure-storage-linked-service"></a>建立 Azure 儲存體連結服務
在此步驟中，您會將您的 Azure 儲存體帳戶連結到您的 Data Factory。 您在本逐步解說稍後的步驟中建立的資料集會參考此連結服務。 您在下一個步驟中定義的 HDInsight 連結服務也會參考此連結服務。  

1. 在您的 Data Factory 的 [Data Factory] 刀鋒視窗上按一下 [製作和部署]。 您應該會看到 Data Factory 編輯器。
2. 按一下 [新增資料存放區] 並選擇 [Azure 儲存體]。

   ![新增資料存放區 - Azure 儲存體 - 功能表](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. 在編輯器中，您應該會看到用來建立 Azure 儲存體連結服務的 **JSON 指令碼**。

   ![Azure 儲存體連結服務](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. 將 **accountname** 和 **accountkey** 以 Azure 儲存體帳戶的名稱及存取金鑰來取代。 若要了解如何取得您的儲存體存取金鑰，請參閱[管理儲存體帳戶](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)中說明如何檢視、複製和重新產生儲存體存取金鑰的資訊。
5. 若要部署連結服務，請按一下命令列的 [部署]。 成功部署連結的服務之後，應該會出現 **Draft-1** 視窗，而且您會在左側的樹狀檢視中看到 **AzureStorageLinkedService**。

#### <a name="create-hdinsight-linked-service"></a>建立 HDInsight 連結服務
在此步驟中，您會建立 Azure HDInsight 連結服務，將 HDInsight Spark 叢集連結至 Data Factory。 HDInsight 叢集是用來執行此範例管線的 Spark 活動中指定的 Spark 程式。  

1. 按一下**...其他** (工具列上)，按一下 新增計算，然後按一下HDInsight 叢集。

    ![建立 HDInsight 連結服務](media/data-factory-spark/new-hdinsight-linked-service.png)
2. 複製下列程式碼片段並貼到 [Draft-1]  視窗。 在 [JSON 編輯器] 中，執行下列步驟：
    1. 指定 HDInsight Spark 叢集的 **URI**。 例如： `https://<sparkclustername>.azurehdinsight.net/`。
    2. 指定具有 Spark 叢集存取權之**使用者**的名稱。
    3. 指定使用者的**密碼**。
    4. 指定與 HDInsight Spark 叢集相關聯的 **Azure 儲存體連結服務**。 在此範例中，它是：**AzureStorageLinkedService**。

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Spark 活動不支援 Azure Data Lake Store 作為主要儲存體的 HDInsight Spark 叢集。
    > - Spark 活動僅支援現有 (您自己的) HDInsight Spark 叢集。 它不支援隨選 HDInsight 連結服務。

    如需 HDInsight 連結服務的詳細資訊，請參閱 [HDInsight 連結服務](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)。
3.  若要部署連結服務，請按一下命令列的 [部署]。  

### <a name="create-output-dataset"></a>建立輸出資料集
輸出資料集可以驅動排程 (每小時、每天等)。 因此，您必須為管線中的 Spark 活動指定輸出資料集，即使活動並未真的產生任何輸出。 為活動指定輸入資料集是選擇性的。

1. 在 [Data Factory 編輯器] 中，按一下命令列上的 [...其他]，按一下 [新增資料集]，然後選取 [Azure Blob 儲存體]。  
2. 複製下列程式碼片段並貼到 [Draft-1] 視窗。 JSON 程式碼片段會定義名為 **OutputDataset** 的資料集。 此外，指定將結果儲存在名為 **adfspark** 的 Blob 容器及名為 **pyFiles/output** 的資料夾中。 如先前所述，此資料集是空的資料集。 此範例中的 Spark 程式不會產生任何輸出。 **availability** 區段指定每日產生一次輸出資料集。  

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
3. 若要部署資料集，請按一下命令列上的 [部署]。


### <a name="create-pipeline"></a>建立管線
在此步驟中，您會建立具有 **HDInsightSpark** 活動的管線。 目前，輸出資料集會影響排程，因此即使活動並未產生任何輸出，您都必須建立輸出資料集。 如果活動沒有任何輸入，您可以略過建立輸入資料集。 因此，在此範例中不會指定任何輸入資料集。

1. 在 **Data Factory 編輯器**中，按一下工具列的 ... 更多，然後按一下新增資料閘道。
2. 使用下列指令碼取代 Draft-1 視窗中的指令碼：

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    請注意下列幾點：
    - **type** 屬性會設為 **HDInsightSpark**。
    - **rootPath** 會設為 **adfspark\\pyFiles**，其中 adfspark 是 Azure Blob 容器，而 pyFiles 是該容器中的正常資料夾。 在此範例中，Azure Blob 儲存體是與 Spark 叢集相關聯的儲存體。 您可以將檔案上傳至不同的 Azure 儲存體。 如果您這麼做，請建立 Azure 儲存體連結服務，以將該儲存體帳戶連結至資料處理站。 然後，將連結服務的名稱指定為 **sparkJobLinkedService** 屬性的值。 如需此屬性和 Spark 活動所支援的其他屬性詳細資訊，請參閱 [Spark 活動屬性](#spark-activity-properties)。  
    - **entryFilePath** 會設為 **test.py**，這就是 python 檔案。
    - **getDebugInfo** 屬性會設為 **Always**，表示永遠產生記錄檔 (不論成功或失敗)。

        > [!IMPORTANT]
        > 我們建議您不要在生產環境中將這個屬性設定為 `Always`，除非您要針對問題進行疑難排解。
    - **outputs** 區段有一個輸出資料集。 您必須指定輸出資料集，即使 Spark 程式不會產生任何輸出。 輸出資料集可以驅動管線的排程 (每小時、每天等)。  

        如需 Spark 活動所支援之屬性的詳細資訊，請參閱 [Spark 活動屬性](#spark-activity-properties)一節。
3. 若要部署管線，按一下命令列上的 [部署]。

### <a name="monitor-pipeline"></a>監視管線
1. 按一下 **X** 以關閉 [Data Factory 編輯器] 刀鋒視窗，以及瀏覽回 [Data Factory] 首頁。 按一下 [監視及管理] 在另一個索引標籤中啟動監視應用程式。

    ![監視及管理圖格](media/data-factory-spark/monitor-and-manage-tile.png)
2. 將頂端的 [開始時間] 篩選變更為 **2/1/2017**，然後按一下 [套用]。
3. 因為管線的開始 (2017-02-01) 與結束時間 (2017-02-02) 之間只有一天，您應該只會看到一個活動時段。 確認資料配量為 [就緒] 狀態。

    ![監視管線](media/data-factory-spark/monitor-and-manage-app.png)    
4. 選取 [活動視窗] 以查看關於活動執行的詳細資料。 如果發生錯誤，您會在右窗格中看到它的詳細資訊。

### <a name="verify-the-results"></a>驗證結果

1. 瀏覽至 https://CLUSTERNAME.azurehdinsight.net/jupyter，啟動您的 HDInsight Spark 叢集的 **Jupyter Notebook**。 您可以為 HDInsight Spark 叢集啟動叢集儀表板，然後啟動 **Jupyter Notebook**。
2. 按一下 [新增] -> [PySpark] 來啟動新的 Notebook。

    ![Jupyter 新筆記本](media/data-factory-spark/jupyter-new-book.png)
3. 在第二個陳述式的結尾複製/貼上文字，並按下 **SHIFT + ENTER** 來執行下列命令。  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. 確認您看到來自 hvac 資料表的資料：  

    ![Jupyter 查詢結果](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
如需詳細指示，請參閱[執行 Spark SQL 查詢](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)區段。 

### <a name="troubleshooting"></a>疑難排解
因為您將 **getDebugInfo** 設定為 **Always**，您會在 Azure Blob 容器的 **pyFiles** 資料夾中看到一個 **log** 子資料夾。 log 資料夾中的記錄檔會提供其他詳細資料。 發生錯誤時，此記錄檔特別有用。 在生產環境中，您可能想要將它設定為**失敗**。

如需進一步的疑難排解，請執行下列步驟：


1. 瀏覽至 `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`。

    ![YARN UI 應用程式](media/data-factory-spark/yarnui-application.png)  
2. 按一下其中一個執行嘗試的 [記錄]。

    ![應用程式頁面](media/data-factory-spark/yarn-applications.png)
3. 您應該會在記錄頁面中看到其他的錯誤資訊。

    ![記錄錯誤](media/data-factory-spark/yarnui-application-error.png)

下列各節提供 Data Factory 實體的相關資訊，以在您的資料處理站中使用 Apache Spark 叢集和 Spark 活動。

## <a name="spark-activity-properties"></a>Spark 活動屬性
以下是使用 Spark 活動之管線的 JSON 定義範例：    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

下表說明 JSON 定義中使用的 JSON 屬性：

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 名稱 | 管線中的活動名稱。 | 是 |
| 說明 | 說明活動用途的文字。 | 否 |
| 類型 | 這個屬性必須設為 HDInsightSpark。 | 是 |
| 預設容器 | Spark 程式執行所在的 HDInsight 連結服務名稱。 | 是 |
| rootPath | Spark 檔案所在的 Azure Blob 容器和資料夾。 檔案名稱有區分大小寫。 | 是 |
| entryFilePath | Spark 程式碼/套件之根資料夾的相對路徑。 | 是 |
| className | 應用程式的 Java/Spark 主要類別 | 否 |
| arguments | Spark 程式的命令列引數清單。 | 否 |
| proxyUser | 模擬來執行 Spark 程式的使用者帳戶 | 否 |
| sparkConfig | 指定下列主題中所列的 Spark 組態屬性值：[Spark 組態 - 應用程式屬性](https://spark.apache.org/docs/latest/configuration.html#available-properties) (英文)。 | 否 |
| getDebugInfo | 指定何時將 Spark 記錄檔複製到 HDInsight 叢集所使用 (或) sparkJobLinkedService 所指定的 Azure 儲存體。 允許的值︰None、Always 或 Failure。 預設值：None。 | 否 |
| sparkJobLinkedService | 存放 Spark 作業檔案、相依性和記錄的 Azure 儲存體連結服務。  如果您未指定此屬性的值，則會使用與 HDInsight 叢集相關聯的儲存體。 | 否 |

## <a name="folder-structure"></a>資料夾結構
不同於 Pig 和 Hive 活動，Spark 活動不支援內嵌指令碼。 Spark 作業也比 Pig/Hive 作業更具擴充性。 對於 Spark 作業，您可以提供多個相依性，例如 jar 套件 (置於 java CLASSPATH)、python 檔案 (置於 PYTHONPATH) 和任何其他檔案。

在 HDInsight 連結服務所參考的 Azure Blob 儲存體中，建立下列資料夾結構。 然後，將相依檔案上傳至根資料夾中以 **entryFilePath** 表示的適當子資料夾。 比方說，將 python 檔案上傳至根資料夾的 pyFiles 子資料夾，將 jar 檔案上傳至 jars 子資料夾。 在執行階段，Data Factory 服務會預期 Azure Blob 儲存體中有下列資料夾結構︰     

| 路徑 | 說明 | 必要 | 類型 |
| ---- | ----------- | -------- | ---- |
| 。 | Spark 作業在儲存體連結服務中的根路徑  | 是 | 資料夾 |
| &lt;使用者定義&gt; | 指向 Spark 作業輸入檔案的路徑 | 是 | 檔案 |
| ./jars | 此資料夾下的所有檔案會上傳並放在叢集的 java 類別路徑 | 否 | 資料夾 |
| ./pyFiles | 此資料夾下的所有檔案會上傳並放在叢集的 PYTHONPATH | 否 | 資料夾 |
| ./files | 此資料夾下的所有檔案會上傳並放在執行程式工作目錄 | 否 | 資料夾 |
| ./archives | 此資料夾下的所有檔案未壓縮 | 否 | 資料夾 |
| ./logs | 此資料夾儲存來自 Spark 叢集的記錄。| 否 | 資料夾 |

以下是 HDInsight 連結服務所參考的 Azure Blob 儲存體中，含有兩個 Spark 作業檔案的儲存體範例。

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
