---
title: "使用 Azure Data Factory 在 Blob 儲存體中複製資料 | Microsoft Docs"
description: "建立 Azure 資料處理站，將資料從 Azure Blob 儲存體中的一個位置複製到相同 Blob 儲存體中其他位置的資料夾。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>使用 PowerShell 建立 Azure 資料處理站 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [第 2 版 - 預覽](quickstart-create-data-factory-powershell.md)

本快速入門說明如何使用 PowerShell 來建立 Azure 資料處理站。 在此 Data Factory 中建立的管線會將資料從 Azure Blob 儲存體中的一個資料夾**複製**到其他資料夾。 如需如何使用 Azure Data Factory **轉換**資料的教學課程，請參閱[教學課程︰使用 Spark 轉換資料](transform-data-using-spark.md)。 

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [開始使用 Data Factory 第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
>
> 本文不提供 Data Factory 服務的詳細簡介。 如需 Azure Data Factory 服務簡介，請參閱 [Azure Data Factory 簡介](introduction.md)。

## <a name="prerequisites"></a>必要條件

### <a name="azure-subscription"></a>Azure 訂閱
如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

### <a name="azure-roles"></a>Azure 角色
若要建立 Data Factory 執行個體，您用來登入 Azure 的使用者帳戶必須為**參與者**或**擁有者**角色，或是 Azure 訂用帳戶的**管理員**。 在 Azure 入口網站中，按一下右上角的 [使用者名稱]，然後選取 [權限] 來檢視您在訂用帳戶中所擁有的權限。 如果您有多個訂用帳戶的存取權，請選取適當的訂用帳戶。 如需將使用者新增至角色的範例指示，請參閱[新增角色](../billing/billing-add-change-azure-subscription-administrator.md)文章。

### <a name="azure-storage-account"></a>Azure 儲存體帳戶
您可以使用一般用途的 Azure 儲存體帳戶 (特別是 Blob 儲存體) 作為本教學課程中的**來源**和**目的地**資料存放區。 如果您沒有一般用途的 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)來建立帳戶。 

#### <a name="get-storage-account-name-and-account-key"></a>取得儲存體帳戶名稱和帳戶金鑰
您會在此快速入門中使用 Azure 儲存體帳戶的名稱和金鑰。 下列程序提供可取得儲存體帳戶名稱和金鑰的步驟。 

1. 啟動網頁瀏覽器並瀏覽至 [Azure 入口網站](https://portal.azure.com)。 使用您的 Azure 使用者名稱和密碼進行登入。 
2. 按一下左側功能表中的 [更多服務]，使用 **Storage** 關鍵字進行篩選，然後選取 [儲存體帳戶]。

    ![搜尋儲存體帳戶](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. 在儲存體帳戶清單中，篩選您的儲存體帳戶 (如有需要)，然後選取**您的儲存體帳戶**。 
4. 在 [儲存體帳戶] 頁面中，選取功能表上的 [存取金鑰]。

    ![取得儲存體帳戶名稱和金鑰](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. 將 [儲存體帳戶名稱] 和 [金鑰1] 欄位的值複製到剪貼簿。 將它們貼到 [記事本] 或任何其他編輯器中並加以儲存。  

#### <a name="create-input-folder-and-files"></a>建立輸入資料夾和檔案
在這一節中，您會在 Azure Blob 儲存體中建立一個名為 **adftutorial** 的 Blob 容器。 接著，您會在容器中建立名為 **input** 的資料夾，然後將範例檔案上傳到 input 資料夾。 

1. 在 [儲存體帳戶] 頁面上，切換至 [概觀]，然後按一下 [Blob]。 

    ![選取 Blob 選項](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. 在 [Blob 服務] 頁面上，按一下工具列上的 [+ 容器]。 

    ![新增容器按鈕](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. 在 [新增容器] 對話方塊中，輸入 **adftutorial** 作為名稱，然後按一下 [確定]。 

    ![輸入容器名稱](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. 在容器清單中按一下 [adftutorial]。 

    ![選取容器](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. 在 [容器] 頁面上，按一下工具列上的 [上傳]。  

    ![上傳按鈕](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. 在 [上傳 blob] 頁面上，按一下 [進階]。

    ![按一下 [進階] 連結](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. 啟動 [記事本] 並使用下列內容建立名為 **emp.txt** 的檔案︰將它儲存在 **c:\ADFv2QuickStartPSH** 資料夾：如果資料夾不存在，建立資料夾 **ADFv2QuickStartPSH**。
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. 在 Azure 入口網站的 [上傳 blob] 頁面上，瀏覽並選取 **emp.txt** 檔，以找到 [檔案] 欄位。 
9. 輸入 **input** 作為 [上傳到資料夾] 欄位的值。 

    ![上傳 blob 設定](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. 確認資料夾是 **input**，且檔案是 **emp.txt**，然後按一下 [上傳]。
11. 您應該會在清單中看到 **emp.txt** 檔案以及上傳的狀態。 
12. 按一下角落的 [X] 關閉 [上傳 blob] 頁面。 

    ![關閉上傳 blob 頁面](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. 將 [容器] 頁面保持開啟。 您可以在本快速入門結尾處使用它來確認輸出。 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>安裝 Azure PowerShell
如果您的電腦上沒有最新的 Azure PowerShell，請加以安裝。 

1. 在網頁瀏覽器中，瀏覽至 [Azure SDK 下載和 SDK](https://azure.microsoft.com/downloads/) 頁面。 
2. 按一下 [命令列工具] -> [PowerShell] 區段中的 [Windows 安裝]。 
3. 若要安裝 Azure PowerShell，執行 **MSI** 檔案。 

如需詳細指示，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/install-azurerm-ps)。 

#### <a name="log-in-to-azure-powershell"></a>登入 Azure PowerShell

1. 在您的電腦上啟動 **PowerShell**。 保持開啟 Azure PowerShell，直到本快速入門結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。

    ![啟動 PowerShell](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. 執行下列命令，並輸入您用來登入 Azure 入口網站的相同 Azure 使用者名稱和密碼：
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 如果您有多個 Azure 訂用帳戶，請執行下列命令以檢視此帳戶的所有訂用帳戶：

    ```powershell
    Get-AzureRmSubscription
    ```
3. 執行下列命令以選取您要使用的訂用帳戶。 以您的 Azure 訂用帳戶識別碼取代 **SubscriptionId**：

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>建立 Data Factory
1. 定義資源群組名稱的變數，以便稍後在 PowerShell 命令中使用。 將下列命令文字複製到 PowerShell，以雙引號指定 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)的名稱，然後執行命令。 例如： `"adfrg"`。
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. 定義 Data Factory 名稱的變數。 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. 定義 Data Factory 位置的變數： 

    ```powershell
    $location = "East US"
    ```
4. 若要建立 Azure 資源群組，請執行下列命令： 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    如果資源群組已經存在，您可能不想覆寫它。 將不同的值指派給 `$resourceGroupName` 變數，然後執行一次命令。 
5. 若要建立 Data Factory，請執行下列 **Set-AzureRmDataFactoryV2** Cmdlet： 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

請注意下列幾點：

* Azure Data Factory 的名稱在全域必須是唯一的。 如果發生下列錯誤，請變更名稱，並再試一次。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* 若要建立 Data Factory 執行個體，您用來登入 Azure 的使用者帳戶必須為**參與者**或**擁有者**角色，或是 Azure 訂用帳戶的**管理員**。
* 目前，Data Factory 第 2 版只允許您在美國東部、美國東部 2 和西歐區域中建立資料處理站。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他區域。

## <a name="create-a-linked-service"></a>建立連結的服務

在資料處理站中建立的連結服務，會將您的資料存放區和計算服務連結到資料處理站。 在本快速入門中，您要建立連結 Azure 儲存體的服務，可用來作為來源和接收的存放區。 連結的服務具有連線資訊，可供 Data Factory 服務在執行階段中用來連線。

1. 在 **C:\ADFv2QuickStartPSH** 資料夾中，使用以下內容建立名為 **AzureStorageLinkedService.json** 的 JSON 檔案：(如果 ADFv2QuickStartPSH 資料夾不存在，則加以建立。)。 

    > [!IMPORTANT]
    > 儲存檔案前，以 Azure 儲存體帳戶的名稱和金鑰取代 &lt;accountName&gt; 和 &lt;accountKey&gt;。

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. 在 **Azure PowerShell** 中，切換到 **ADFv2QuickStartPSH** 資料夾。

3. 執行 **Set-AzureRmDataFactoryV2LinkedService** Cmdlet 來建立連結服務：**AzureStorageLinkedService**。 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    以下是範例輸出：

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>建立資料集
在此步驟中，您可以定義資料集來代表要從來源複製到接收的資料。 資料集是 **AzureBlob** 的類型。 它會參考您在前一個步驟中建立的 **Azure 儲存體連結服務**。 它會接受參數來建構 **folderPath** 屬性。 針對輸入資料集，管線中的複製活動會傳遞輸入路徑作為此參數的值。 同樣地，針對輸出資料集，複製活動會傳遞輸出路徑作為此參數的值。 

1. 在 **C:\ADFv2QuickStartPSH** 資料夾中，使用下列內容建立名為 **BlobDataset.json** 的 JSON 檔案：

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. 若要建立資料集：**BlobDataset**，請執行 **Set-AzureRmDataFactoryV2Dataset** Cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    以下是範例輸出：

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>建立管線
  
在本快速入門中，您要透過採用兩個參數 -輸入 Blob 路徑和輸出 Blob 路徑的一個活動來建立此管線。 這些參數的值是在觸發/執行管線時設定。 複製活動會使用在前一個步驟中建立作為輸入和輸出的同一個 Blob 資料集。 將該資料集用作輸入資料集時，即會指定輸入路徑。 並且，將該資料集用作輸出資料集時，即會指定輸出路徑。 

1. 在 **C:\ADFv2QuickStartPSH** 資料夾中，使用下列內容建立名為 **Adfv2QuickStartPipeline.json** 的 JSON 檔案：

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. 若要建立管線：**Adfv2QuickStartPipeline**，請執行 **Set-AzureRmDataFactoryV2Pipeline** Cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    以下是範例輸出：

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>建立管線執行

在此步驟中，您會以來源和接收 Blob 路徑的實際值設定管線參數 **inputPath** 和 **outputPath** 的值。 接著，您可以使用這些引數來建立管線執行。 

1. 在 **C:\ADFv2QuickStartPSH** 資料夾中，使用下列內容建立名為 **PipelineParameters.json** 的 JSON 檔案：

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. 執行 **Invoke-AzureRmDataFactoryV2Pipeline** Cmdlet 來建立管線執行，並傳入參數值。 Cmdlet 會傳回管線執行識別碼，方便後續監視。

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>監視管道執行

1. 執行下列 PowerShell 程式碼以持續檢查管線執行狀態，直到完成複製資料為止。 在 PowerShell 視窗中複製/貼上下列指令碼，然後按 ENTER。 

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 10
    }
    ```

    以下是管線執行的範例輸出：

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    如果您看到錯誤：
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    請執行下列步驟： 
    1. 在 AzureStorageLinkedService.json 中，確認您 Azure 儲存體帳戶的名稱和金鑰是正確的。 
    2. 請確認連接字串的格式是正確的。 例如，AccountName 和 AccountKey 屬性會以分號 (`;`) 字元分隔。 
    3. 如果您的帳戶名稱和帳戶金鑰周圍有角括號，請將它們移除。 
    4. 下列為範例連接字串： 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. 遵循[建立連結的服務](#create-a-linked-service)一節中的步驟重新建立連結的服務。 
    6. 遵循[建立管線執行](#create-a-pipeline-run)重新執行管線。 
    7. 再次執行目前的監視命令來監視新的管線執行。 
1. 執行下列指令碼來取出複製活動執行詳細資料，例如，讀取/寫入資料的大小。

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. 確認您看到的輸出類似下列活動執行結果範例輸出：

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>驗證輸出
管道會自動在 adftutorial Blob 容器中建立輸出資料夾。 然後，它會將 emp.txt 檔案從輸入資料夾複製到輸出資料夾。 

1. 在 Azure 入口網站的 [adftutorial] 容器頁面上，按一下 [重新整理] 可查看輸出資料夾。 
    
    ![重新整理](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. 在資料夾清單中按一下 [輸出]。 
2. 確認 **emp.txt** 已複製到輸出資料夾。 

    ![重新整理](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>清除資源
您有兩種方式可以清除您在本快速入門中建立的資源。 您可以刪除 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)，其中包括資源群組中的所有資源。 如果您想要讓其他資源保持不變，請僅刪除您在本教學課程中建立的資料處理站。

刪除資源群組會刪除所有資源，包含其中的 Data Factory。 執行下列命令來刪除整個資源群組： 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

如果您需要只刪除 Data Factory，而非整個資源群組，請執行下列命令： 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>後續步驟
在此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 瀏覽[教學課程](tutorial-copy-data-dot-net.md)以了解使用 Data Factory 的更多案例。 
