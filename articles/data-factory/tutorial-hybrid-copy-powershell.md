---
title: "使用 Azure Data Factory 將內部部署資料複製到雲端 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 中的自我裝載整合執行階段，將資料從內部部署資料存放區複製到 Azure 雲端。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/14/2017
ms.author: jingwang
ms.openlocfilehash: 9aac9c9bcc609a91415438279419d4cc8e237fcb
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="copy-data-between-on-premises-and-cloud"></a>在內部部署和雲端之間複製資料
Azure Data Factory 是雲端式資料整合服務，可讓您在雲端建立資料驅動工作流程，以便協調及自動進行資料移動和資料轉換。 使用 Azure Data Factory，您可以建立和排程資料驅動工作流程 (稱為管線)，這類工作流程可以從不同資料存放區內嵌資料，使用計算服務 (例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure Machine Learning) 來處理/轉換資料，以及將輸出資料發佈至資料存放區 (例如 Azure SQL 資料倉儲)，以供商業智慧 (BI) 應用程式使用。

在本教學課程中，您會使用 Azure PowerShell 建立 Data Factory 管線，以將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體。 您會建立和使用 Azure Data Factory 的自我裝載整合執行階段 (IR)，這可以整合內部部署資料存放區和雲端資料存放區。  若要了解如何使用其他工具/SDK 來建立資料處理站，請參閱[快速入門](quickstart-create-data-factory-dot-net.md)。

您會在本教學課程中執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立自我裝載整合執行階段。
> * 在自我裝載整合執行階段上建立並加密內部部署 SQL Server 連結服務。
> * 建立 Azure 儲存體連結服務。
> * 建立 SQL Server 和 Azure 儲存體資料集。
> * 建立具有複製活動的管線來移動資料。
> * 啟動管線執行。
> * 監視管道和活動執行。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>必要條件

* **SQL Server**。 在本教學課程中，您會使用內部部署 SQL 資料庫作為**來源**資料存放區。
* **Azure 儲存體帳戶**。 在本教學課程中，您會使用 Azure Blob 儲存體作為**目的地/接收**資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱 [建立儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account) 一文以取得建立步驟。
* **Azure PowerShell**(英文)。 遵循[如何安裝並設定 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的指示。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 **PowerShell**。 將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。

    執行下列命令，並輸入您用來登入 Azure 入口網站的使用者名稱和密碼：
    ```powershell
    Login-AzureRmAccount
    ```
    執行下列命令以檢視此帳戶的所有訂用帳戶：

    ```powershell
    Get-AzureRmSubscription
    ```
    執行下列命令以選取您要使用的訂用帳戶。 以您的 Azure 訂用帳戶識別碼取代 **SubscriptionId**：

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. 執行 **Set-AzureRmDataFactoryV2** Cmdlet 來建立資料處理站。 執行命令之前，以您自己的值取代預留位置。

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    請注意下列幾點：

    * Azure Data Factory 的名稱在全域必須是唯一的。 如果發生下列錯誤，請變更名稱，並再試一次。

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * 若要建立 Data Factory 執行個體，您必須是 Azure 訂用帳戶的參與者或系統管理員。
    * 目前，Data Factory 只允許您在美國東部和美國東部 2 區域中建立資料處理站。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他區域。

## <a name="create-a-self-hosted-ir"></a>建立自我裝載 IR

在本節中，您可以建立自我裝載整合執行階段，並將它與內部部署節點 (機器) 產生關聯。

1. 建立自我裝載整合執行階段。 請使用唯一名稱，以防有另一個同名的整合執行階段存在。

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   以下是範例輸出：

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. 執行下列命令，以取出建立的整合執行階段的狀態。

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   以下是範例輸出：

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. 執行下列命令來取出驗證金鑰，用以向雲端中的 Data Factory 服務註冊自我裝載整合執行階段。 複製其中一個金鑰，以註冊自我裝載整合執行階段。

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   以下是範例輸出：

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. 將自我裝載整合執行階段[下載](https://www.microsoft.com/download/details.aspx?id=39717)到本機 Windows 電腦，然後使用上一個步驟中取得的驗證金鑰，手動註冊自我裝載整合執行階段。

   ![監視整合執行階段](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   自我裝載整合執行階段註冊成功時，您會看到下列訊息：

   ![已成功註冊](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   當節點已連線至雲端服務時，您會看到下列頁面：

   ![節點已連線](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>建立連結的服務

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>建立 Azure 儲存體連結服務 (目的地/接收)

1. 在 **C:\ADFv2Tutorial** 資料夾中，使用下列內容建立名為 **AzureStorageLinkedService.json** 的 JSON 檔案。 如果 ADFv2Tutorial 資料夾不存在，請建立。  以您的 Azure 儲存體帳戶名稱和金鑰取代 &lt;accountname&gt; 和 &lt;accountkey&gt;。

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. 在 **Azure PowerShell** 中，切換至 **ADFv2Tutorial** 資料夾。

   執行 **Set-AzureRmDataFactoryV2LinkedService** Cmdlet 來建立連結服務：**AzureStorageLinkedService**。 本教學課程中使用的 Cmdlet 會採用 **ResourceGroupName** 和 **DataFactoryName** 參數的值。 或者，您可以傳遞 Set-AzureRmDataFactoryV2 Cmdlet 所傳回的 **DataFactory** 物件，就不需要在每次執行 Cmdlet 時輸入 ResourceGroupName 和 DataFactoryName。

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   以下是範例輸出：

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>建立及加密 SQL Server 連結服務 (來源)

1. 在 **C:\ADFv2Tutorial** 資料夾中，使用下列內容建立名為 **SqlServerLinkedService.json** 的 JSON 檔案：儲存檔案之前，以您的 SQL Server 值取代 **&lt;servername>**、**&lt;databasename>**、**&lt;username>****&lt;servername>** 和 **&lt;password>**。 以您的整合執行階段名稱取代 **&lt;integration****runtime** **name>**。

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. 若要加密從內部部署自我裝載整合執行階段的 JSON 承載所傳來的敏感性資料，我們可以執行 **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** 並傳遞上述的 JSON 承載。 此加密可確保使用資料保護應用程式開發介面 (DPAPI) 來加密認證，並儲存在本機的自我裝載整合執行階段節點。 輸出承載可以重新導向至另一個包含加密認證的 JSON 檔案 (在此案例中是 'encryptedLinkedService.json')。

    執行命令之前，以您的整合執行階段名稱取代 **&lt;integration runtime name&gt;**。

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. 使用上一個步驟中的 JSON 執行下列命令，以建立**SqlServerLinkedService**：

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>建立資料集

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>準備用於教學課程的內部部署 SQL Server

在此步驟中，您會建立代表複製作業的輸入和輸出資料的輸入和輸出資料集 (內部部署 SQL Server 資料庫 => Azure Blob 儲存體)。 建立資料集之前，請執行下列步驟 (詳細步驟隨附於清單後)：

- 在您新增為 Data Factory 連結服務的 SQL Server 資料庫中，建立名為 **emp** 的資料表，並在資料表中插入幾個範例項目。
- 在您加入 Data Factory 作為連結服務的 Azure Blob 儲存體帳戶中，建立名為 **adftutorial** 的 Blob 容器。


1. 在您指定用於內部部署 SQL Server 連結服務 (**SqlServerLinkedService**) 的資料庫中，使用下列 SQL 指令碼在資料庫中建立 **emp** 資料表。

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. 在資料表中插入一些範例：

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>建立來源 SQL Database 的資料集

1. 在 **C:\ADFv2Tutorial** 資料夾中，使用下列內容建立名為 **SqlServerDataset.json** 的 JSON 檔案：  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. 若要建立資料集：**SqlServerDataset**，請執行 **Set-AzureRmDataFactoryV2Dataset** Cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    以下是範例輸出：

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>建立接收 Azure Blob 儲存體的資料集

1. 在 **C:\ADFv2Tutorial** 資料夾中，使用下列內容建立名為 **AzureBlobDataset.json** 的 JSON 檔案：

    > [!IMPORTANT]
    > 此範例程式碼假設您在 Azure Blob 儲存體中有一個名為 **adftutorial** 的容器。

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. 若要建立資料集：**AzureBlobDataset**，請執行 **Set-AzureRmDataFactoryV2Dataset** Cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    以下是範例輸出：

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>建立管線

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>建立管道 "SqlServerToBlobPipeline"

1. 在 **C:\ADFv2Tutorial** 資料夾中，使用下列內容建立名為 **SqlServerToBlobPipeline.json** 的 JSON 檔案：

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. 若要建立管線：**SQLServerToBlobPipeline**，請執行 **Set-AzureRmDataFactoryV2Pipeline** Cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    以下是範例輸出：

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>啟動及監視管道執行

1. 啟動 "SQLServerToBlobPipeline" 管道的管道執行，並擷取管道執行識別碼，方便後續監視。  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. 執行下列程式碼以持續檢查管道 "**SQLServerToBlobPipeline**" 的執行狀態，並印出最終結果。

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    執行範例的輸出如下：

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. 您可以取得管道 "**SQLServerToBlobPipeline**" 的執行識別碼，並檢查詳細的活動執行結果，如下所示。

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    執行範例的輸出如下：

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. 連線至接收 Azure Blob 儲存體，並確認已從 Azure SQL Database 正確複製資料。

## <a name="next-steps"></a>後續步驟
在此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 您已了解如何︰

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立自我裝載整合執行階段
> * 在自我裝載整合執行階段上建立並加密內部部署 SQL Server 連結服務
> * 建立 Azure 儲存體連結服務。
> * 建立 SQL Server 和 Azure 儲存體資料集。
> * 建立具有複製活動的管線來移動資料。
> * 啟動管線執行。
> * 監視管道和活動執行。

如需 Azure Data Factory 支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。

進入下列教學課程，以了解如何將大量資料從來源複製到目的地：

> [!div class="nextstepaction"]
>[複製大量資料](tutorial-bulk-copy.md)
