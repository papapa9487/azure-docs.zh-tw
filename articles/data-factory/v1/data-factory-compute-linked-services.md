---
title: "Azure Data Factory 支援的計算環境 | Microsoft Docs"
description: "了解您可以在 Azure Data Factory 管線中 (如 Azure HDInsight) 用來轉換/處理資料的計算環境。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 1547b5c3a5c629b85ff5fa9de6b39b25531d9ec9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory 支援的計算環境
> [!NOTE]
> 本文適用於正式推出 (GA) 的第 1 版 Data Factory。 如果您使用處於預覽狀態的 Data Factory 第 2 版，請參閱[第 2 版中的計算連接服務](../compute-linked-services.md)。

本文說明您可用來處理或轉換資料的各種計算環境。 其中還提供在設定將這些計算環境連結至 Azure Data Factory 的連結服務時，Data Factory 所支援的不同組態 (隨選與自備) 的詳細資料。

下表列出 Data Factory 支援的計算環境以及可在環境上執行的活動。 

| 計算環境                      | 活動                               |
| ---------------------------------------- | ---------------------------------------- |
| [隨選 HDInsight 叢集](#azure-hdinsight-on-demand-linked-service)或[您自己的 HDInsight 叢集](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md)、[Hive](data-factory-hive-activity.md)、[Pig](data-factory-pig-activity.md)、[MapReduce](data-factory-map-reduce.md)、[Hadoop 串流](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning 活動︰批次執行和更新資源](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service)、[Azure SQL 資料倉儲](#azure-sql-data-warehouse-linked-service)、[SQL Server](#sql-server-linked-service) | [預存程序](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Azure Data Factory 中支援的 HDInsight 版本
Azure HDInsight 支援多個可隨時部署的 Hadoop 叢集版本。 每一個版本選擇都會建立特定版本的 Hortonworks Data Platform (HDP) 散發，以及該散發內包含的一組元件。 Microsoft 會持續更新所支援 HDInsight 版本的清單，以提供最新的 Hadoop 生態系統元件和修正程式。 如需詳細資訊，請參閱[支援的 HDInsight 版本](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)。

> [!IMPORTANT]
> 以 Linux 為基礎的 HDInsight 3.3 版已於 2017 年 7 月 31 日被淘汰。 Data Factory v1 隨選 HDInsight 連結服務的客戶，必須於 2017 年 12 月 15 日之前完成較新版本 HDInsight 的測試及升級。 以 Windows 為基礎的 HDInsight 將於 2018 年 7 月 31 日被淘汰。
>
> 

**淘汰日期之後會發生什麼事** 

在 2017 年 12 月 15 日之後：

- 您將無法使用 Azure Data Factory v1 中的隨選 HDInsight 連結服務建立以 Linux 為基礎的 HDInsight 3.3 版 (或較舊版本) 叢集。 

- 如果沒有在現有 Azure Data Factory v1 隨選 HDInsight 連結服務 JSON 定義中明確指定 [osType 和/或 Version 屬性](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service)，預設值將會從 **Version=3.1, osType=Windows** 變更為 **Version=3.6, osType=Linux**。

在 2018 年 7 月 31 日之後：

- 您將無法使用 Azure Data Factory v1 中的隨選 HDInsight 連結服務建立任何版本之以 Windows 為基礎的 HDInsight 叢集。 

 **建議的動作** 

- 將受影響的 Azure Data Factory v1 隨選 HDInsight 連結服務定義的 [osType 和/或 Version 屬性](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service)更新為較新之以 Linux 為基礎的 HDInsight 版本 (HDInsight 3.6)，以確保您可以使用最新的 Hadoop 生態系統元件和修正。 
- 在 2017 年 12 月 15 日之前，請測試會參考受影響連結服務的 Azure Data Factory V1 Hive、Pig、MapReduce 及 Hadoop 資料流活動，以確保它們相容於新的 *osType* 和/或 *Version* 預設值 (Version=3.6, osType=Linux)，或是您正在升級至的明確 HDInsight version 和 osType。 若要深入了解相容性，請檢閱[從以 Windows 為基礎的 HDInsight 叢集移轉至以 Linux 為基礎的叢集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-migrate-from-windows-to-linux)，以及[可以搭配 HDInsight 使用的 Hadoop 元件和版本有哪些？](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions)文件網頁。 
- 如果您想要繼續使用 Azure Data Factory v1 隨選 HDInsight 連結服務來建立以 Windows 為基礎的 HDInsight 叢集，請於 2017 年 12 月 15 日之前將 osType 明確設定為 Windows。 不過，我們仍然建議在 2018 年 7 月 31 日之前移轉至以 Linux 為基礎的 HDInsight 叢集。 
- 如果您是使用隨選 HDInsight 連結服務來執行 Azure Data Factory v1 DotNet 自訂活動，請將 DotNet 自訂活動 JSON 定義更新成改為使用 Azure Batch 連結服務。 若要深入了解，請參閱[在 Azure Data Factory 管線中使用自訂活動](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-use-custom-activities)文件網頁。 

>[!Note]
>針對在 Azure Data Factory v1 中使用現有自攜叢集 (BYOC) 的客戶，或是在 Azure Data Factory v2 中使用 BYOC 和隨選 HDInsight 連結服務的客戶，由於系統已強制執行 Azure HDInsight 叢集最新版本的支援原則，因此不需要執行任何動作。 
>
> 


## <a name="on-demand-compute-environment"></a>隨選計算環境
在這種組態中，運算環境完全是由 Azure Data Factory 服務管理。 Data Factory 服務會在工作提交前自動建立運算環境以處理資料，而在工作完成時予以移除。 您可以建立隨選計算環境的連結服務、加以設定，以及控制工作執行、叢集管理和啟動動作的細微設定。

> [!NOTE]
> 目前僅支援 Azure HDInsight 叢集的隨選組態。
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight 隨選連結服務
Azure Data Factory 服務可自動建立以 Windows/Linux 為基礎的隨選 HDInsight 叢集來處理資料。 此叢集會建立在與叢集相關聯的儲存體帳戶 (JSON 中的 linkedServiceName 屬性) 相同的區域中。

請注意下列有關隨選 HDInsight 連結服務的 **重點** ：

* 您不會看到 Azure 訂用帳戶中建立的隨選 HDInsight 叢集。 Azure Data Factory 服務會代您管理隨選 HDInsight 叢集。
* 在隨選 HDInsight 叢集上執行之工作的記錄檔會被複製到與 HDInsight 叢集相關聯的儲存體帳戶。 您可以從 Azure 入口網站的 [活動執行詳細資料]  刀鋒視窗存取這些記錄檔。 如需詳細資訊，請參閱 [監視及管理管線](data-factory-monitor-manage-pipelines.md) 一文。
* 只會針對 HDInsight 叢集啟動並執行工作的時間來向您收取費用。

> [!IMPORTANT]
> 通常會花費 **20 分鐘**或更久的時間來佈建隨選 Azure HDInsight 叢集。
>
> 

### <a name="example"></a>範例
下列 JSON 會定義以 Linux 為基礎的隨選 HDInsight 連結服務。 Data Factory 服務會在處理資料配量時自動建立 **以 Linux 為基礎的** HDInsight 叢集。 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

若要使用以 Windows 為基礎的 HDInsight 叢集，請將 **osType** 設為 **windows**，或者不要使用此屬性，因為預設值是︰windows。  

> [!IMPORTANT]
> HDInsight 叢集會在您於 JSON 中指定的 Blob 儲存體 (**linkedServiceName**) 建立**預設容器**。 HDInsight 不會在刪除叢集時刪除此容器。 這是設計的行為。 在使用 HDInsight 隨選連結服務時，除非有現有的即時叢集 (**timeToLive**)，否則每當需要處理配量時，就會建立 HDInsight 叢集，並在處理完成時予以刪除。 
>
> 隨著處理的配量越來越多，您會在 Azure Blob 儲存體中看到許多容器。 如果在疑難排解作業時不需要這些容器，建議您加以刪除以降低儲存成本。 這些容器的名稱會遵循模式︰`adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`。 請使用 [Microsoft 儲存體總管](http://storageexplorer.com/) 之類的工具刪除 Azure Blob 儲存體中的容器。
>
> 

### <a name="properties"></a>屬性
| 屬性                     | 說明                              | 必要 |
| ---------------------------- | ---------------------------------------- | -------- |
| 類型                         | type 屬性應設為 **HDInsightOnDemand**。 | 是      |
| clusterSize                  | 叢集中的背景工作/資料節點數。 HDInsight 叢集會利用您為此屬性指定的 2 個前端節點以及背景工作節點數目來建立。 節點大小為具有 4 個核心的 Standard_D3，因此 4 個背景工作節點的叢集需要 24 個核心 (4\*4 = 16 個核心用於背景工作節點，加上 2\*4 = 8 個核心用於前端節點)。 如需 Standard_D3 層的詳細資料，請參閱[在 HDInsight 中建立 Linux 型 Hadoop 叢集](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。 | 是      |
| timetolive                   | 隨選 HDInsight 叢集允許的閒置時間。 指定在活動執行完成後，如果叢集中沒有其他作用中的作業，隨選 HDInsight 叢集要保持運作多久。<br/><br/>例如，如果活動執行花費 6 分鐘，而 timetolive 設為 5 分鐘，叢集會在處理活動執行的 6 分鐘期間之後保持運作 5 分鐘。 如果 6 分鐘期間內執行另一個活動，則會由相同叢集來處理。<br/><br/>建立隨選 HDInsight 叢集是昂貴的作業 (可能需要一段時間)，因此請視需要使用這項設定，重複使用隨選 HDInsight 叢集以改善 Data Factory 的效能。<br/><br/>如果您將 timetolive 值設為 0，叢集會在活動執行完成後立即刪除。 然而，如果您設定較高的值，叢集可能會有不必要的閒置而導致高成本。 因此，請務必根據您的需求設定適當的值。<br/><br/>如果適當地設定 timetolive 屬性值，則多個管線可以共用隨選 HDInsight 叢集的執行個體。 | 是      |
| 版本                      | HDInsight 叢集的版本。 針對 Windows 叢集的預設值為 3.1，針對 Linux 叢集的預設值為 3.2。 | 否       |
| 預設容器            | 隨選叢集用於儲存及處理資料的 Azure 儲存體連結服務。 建立 HDInsight 叢集的區域和這個 Azure 儲存體帳戶的區域相同。<p>目前，您無法建立使用 Azure Data Lake Store 做為儲存體的隨選 HDInsight 叢集。 如果您想要在 Azure Data Lake Store 中儲存 HDInsight 處理的結果資料，可使用複製活動將 Azure Blob 儲存體的資料複製到 Azure Data Lake Store。 </p> | 是      |
| additionalLinkedServiceNames | 指定 HDInsight 連結服務的其他儲存體帳戶，讓 Data Factory 服務代表您註冊它們。 這些儲存體帳戶與 HDInsight 叢集必須在相同區域，而建立此叢集的區域與 linkedServiceName 所指定之儲存體帳戶的區域相同。 | 否       |
| osType                       | 作業系統的類型。 允許的值為：Windows (預設值) 和 linux | 否       |
| hcatalogLinkedServiceName    | 指向 HCatalog 資料庫的 Azure SQL 連結服務名稱。 會使用 Azure SQL 資料庫作為中繼存放區，建立隨選 HDInsight 叢集。 | 否       |

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON 範例

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>進階屬性
您也可以針對隨選 HDInsight 叢集的細微組態指定下列屬性。

| 屬性               | 說明                              | 必要 |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | 指定要建立之 HDInsight 叢集的核心組態參數 (如 core-site.xml 所示)。 | 否       |
| hBaseConfiguration     | 指定 HDInsight 叢集的 HBase 組態參數 (hbase-site.xml)。 | 否       |
| hdfsConfiguration      | 指定 HDInsight 叢集的 HDFS 組態參數 (hdfs-site.xml)。 | 否       |
| hiveConfiguration      | 指定 HDInsight 叢集的 hive 組態參數 (hive-site.xml)。 | 否       |
| mapReduceConfiguration | 指定 HDInsight 叢集的 MapReduce 組態參數 (mapred-site.xml)。 | 否       |
| oozieConfiguration     | 指定 HDInsight 叢集的 Oozie 組態參數 (oozie-site.xml)。 | 否       |
| stormConfiguration     | 指定 HDInsight 叢集的 Storm 組態參數 (storm-site.xml)。 | 否       |
| yarnConfiguration      | 指定 HDInsight 叢集的 Yarn 組態參數 (yarn-site.xml)。 | 否       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>範例 – 包含進階屬性的隨選 HDInsight 叢集組態

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>節點大小
您可使用下列屬性指定前端、資料和的 zookeeper 節點的大小： 

| 屬性          | 說明                              | 必要 |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | 指定前端節點的大小。 預設值為：Standard_D3。 如需詳細資料，請參閱**指定節點大小**一節。 | 否       |
| dataNodeSize      | 指定資料節點的大小。 預設值為：Standard_D3。 | 否       |
| zookeeperNodeSize | 指定 Zoo Keeper 節點的大小。 預設值為：Standard_D3。 | 否       |

#### <a name="specifying-node-sizes"></a>指定節點大小
有關您在上一節所述的屬性中需要指定的字串值，請參閱[虛擬機器的大小](../../virtual-machines/linux/sizes.md)一文。 值必須符合本文件中所參考的 **CMDLET 與 APIS**。 如文中所述，「大型」(預設值) 資料節點的記憶體大小為 7-GB，但這可能不適用於您的案例。 

若想要建立 D4 大小的前端節點與背景工作節點，請指定 **Standard_D4** 作為 headNodeSize 與 dataNodeSize 屬性的值。 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

若您為這些屬性指定錯誤的值，可能會顯示下列 **錯誤：** 無法建立叢集。 例外狀況：無法完成叢集建立作業。 作業失敗，錯誤碼為 '400'。 叢集剩餘狀態：「錯誤」。 訊息：「PreClusterCreationValidationFailure」。 出現此錯誤時，請確定您是使用[虛擬機器的大小](../../virtual-machines/linux/sizes.md)一文的表格中的 **CMDLET 與 API** 名稱。  

> [!NOTE]
> 目前 Azure Data Factory 不支援使用 Azure Data Lake Store 作為主要存放區的 HDInsight 叢集。 使用 Azure 儲存體作為 HDInsight 叢集的主要存放區。 
>
> 


## <a name="bring-your-own-compute-environment"></a>自備計算環境
在這種組態中，使用者可以將現有的運算環境註冊為 Data Factory 中的連結服務。 此運算環境是由使用者管理並由 Data Factory 服務用來執行活動。

下列計算環境可支援這類型的組態：

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB、Azure SQL DW、SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 連結服務
您可以建立 Azure HDInsight 連結服務，以向 Data Factory 註冊自己的 HDInsight 叢集。

### <a name="example"></a>範例

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>屬性
| 屬性          | 說明                              | 必要 |
| ----------------- | ---------------------------------------- | -------- |
| 類型              | type 屬性應設為 **HDInsight**。 | 是      |
| clusterUri        | HDInsight 叢集的 URI。        | 是      |
| username          | 指定要用來連接到現有 HDInsight 叢集的使用者名稱。 | 是      |
| password          | 指定使用者帳戶的密碼。   | 是      |
| linkedServiceName | 參照 HDInsight 叢集所使用 Azure Blob 儲存體的 Azure 儲存體連結服務名稱。 <p>目前，您無法針對此屬性指定 Azure Data Lake Store 連結服務。 如果 HDInsight 叢集可存取 Data Lake Store，您可以透過 Hive/Pig 指令碼存取 Azure Data Lake Store 中的資料。 </p> | 是      |

## <a name="azure-batch-linked-service"></a>Azure Batch 連結服務
您可以建立 Azure Batch 連結服務，以向 Data Factory 註冊虛擬機器 (VM) 的 Batch 集區。 您可以使用 Azure Batch 或 Azure HDInsight 執行 .NET 自訂活動。

如果您不熟悉 Azure Batch 服務，請參閱下列主題：

* [Azure Batch 基本知識](../../batch/batch-technical-overview.md) ，以取得 Azure Batch 服務的概觀。
* [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) Cmdlet 可建立 Azure Batch 帳戶 (或) [Azure 入口網站](../../batch/batch-account-create-portal.md)，以使用 Azure 入口網站建立 Azure Batch 帳戶。 如需使用此 Cmdlet 的詳細指示，請參閱 [使用 PowerShell 管理 Azure Batch 帳戶](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) 主題。
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) Cmdlet 可建立 Azure Batch 集區。

### <a name="example"></a>範例

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

將 "**.\<區域名稱\>**" 附加至為 **accountName** 屬性所用的 Batch 帳戶名稱。 範例：

```json
"accountName": "mybatchaccount.eastus"
```

另一個選項是提供 batchUri 端點，如下列範例所示：

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>屬性
| 屬性          | 說明                              | 必要 |
| ----------------- | ---------------------------------------- | -------- |
| 類型              | type 屬性應設為 **AzureBatch**。 | 是      |
| accountName       | 建立 Azure Batch 帳戶。         | 是      |
| accessKey         | Azure Batch 帳戶的存取金鑰。  | 是      |
| poolName          | 虛擬機器的集區名稱。    | 是      |
| 預設容器 | 與此 Azure Batch 連結服務相關聯的 Azure 儲存體服務連結名稱。 此連結服務用於執行活動及儲存活動執行記錄檔所需的暫存檔案。 | 是      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning 連結服務
您可建立 Azure Machine Learning 連結服務，以向 Data Factory 註冊 Machine Learning 批次評分端點。

### <a name="example"></a>範例

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>屬性
| 屬性   | 說明                              | 必要 |
| ---------- | ---------------------------------------- | -------- |
| 類型       | type 屬性應設為： **AzureML**。 | 是      |
| mlEndpoint | 批次評分 URL。                   | 是      |
| apiKey     | 已發佈的工作區模型的 API。     | 是      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 連結服務
您需建立 **Azure Data Lake Analytics** 連結服務，來將 Azure Data Lake Analytics 計算服務連結到 Azure Data Factory。 管線中的 Data Lake Analytics U-SQL 活動會參考此連結服務。 

下表提供 JSON 定義中所使用之一般屬性的描述。 您可以在服務主體與使用者認證驗證之間進一步選擇。

| 屬性                 | 說明                              | 必要                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | type 屬性應設為： **AzureDataLakeAnalytics**。 | 是                                      |
| **accountName**          | Azure Data Lake Analytics 帳戶名稱。  | 是                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI。           | 否                                       |
| **subscriptionId**       | Azure 訂用帳戶識別碼                    | 否 (如果未指定，便會使用 Data Factory 的訂用帳戶)。 |
| **resourceGroupName**    | Azure 資源群組名稱                | 否 (若未指定，便會使用 Data Factory 的資源群組)。 |

### <a name="service-principal-authentication-recommended"></a>服務主體驗證 (建議)
若要使用服務主體驗證，請在 Azure Active Directory (Azure AD) 中註冊應用程式實體，並授與其 Data Lake Store 存取權。 如需詳細的步驟，請參閱[服務對服務驗證](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 請記下以下的值，您可以使用這些值來定義連結服務：
* 應用程式識別碼
* 應用程式金鑰 
* 租用戶識別碼

指定下列屬性以使用服務主體驗證：

| 屬性                | 說明                              | 必要 |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | 指定應用程式的用戶端識別碼。     | 是      |
| **servicePrincipalKey** | 指定應用程式的金鑰。           | 是      |
| **tenant**              | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是      |

**範例：服務主體驗證**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>使用者認證驗證
或者，您也可以藉由指定下列屬性，使用 Data Lake Analytics 的使用者認證驗證：

| 屬性          | 說明                              | 必要 |
| :---------------- | :--------------------------------------- | :------- |
| **authorization** | 按一下「資料處理站編輯器」中的 [授權] 按鈕，然後輸入您的認證，此動作會將自動產生的授權 URL 指派給此屬性。 | 是      |
| **sessionId**     | OAuth 授權工作階段的 OAuth 工作階段識別碼。 每個工作階段識別碼都是唯一的，只能使用一次。 當您使用「資料處理站編輯器」時便會自動產生此設定。 | 是      |

**範例：使用者認證授權**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>權杖到期
您使用 [授權] 按鈕所產生的授權碼在一段時間後會到期。 請參閱下表以了解不同類型的使用者帳戶的到期時間。 當驗證**權杖到期**時，您可能會看到下列錯誤訊息：認證作業發生錯誤：invalid_grant - AADSTS70002：驗證認證時發生錯誤。 AADSTS70008：提供的存取授權已過期或撤銷。 追蹤識別碼：d18629e8-af88-43c5-88e3-d8419eb1fca1 相互關連識別碼：fac30a0c-6be6-4e02-8d69-a776d2ffefd7 時間戳記：2015-12-15 21:09:31Z

| 使用者類型                                | 到期時間                            |
| :--------------------------------------- | :--------------------------------------- |
| 不受 Azure Active Directory 管理的使用者帳戶 (@hotmail.com、@live.com 等) | 12 小時                                 |
| 受 Azure Active Directory (AAD) 管理的使用者帳戶 | 最後一次執行配量後的 14 天。 <br/><br/>如果以 OAuth 式連結服務為基礎的配量至少每 14 天執行一次，則為 90 天。 |

如果要避免/解決此錯誤，請在**權杖到期**時使用 [授權] 按鈕重新授權，然後重新部署連結服務。 您也可以如下使用程式碼，以程式設計方式產生 **sessionId** 和 **authorization** 屬性的值：

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

請參閱 [AzureDataLakeStoreLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、[AzureDataLakeAnalyticsLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)和 [AuthorizationSessionGetResponse 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)主題，以取得在程式碼中使用的 Data Factory 類別的詳細資訊。 請針對 WindowsFormsWebAuthenticationDialog 類別，新增對下列項目的參考：Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll。 

## <a name="azure-sql-linked-service"></a>Azure SQL 連結服務
您可建立 Azure SQL 連結服務，並將其與 [預存程序活動](data-factory-stored-proc-activity.md) 搭配使用，以叫用 Data Factory 管線中的預存程序。 如需此連結服務的詳細資料，請參閱 [Azure SQL 連接器](data-factory-azure-sql-connector.md#linked-service-properties) 一文。

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL 資料倉儲連結服務
您可以建立 Azure SQL 資料倉儲連結服務，並將其與 [預存程序活動](data-factory-stored-proc-activity.md) 搭配使用，以叫用 Data Factory 管線中的預存程序。 如需此連結服務的詳細資料，請參閱 [Azure SQL 資料倉儲連接器](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) 一文。

## <a name="sql-server-linked-service"></a>SQL Server 連結服務
您可以建立 SQL Server 連結服務，並將其與 [預存程序活動](data-factory-stored-proc-activity.md) 搭配使用，以叫用 Data Factory 管線中的預存程序。 如需此連結服務的詳細資料，請參閱 [SQL Server 連接器](data-factory-sqlserver-connector.md#linked-service-properties) 一文。

