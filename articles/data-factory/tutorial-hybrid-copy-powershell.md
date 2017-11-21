---
title: "使用 Azure Data Factory 將資料從 SQL Server 複製到 Blog 儲存體 | Microsoft Docs"
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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: afd7735712d03110a67509a7e94d336219a65b34
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>教學課程：將資料從內部部署 SQL Server 複製到 Azure Blob 儲存體
在本教學課程中，您會使用 Azure PowerShell 建立 Data Factory 管線，以將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體。 您會建立和使用 Azure Data Factory 的自我裝載整合執行階段 (IR)，這可以整合內部部署資料存放區和雲端資料存放區。  若要了解如何使用其他工具/SDK 來建立資料處理站，請參閱[快速入門](quickstart-create-data-factory-dot-net.md)。

本文不提供 Data Factory 服務的詳細簡介。 如需 Azure Data Factory 服務簡介，請參閱 [Azure Data Factory 簡介](introduction.md)。 

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

您會在本教學課程中執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立自我裝載整合執行階段。
> * 建立 SQL Server 和 Azure 儲存體連結的服務。 
> * 建立 SQL Server 和 Azure Blob 資料集。
> * 建立具有複製活動的管線來移動資料。
> * 啟動管線執行。
> * 監視管道執行。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>必要條件

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 或 2016。 
在本教學課程中，您會使用內部部署 SQL 資料庫作為**來源**資料存放區。 在您的 SQL Server 資料庫中建立名為 **emp** 的資料表，並在資料表中插入幾個範例項目。

1. 啟動 **SQL Server Management Studio**。 如果您使用 SQL Server 2016，您可能需要從[下載中心](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)個別地安裝 SQL Server Management Studio。 
2. 使用您的認證連線到 SQL Server。 
3. 建立範例資料庫。 在樹狀檢視中，以滑鼠右鍵按一下 [資料庫]，然後按一下 [新增資料庫]。 在 [新增資料庫] 對話方塊中，輸入資料庫的**名稱**，然後按一下 [確定]。 
4. 針對資料庫執行下列查詢指令碼，以建立 **emp** 資料表。 在樹狀檢視中，以滑鼠右鍵按一下您建立的**資料庫**，然後按一下 [新增查詢]。 

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
2. 對資料庫執行下列命令並將某些範例資料插入資料表中：

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Azure 儲存體帳戶
您可以使用一般用途的 Azure 儲存體帳戶 (特別是 Blob 儲存體) 作為本教學課程中的**目的地/接收**資料存放區。 如果您沒有一般用途的 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)來建立帳戶。

#### <a name="get-storage-account-name-and-account-key"></a>取得儲存體帳戶名稱和帳戶金鑰
您會在此快速入門中使用 Azure 儲存體帳戶的名稱和金鑰。 下列程序提供可取得儲存體帳戶名稱和金鑰的步驟。 

1. 啟動網頁瀏覽器並瀏覽至 [Azure 入口網站](https://portal.azure.com)。 使用您的 Azure 使用者名稱和密碼進行登入。 
2. 按一下左側功能表中的 [更多服務]，使用 **Storage** 關鍵字進行篩選，然後選取 [儲存體帳戶]。

    ![搜尋儲存體帳戶](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. 在儲存體帳戶清單中，篩選您的儲存體帳戶 (如有需要)，然後選取**您的儲存體帳戶**。 
4. 在 [儲存體帳戶] 頁面中，選取功能表上的 [存取金鑰]。

    ![取得儲存體帳戶名稱和金鑰](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. 將 [儲存體帳戶名稱] 和 [金鑰1] 欄位的值複製到剪貼簿。 將它們貼到 [記事本] 或任何其他編輯器中並加以儲存。 您會在本教學課程中使用儲存體帳戶名稱和金鑰。 

#### <a name="create-the-adftutorial-container"></a>建立 adftutorial 容器 
在這一節中，您會在 Azure Blob 儲存體中建議一個名為 adftutorial 的 Blob 容器。 

1. 如果您的電腦上沒有 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，請加以安裝。 
2. 在您的電腦上啟動 **Microsoft Azure 儲存體總管**。   
3. 在 [連線到 Azure 儲存體] 視窗中，選取 [使用儲存體帳戶名稱和金鑰]，然後按 [下一步]。 如果您沒有看到 [連線到 Azure 儲存體] 視窗，以滑鼠右鍵按一下樹狀檢視中的 [儲存體帳戶]，然後按一下 [連線到 Azure 儲存體]。 

    ![連線到 Azure 儲存體](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. 在 使用名稱和金鑰附加 視窗中，貼上您在前一個步驟中儲存的 帳戶名稱 和 帳戶金鑰。 然後按 [下一步] 。 
5. 在 [連線摘要] 視窗中，按一下 [連線]。
6. 確認您在樹狀檢視中看到您的儲存體帳戶位於 [(本機和已連結)] -> [儲存體帳戶] 之下。 
7. 展開 [Blob 容器] 並確認名為 **adftutorial** 的 Blob 容器不存在。 如果已經存在，請略過用於建立容器的後續步驟。 
8. 以滑鼠右鍵按一下 [Blob 容器]，然後選取 [建立 Blob 容器]。

    ![建立 Blob 容器](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. 輸入 **adftutorial** 作為名稱，然後按 **ENTER**。 
10. 確認已在樹狀檢視中選取 **adftutorial** 容器。 Data Factory 會在此容器中自動建立輸出資料夾，因此您不需要建立輸出資料夾。 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>安裝 Azure PowerShell
如果您的電腦上沒有最新的 Azure PowerShell，請加以安裝。 

1. 在網頁瀏覽器中，瀏覽至 [Azure SDK 下載和 SDK](https://azure.microsoft.com/downloads/) 頁面。 
2. 按一下 [命令列工具] -> [PowerShell] 區段中的 [Windows 安裝]。 
3. 若要安裝 Azure PowerShell，執行 **MSI** 檔案。 

如需詳細指示，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/install-azurerm-ps)。 

#### <a name="log-in-to-azure-powershell"></a>登入 Azure PowerShell
在您的電腦上啟動 **PowerShell**。 保持開啟 Azure PowerShell，直到本快速入門結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。

1. 執行下列命令，並輸入您用來登入 Azure 入口網站的 Azure 使用者名稱和密碼：
       
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

1. 定義資源群組名稱的變數，以便稍後在 PowerShell 命令中使用。 將下列命令文字複製到 PowerShell，以雙引號指定 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)的名稱，然後執行命令。 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. 定義資料處理站名稱的變數，以便稍後在 PowerShell 命令中使用。 

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

    如果資源群組已經存在，您可能不想覆寫它。 將不同的值指派給 `$resourceGroupName` 變數，然後再試一次。 如果您想要與其他人共用資源群組，請繼續進行下一個步驟。  
5. 若要建立 Data Factory，請執行下列 **Set-AzureRmDataFactoryV2** Cmdlet： 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

請注意下列幾點：

* Azure Data Factory 的名稱在全域必須是唯一的。 如果發生下列錯誤，請變更名稱，並再試一次。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* 若要建立 Data Factory 執行個體，您必須是 Azure 訂用帳戶的**參與者**或**系統管理員**。
* 目前，Data Factory 第 2 版只允許您在美國東部、美國東部 2 和西歐區域中建立資料處理站。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他區域。

## <a name="create-a-self-hosted-ir"></a>建立自我裝載 IR

在本節中，您可以建立自我裝載整合執行階段，並將它與內部部署節點 (機器) 產生關聯。

1. 建立整合執行階段名稱的變數。 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. 建立自我裝載整合執行階段。 請使用唯一名稱，以防有另一個同名的整合執行階段存在。

   ```powershell
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
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>安裝整合執行階段
1. 在本機 windows 電腦上[下載](https://www.microsoft.com/download/details.aspx?id=39717)自我裝載的整合執行階段並執行安裝。 
2. 在 [歡迎使用 Microsoft Integration Runtime 設定精靈] 中，按 [下一步]。  
3. 在 [使用者授權合約] 頁面上，接受條款和授權合約，然後按 [下一步]。 
4. 在 [目的地資料夾] 頁面上，按 [下一步]。 
5. 在 [準備好要安裝 Microsoft Integration Runtime] 上按一下 [安裝]。 
6. 如果您看到一則有關正在設定不使用時要進入睡眠或休眠模式之電腦的警告訊息，請按一下 [確定]。 
7. 在 [完成 Microsoft Integration Runtime 設定精靈] 頁面，按一下 [完成]。
8. 在 [註冊 Integration Runtime (自我裝載)] 頁面上，貼上您在上一節中儲存的金鑰，然後按一下 [註冊]。 

   ![監視整合執行階段](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. 自我裝載整合執行階段註冊成功時，您會看到下列訊息：

   ![已成功註冊](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. 在 [新增 Integration Runtime (自我裝載) 節點] 頁面上，按 [下一步]。 

    ![新增 Integration Runtime 節點頁面](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. 在 [內部網路通訊通道] 上，按一下 [略過]。 您可以選取 TLS/SSL 憑證，以便保護在多節點整合執行階段環境中的內部節點通訊。 

    ![內部網路通訊通道頁面](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. 在 [註冊 Integration Runtime (自我裝載)] 頁面上，按一下 [啟動組態管理員]。 
6. 當節點已連線至雲端服務時，您會看到下列頁面：

   ![節點已連線](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>建立連結的服務

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>建立 Azure 儲存體連結服務 (目的地/接收)

1. 在 **C:\ADFv2Tutorial** 資料夾中，使用下列內容建立名為 **AzureStorageLinkedService.json** 的 JSON 檔案：如果 ADFv2Tutorial 資料夾尚不存在，請加以建立。  

    > [!IMPORTANT]
    > 儲存檔案前，以 Azure 儲存體帳戶的名稱和金鑰取代 &lt;accountName&gt; 和 &lt;accountKey&gt;。

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

1. 在 **C:\ADFv2Tutorial** 資料夾中，使用下列內容建立名為 **SqlServerLinkedService.json** 的 JSON 檔案：儲存檔案之前，以您的 SQL Server 值取代 **&lt;servername>**、**&lt;databasename>**、**&lt;username>****&lt;servername>** 和 **&lt;password>**。 

    > [!IMPORTANT]
    > 以您的整合執行階段名稱取代 **&lt;integration****runtime** **name>**。

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
2. 若要加密從內部部署自我裝載整合執行階段的 JSON 承載所傳來的敏感性資料，請執行 **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** 並傳遞上述的 JSON 承載。 此加密可確保使用資料保護應用程式開發介面 (DPAPI) 來加密認證。 已加密的認證會儲存在本機的自我裝載整合執行階段節點 (本機電腦) 上。 輸出承載可以重新導向至另一個包含加密認證的 JSON 檔案 (在此案例中是 'encryptedLinkedService.json')。
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. 使用上一個步驟中的 JSON 執行下列命令，以建立**SqlServerLinkedService**：

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>建立資料集
在此步驟中，您會建立代表複製作業的輸入和輸出資料的輸入和輸出資料集 (內部部署 SQL Server 資料庫 => Azure Blob 儲存體)。

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
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>驗證輸出
管道會自動在 `adftutorial` Blob 容器中建立名為 `fromonprem` 的輸出資料夾。 確認您在輸出資料夾中看到 **dbo.emp.txt** 檔案。 使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 來驗證所建立的輸出。 

## <a name="next-steps"></a>後續步驟
在此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 您已了解如何︰

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立自我裝載整合執行階段。
> * 建立 SQL Server 和 Azure 儲存體連結的服務。 
> * 建立 SQL Server 和 Azure Blob 資料集。
> * 建立具有複製活動的管線來移動資料。
> * 啟動管線執行。
> * 監視管道執行。

如需 Azure Data Factory 所支援的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。

進入下列教學課程，以了解如何將大量資料從來源複製到目的地：

> [!div class="nextstepaction"]
>[複製大量資料](tutorial-bulk-copy.md)
