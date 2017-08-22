---
title: "在 HDInsight 上將 Hadoop 工作的資料上傳 | Microsoft Docs"
description: "了解如何使用 Azure CLI、Azure 儲存體總管、Azure PowerShell、Hadoop 命令列或 Sqoop 在 HDInsight 中上傳及存取 Hadoop 工作。"
keywords: "etl hadoop、將資料上傳到 hadoop、hadoop 載入資料"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 134afd3495c555f85e8838cbe0344a3a48534950
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>在 HDInsight 上將 Hadoop 工作的資料上傳
Azure HDInsight 在 Azure Blob 儲存體上提供了全功能的 Hadoop 分散式檔案系統 (HDFS)。 此儲存體是設計為 HDFS 的延伸，以便為使用者提供流暢的體驗。 此儲存體可讓 Hadoop 生態系統中的完整元件集直接在它管理的資料上運作。 Azure Blob 儲存體和 HDFS 是不同的檔案系統，但經過最佳化後，都非常適合儲存資料以及計算儲存的資料。 如需關於使用 Azure Blob 儲存體有哪些優點的資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

**必要條件**

開始進行之前，請注意下列需求：

* Azure HDInsight 叢集。 如需指示，請參閱 [Azure HDInsight 使用者入門][hdinsight-get-started]或[佈建 HDInsight 叢集][hdinsight-provision]。

## <a name="why-blob-storage"></a>為什麼要使用 Blob 儲存體？
部署 Azure HDInsight 叢集通常是為了執行 MapReduce 工作，並在這些工作完成後卸除叢集。 將計算完成後的資料保留在 HDFS 叢集是成本較高的資料儲存方式。 對於使用 HDInsight 來處理的資料，Azure Blob 儲存體是一種高可用性、高延展性、大容量、低成本且可共用的儲存方案。 將資料儲存在 Blob 中，可安全地釋放做為計算用途的 HDInsight 叢集，而且不會遺失資料。

### <a name="directories"></a>目錄
Azure Blob 儲存體容器會以機碼/值組來儲存資料，而且沒有目錄階層。 然而，機碼名稱中可使用 "/" 字元，使檔案變成好像儲存在目錄結構中一樣。 HDInsight 會將它們視為就像是實際的目錄一樣。

例如，Blob 的機碼可能是 *input/log1.txt*。 實際上不存在 "input" 目錄，只是因為機碼名稱中有 "/" 字元，才形成檔案路徑的外觀。

因此，當您使用 Azure Explorer 工具時，可能會注意到一些 0 位元組的檔案。 這些檔案有兩種用途：

* 如果是空資料夾，這些檔案會標示資料夾的存在。 Azure Blob 儲存體很聰明，它知道如果有一個名為 foo/bar 的 Blob ，就有一個名為 **foo**的資料夾。 但如果要表達有一個名為 **foo** 的空資料夾，唯一的辦法就是放入這個特殊的 0 位元組檔案。
* 這些檔案中保存 Hadoop 檔案系統所需的特殊中繼資料，尤其是資料夾的權限和擁有者。

## <a name="command-line-utilities"></a>命令列公用程式
Microsoft 提供下列公用程式來使用 Azure Blob 儲存體：

| 工具 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure 命令列介面][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] | | |✔ |
| [Hadoop 命令](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> 雖然 Azure CLI、Azure PowerShell 與 AzCopy 都可從外部 Azure 使用，但是 Hadoop 命令只能在 HDInsight 叢集上使用，而且只允許將資料從本機檔案系統載入到 Azure Blob 儲存體。
>
>

### <a id="xplatcli"></a>Azure CLI
Azure CLI 是可讓您管理 Azure 服務的跨平台工具。 使用以下步驟將資料上傳至 Azure Blob 儲存體：

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [安裝和設定適用於 Mac、Linux 和 Windows 的 Azure CLI](../cli-install-nodejs.md)。
2. 開啟命令提示字元、Bash 或其他殼層，然後使用以下命令驗證您的 Azure 訂用帳戶。

        azure login

    出現提示時，輸入訂用帳戶的使用者名稱和密碼。
3. 輸入以下命令可列出訂用帳戶的儲存體帳戶：

        azure storage account list
4. 選取含有您想要使用之 Blob 的儲存體帳戶，然後使用以下命令擷取此帳戶的金鑰：

        azure storage account keys list <storage-account-name>

    此時應該會傳回**主要**和**次要**金鑰。 複製 **主要** 金鑰值，因為接下來的步驟中會使用此值。
5. 使用以下命令擷取儲存體帳戶內的 Blob 儲存體清單：

        azure storage container list -a <storage-account-name> -k <primary-key>
6. 使用以下命令將檔案上傳及下載至 Blob：

   * 上傳檔案：

           azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
   * 下載檔案：

           azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [!NOTE]
> 如果您會持續使用同一個儲存體帳戶，可以設定以下環境變數，而不是為每個命令指定帳戶和金鑰：
>
> * **AZURE\_STORAGE\_ACCOUNT**：儲存體帳戶名稱
> * **AZURE\_STORAGE\_ACCESS\_KEY**：儲存體帳戶金鑰
>
>

### <a id="powershell"></a>Azure PowerShell
Azure PowerShell 是一種指令碼環境，可讓您在 Azure 中用來控制和自動化工作負載的部署及管理。 如需關於設定工作站以執行 Azure PowerShell 的資訊，請參閱 [安裝和設定 Azure PowerShell](/powershell/azure/overview)。

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**將本機檔案上傳至 Azure Blob 儲存體**

1. 依照 [安裝和設定 Azure PowerShell](/powershell/azure/overview)的指示，開啟 Azure PowerShell 主控台。
2. 在下列指令碼中設定前五個變數的值：

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
3. 將指令碼貼到 Azure PowerShell 主控台，並執行該指令碼來複製檔案。

如需建立來使用 HDInsight 的 PowerShell 指令碼範例，請參閱 [HDInsight 工具](https://github.com/blackmist/hdinsight-tools)。

### <a id="azcopy"></a>AzCopy
AzCopy 是一套命令列工具，此工具設計目的在於簡化將資料移入及移出 Azure 儲存體帳戶的傳輸工作。 您可以將它當做獨立工具來使用，也可以將此工具納入現有的應用程式中。 [下載 AzCopy][azure-azcopy-download]。

AzCopy 語法如下：

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

如需詳細資訊，請參閱 [AzCopy - 上傳/下載 Azure Blob 的檔案][azure-azcopy]。

### <a id="commandline"></a>Hadoop 命令列
Hadoop 命令列僅適用於當資料已存在於叢集前端節點時，將資料儲存到 Blob 儲存體。

若要使用 Hadoop 命令，您必須先使用下列其中一個方法連線到前端節點：

* **Windows 架構的 HDInsight**： [使用遠端桌面連線](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **Linux 架構的 HDInsight**：使用 SSH ([SSH 命令](hdinsight-hadoop-linux-use-ssh-unix.md) 或 [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)) 來連線

連線之後，您就可以使用下列語法來將檔案上傳到儲存體。

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

例如， `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

因為 HDInsight 的預設檔案系統是位於 Azure Blob 儲存體中，所以 /example/data.txt 實際上是在 Azure Blob 儲存體中。 您也可以用下列語法來參考此檔案：

    wasb:///example/data/data.txt

或

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

如需其他可用來處理檔案的 Hadoop 命令清單，請參閱 [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> 在 HBase 叢集上，寫入資料時所使用的預設區塊大小是 256 KB。 雖然在使用 HBase API 或 REST API 時此大小可正常運作，但使用 `hadoop` 或 `hdfs dfs` 命令寫入大於 ~12 GB 的資料會導致錯誤。 如需詳細資訊，請參閱下面的[在 Blob 上寫入時的儲存體例外狀況](#storageexception)一節。
>
>

## <a name="graphical-clients"></a>圖形化用戶端
其他還有數個應用程式也會提供可搭配 Azure 儲存體使用的圖形化介面。 以下提供數個這類應用程式的清單：

| 用戶端 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) |✔ |✔ |✔ |
| [Azure 儲存體總管](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools for HDInsight
如需詳細資訊，請參閱 [瀏覽連結的資源](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources)。

### <a id="storageexplorer"></a>Azure 儲存體總管
*Azure 儲存體總管* 是一種可在 Blob 中檢查和變更資料的實用工具。 它是免費開放原始碼工具，可從 [http://storageexplorer.com/](http://storageexplorer.com/)下載。 原始碼亦可從此連結取得。

使用此工具之前，必須先知道您的 Azure 儲存體帳戶名稱和帳戶金鑰。 如需關於取得此資訊的指示，請參閱[建立、管理或刪除儲存體帳戶][azure-create-storage-account]的＜如何：檢視、複製及重新產生儲存體存取金鑰＞一節。

1. 執行 Azure 儲存體總管。 如果這是您第一次執行 [儲存體總管]，系統將會提示您輸入 [儲存體帳戶名稱] 和 [儲存體帳戶金鑰]。 如果您之前曾執行過，請使用 [新增] 按鈕加入新的儲存體帳戶名稱和金鑰。

    輸入 HDInsight 叢集所使用儲存體帳戶的名稱和金鑰，然後選取 [儲存並開啟]。

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. 在此介面左邊的 [容器] 清單中，按一下與 HDInsight 叢集相關聯的容器名稱。 根據預設，這是 HDInsight 叢集的名稱，但如果您在建立叢集時輸入了特定名稱，則有可能不同。
3. 從工具列選取上傳圖示。

    ![工具列和反白顯示的上傳圖示](./media/hdinsight-upload-data/toolbar.png)
4. 指定要上傳的檔案，然後按一下 [開啟] 。 出現提示時，請選取 [上傳]  將檔案上傳至儲存體容器的根目錄。 如果您想要將檔案上傳到特定路徑，請在 [目的地] 欄位中輸入路徑，然後選取 [上傳]。

    ![檔案上傳對話方塊](./media/hdinsight-upload-data/fileupload.png)

    完成上傳檔案之後，您可以從 HDInsight 叢集上的作業加以使用。

## <a name="mount-azure-blob-storage-as-local-drive"></a>將 Azure Blob 儲存體掛接為本機磁碟機
請參閱 [將 Azure Blob 儲存體掛接為本機磁碟機](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)。

## <a name="services"></a>服務
### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory 服務是完全受管理的服務，可將資料儲存、資料處理及資料移動服務組合成有效率、可調整且可靠的資料生產管線。

Azure Data Factory 可用來將資料移至 Azure Blob 儲存體，或建立資料管線直接使用 HDInsight 功能，例如 Hive 和 Pig。

如需詳細資訊，請參閱 [Azure Data Factory 文件](https://azure.microsoft.com/documentation/services/data-factory/)。

### <a id="sqoop"></a>Apache Sqoop
Sqoop 是一種專門在 Hadoop 和關聯式資料庫之間傳送資料的工具。 此工具可讓您從 SQL Server、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS)，將資料匯入 Hadoop 分散式檔案系統 (HDFS)，使用 MapReduce 或 Hive 轉換 Hadoop 中的資料，然後將資料匯回 RDBMS。

如需詳細資訊，請參閱[搭配 HDInsight 使用 Sqoop][hdinsight-use-sqoop]。

## <a name="development-sdks"></a>開發 SDK
Azure Blob 儲存體也可以使用 Azure SDK，透過下列程式設計語言來存取：

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

如需安裝 Azure SDK 的詳細資訊，請參閱 [Azure 下載](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>疑難排解
### <a id="storageexception"></a>在 Blob 上寫入時的儲存體例外狀況
**徵兆**︰使用 `hadoop` 或 `hdfs dfs` 命令在 HBase 叢集上寫入 ~12 GB 或更大的檔案時，您可能會遇到下列錯誤︰

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**原因**：在寫入 Azure 儲存體時，HDInsight 叢集上的 HBase 會將區塊大小預設為 256 KB。 雖然這適用於 HBase API 或 REST API，但會導致在使用 `hadoop` 或 `hdfs dfs` 命令列公用程式時發生錯誤。

**解決方案**︰使用 `fs.azure.write.request.size` 指定較大的區塊大小。 您可以使用 `-D` 參數針對每一次使用進行指定。 以下是搭配使用此參數與 `hadoop` 命令的範例︰

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

您也可以使用 Ambari，全域增加 `fs.azure.write.request.size` 的值。 使用下列步驟即可變更 Ambari Web UI 中的值︰

1. 在瀏覽器中，移至叢集的 Ambari Web UI。 這是 https://CLUSTERNAME.azurehdinsight.net，其中 **CLUSTERNAME** 是叢集的名稱。

    出現提示時，請輸入該叢集的管理員名稱和密碼。
2. 在畫面左側選取 [HDFS]，然後選取 [設定] 索引標籤。
3. 在 [篩選...] 欄位中，輸入 `fs.azure.write.request.size`。 這會在頁面中間顯示欄位和目前的值。
4. 將值從 262144 (256 KB) 變更為新值。 例如，4194304 (4 MB)。

![透過 Ambari Web UI 變更值的影像](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

如需使用 Ambari 的詳細資訊，請參閱[使用 Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

## <a name="next-steps"></a>後續步驟
您現在已了解如何將資料匯入 HDInsight，請接著閱讀下列文章以了解如何執行分析：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]
* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

