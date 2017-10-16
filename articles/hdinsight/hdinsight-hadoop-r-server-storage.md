---
title: "適用於 HDInsight 上 R 伺服器的 Azure 儲存體選項 - Azure | Microsoft Docs"
description: "了解 HDInsight 上 R 伺服器之使用者可用的不同儲存體選項"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: aef9c15636ccaecce07d4fa218a40ed26ebad9df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-solutions-for-r-server-on-hdinsight"></a>適用於 HDInsight 上 R 伺服器的 Azure 儲存體選項

HDInsight 上的 Microsoft R 伺服器有數種儲存體選項可用來保存資料、程式碼或包含分析結果的物件。 這些包括下列選項：

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake 儲存體](https://azure.microsoft.com/services/data-lake-store/)
- [Azure 檔案儲存體](https://azure.microsoft.com/services/storage/files/)

您可以使用 HDI 叢集存取多個 Azure 儲存體帳戶或容器。 「Azure 檔案」儲存體是一個可在邊緣節點上使用的方便資料儲存體選項，可讓您掛接「Azure 儲存體」檔案共用 (例如掛接到 Linux 檔案系統)。 但是，只要是擁有受支援作業系統 (例如 Windows 或 Linux) 的系統，就可以掛接和使用「Azure 檔案」共用。 

當您在 HDInsight 中建立 Hadoop 叢集時，可以指定 **Azure 儲存體**帳戶或 **Data Lake Store**。 來自該帳戶的特定儲存體容器，會保留您所建立叢集的檔案系統 (例如，Hadoop 分散式檔案系統)。 如需詳細資訊與指導方針，請參閱：

- [搭配 HDInsight 使用 Azure 儲存體](hdinsight-hadoop-use-blob-storage.md)
- [搭配 Azure HDInsight 叢集使用 Data Lake Store](hdinsight-hadoop-use-data-lake-store.md)。 

如需有關 Azure 儲存體解決方案的詳細資訊，請參閱 [Microsoft Azure 儲存體簡介](../storage/common/storage-introduction.md)。 

如需有關如何針對您的情節選取最適儲存體選項的指導方針，請參閱[決定何時使用 Azure Blob、Azure 檔案或 Azure 資料磁碟](../storage/common/storage-decide-blobs-files-disks.md) 


## <a name="use-azure-blob-storage-accounts-with-r-server"></a>搭配 R 伺服器使用 Azure Blob 儲存體帳戶

如有需要，您可以使用 HDI 叢集存取多個 Azure 儲存體帳戶或容器。 若要這樣做，您必須在建立叢集時於 UI 中指定其他儲存體帳戶，然後遵循下列步驟，以便搭配 R 伺服器使用它們。

> [!WARNING]
> 基於效能目的，系統會在與您指定的主要儲存體帳戶相同資料中心內建立 HDInsight 叢集。 不支援在與 HDInsight 叢集不同的位置中使用儲存體帳戶。

1. 使用儲存體帳戶名稱 **storage1** 和預設容器 **container1** 建立 HDInsight 叢集。
2. 指定其他儲存體帳戶 **storage2**。  
3. 將 mycsv.csv 檔案複製到 /share 目錄，並對該檔案執行分析。  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. 在 R 程式碼中，將名稱節點設定為 **default** ，並設定要處理的目錄和檔案。  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

所有目錄和檔案的參考會指向儲存體帳戶 wasb://container1@storage1.blob.core.windows.net。 這是與 HDInsight 叢集關聯的「預設儲存體帳戶」。

現在，假設您想要處理名稱為 mySpecial.csv 的檔案，其所在位置為 **storage2** 中 **container2** 的 /private 目錄。

在 R 程式碼中，將名稱節點參考指向 **storage2** 儲存體帳戶。


    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

現在，所有目錄和檔案的參考會指向儲存體帳戶 wasb://container2@storage2.blob.core.windows.net。 這是您已指定的「名稱節點」。

您必須在 **storage2** 上設定 /user/RevoShare/<SSH username> 目錄，如下所示：


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>



## <a name="use-an-azure-data-lake-store-with-r-server"></a>搭配 R 伺服器使用 Azure Data Lake Store

若要搭配 HDInsight 帳戶使用 Data Lake 存放區，您必須對叢集提供您想要使用之每個 Azure Data Lake 存放區的存取權。 如需有關如何使用 Azure 入口網站並使用 Azure Data Lake Store 帳戶作為預設儲存體或作為其他存放區來建立 HDInsight 叢集的指示，請參閱 [使用 Azure 入口網站搭配 Data Lake Store 建立 HDInsight 叢集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。

接著，您會以和使用次要 Azure 儲存體帳戶的方式 (如先前程序所述) 很像的方式，在 R 指令碼中使用存放區。

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>為叢集新增 Azure Data Lake Store 的存取權
您可以使用與 HDInsight 叢集相關聯的 Azure Active Directory (Azure AD) 服務主體來存取 Data Lake Store。

新增 Azure AD 服務主體：

1. 在建立 HDInsight 叢集時，從 [資料來源] 索引標籤中選取 [叢集 AAD 身分識別]。

2. 在 [叢集 AAD 身分識別] 對話方塊的 [選取 AD 服務主體] 底下，選取 [新建]。

在為服務主體命名並建立密碼後，請按一下 [管理 ADLS 存取]，以將該服務主體與您的 Data Lake Store 產生關聯。

建立叢集之後，您也可以新增叢集存取權到一或多個 Data Lake Store。 為 Data Lake Store 開啟 Azure 入口網站入口，並移至 [資料總管] > [存取] > [新增]。 

### <a name="how-to-access-the-data-lake-store-from-r-server"></a>如何從 R 伺服器存取 Data Lake Store

一旦您獲得 Data Lake 存放區的存取權，就可以在 HDInsight 上的 R 伺服器中使用該存放區，其方式就和使用次要 Azure 儲存體帳戶一樣。 唯一的差別在於前置詞 **wasb://** 會變更為 **adl://**，如下所示：


    # Point to the ADL store (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

    # Create factors for days of the week
    colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

    # Define the data source
    airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

    # Run a linear regression
    model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)


下列命令是用來搭配 RevoShare 目錄設定 Data Lake 儲存體帳戶，並新增來自先前範例的範例 .csv 檔案：


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server"></a>搭配 R 伺服器使用 Azure 檔案儲存體

另外還有可在邊緣節點上使用的便利資料儲存體選項，我們稱之為 [Azure 檔案]((https://azure.microsoft.com/services/storage/files/)。 它可以讓您將 Azure 儲存體的檔案共用掛接至 Linux 檔案系統。 此選項對於儲存資料檔案、R 指令碼與結果物件相當便利，該結果物件在稍後可以於邊緣節點 (而不是 HDFS) 上使用原生檔案系統時需要。 

Azure 檔案的主要優點是，只要是擁有受支援作業系統 (例如 Windows 或 Linux) 的系統，就可以掛接和使用檔案共用。 例如，您本人或您的小組成員所擁有的另一個 HDInsight 叢集，或是 Azure VM 甚或內部部署系統均可使用 Azure 檔案。 如需詳細資訊，請參閱：

- [如何搭配 Linux 使用 Azure 檔案儲存體](../storage/files/storage-how-to-use-files-linux.md)
- [如何在 Windows 上使用 Azure 檔案儲存體](../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>後續步驟

既然已經了解 Azure 儲存體選項，就可以使用下列連結來探索使用 HDInsight 上之 R 伺服器完成資料科學工作的方式。

* [HDInsight 上的 R 伺服器概觀](hdinsight-hadoop-r-server-overview.md)
* [開始使用 Hadoop 上的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)
* [將 RStudio Server 新增至 HDInsight (若未在建立叢集期間新增)](hdinsight-hadoop-r-server-install-r-studio.md)
* [適用於 HDInsight 上 R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)

