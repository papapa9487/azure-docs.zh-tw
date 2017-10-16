---
title: "使用 Data Factory 建立隨選 Hadoop 叢集 - Azure HDInsight | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 在 HDInsight 中建立隨選 Handooop 叢集。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: c1061811d205494969047fa3f91cbf449a25d8ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>使用 Azure Data Factory 在 HDInsight 中建立隨選 Handooop 叢集
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure Data Factory](../data-factory/introduction.md) 是雲端架構資料整合服務，用來協調以及自動移動和轉換資料。 它可以建立 HDInsight Hadoop 叢集 Just-in-Time 來處理輸入資料配量並在處理序完成時刪除叢集。 使用隨選 HDInsight Hadoop 叢集的優點包括︰

- 您只需支付作業在 HDInsight Hadoop 叢集上執行的時間 (加上簡短的可設定閒置時間)。 不論使用與否，HDInsight 叢集都是按分鐘計費。 當您在 Data Factory 中使用隨選 HDInsight 連結服務時，會隨選建立叢集。 而叢集會在作業完成時自動刪除。 所以您只需對作業執行時間和短暫閒置時間 (存留時間設定) 付費。
- 您可以使用 Data Factory 管線建立工作流程。 例如，您可以用管線將資料從內部部署 SQL Server 複製到 Azure blob 儲存體，在隨選 HDInsight Hadoop 叢集上執行 Hive 指令碼和 Pig 指令碼來處理資料。 然後，將結果資料複製到 Azure SQL 資料倉儲以供 BI 應用程式使用。
- 您可以排程定期 (每小時、每天、每週、每月等) 執行工作流程。

在 Azure Data Factory 中，資料處理站可以有一或多個資料管線。 資料管線具有一或多個活動。 兩種活動類型︰[資料移動活動](../data-factory/copy-activity-overview.md)和[資料轉換活動](../data-factory/transform-data.md)。 您可以使用資料移動活動 (目前，只有複製活動)，將資料從來源資料存放區移到目的地資料存放區。 使用資料轉換活動以處理/轉換資料。 HDInsight Hive 活動是 Data Factory 所支援的其中一個轉換活動。 您在本教學課程中使用 Hive 轉換活動。

您可以設定 Hive 活動使用您自己的 HDInsight Hadoop 叢集或隨選 HDInsight Hadoop 叢集。 在本教學課程中，資料處理站管線中的 Hive 活動會設定為使用隨 HDInsight 叢集。 因此，當執行活動以處理資料配量時，以下是會發生的事︰

1. 會自動建立 HDInsight Hadoop 叢集 just-in-time 以處理配量。  
2. 會在叢集上執行 HiveQL 指令碼以處理輸入資料。
3. 處理完成後，會刪除 HDInsight Hadoop 叢集，且叢集在設定的一段時間會處於閒置狀態 (timeToLive 設定)。 如果下一個資料配量可用於在此 timeToLive 閒置時間中進行處理，相同的叢集會用來處理配量。  

在本教學課程中，與 Hive 活動相關聯的 HiveQL 指令碼會執行下列動作︰

1. 建立參考儲存在 Azure blob 儲存體中未經處理之 Web 記錄資料的外部資料表。
2. 依年份和月份分割的未經處理資料。
3. Azure blob 儲存體中儲存的分割資料。

在本教學課程中，與 Hive 活動相關聯的 HiveQL 指令碼會建立外部資料表，其會參考 Azure Blob 儲存體中儲存的未經處理 web 記錄資料。 這裡是輸入檔中每個月份的資料列範例。

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

HiveQL 指令碼會依年份和月份分割未經處理的資料。 它會根據先前的輸入建立三個輸出資料夾。 每個資料夾都包含一個檔案，內含每個月的項目。

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

如需 Data Factory 資料轉換活動 (Hive 活動除外) 的清單，請參閱 [使用 Azure Data Factory 進行轉換和分析](../data-factory/transform-data.md)。

> [!NOTE]
> 目前，您只可以從 Azure Data Factory 建立 HDInsight 叢集 3.2 版。

## <a name="prerequisites"></a>必要條件
開始執行本文中的指示之前，您必須擁有以下項目：

* [Azure 訂用帳戶](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell。

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>準備儲存體帳戶
在此案例中，您最多可以使用三個儲存體帳戶︰

- HDInsight 叢集的預設儲存體帳戶
- 輸入資料的儲存體帳戶
- 輸出資料的儲存體帳戶

為了簡化本教學課程，您會將一個儲存體帳戶用於 3 個用途。 本節中找到的 Azure PowerShell 範例指令碼會執行下列工作：

1. 登入 Azure。
2. 建立 Azure 資源群組。
3. 建立 Azure 儲存體帳戶。
4. 在儲存體帳戶中建立 Blob 容器
5. 將下列兩個檔案複製到 Blob 容器︰

   * 輸入資料檔： [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * HiveQL 指令碼： [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     這兩個檔案會儲存在公用 Blob 容器。


**使用 Azure PowerShell 準備處存體並複製檔案：**
> [!IMPORTANT]
> 指定指令碼會建立之 Azure 資源群組和 Azure 儲存體帳戶的名稱。
> 記下指令碼所輸出的**資源群組名稱**、**儲存體帳戶名稱**和**儲存體帳戶金鑰**。 您在下一節中需要這些資料。

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

如需有關此 PowerShell 指定碼的說明，請參閱 [使用 Azure PowerShell 搭配 Azure 儲存體](../storage/common/storage-powershell-guide-full.md)。 如果您想要改為使用 Azure CLI，請參閱 Azure CLI 指令碼的[附錄](#appendix)區段。

**檢查儲存體帳戶和內容**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側面板上的 [資源群組]  。
3. 按兩下您在 PowerShell 指令碼中建立的資源群組名稱。 如果列出太多的資源群組，請使用篩選器。
4. 除非您與其他專案共用資源群組，否則 [資源]  圖格應列出一個資源。 該資源是您先前指定名稱的儲存體帳戶。 按一下儲存體帳戶名稱。
5. 按一下 [Blob]  圖格。
6. 按一下 [adfgetstarted]  容器。 您會看到兩個資料夾︰[輸入資料] 和 [指令碼]。
7. 開啟資料夾並檢查資料夾中的檔案。 Inputdata 包含 input.log 檔案與輸入資料，而指令碼資料夾包含 HiveQL 指令碼檔案。

## <a name="create-a-data-factory-using-resource-manager-template"></a>使用 Resource Manager 範本建立資料處理站
備妥儲存體帳戶、輸入資料和 HiveQL 指令碼，您就準備好建立 Azure Data Factory。 有數種方法可建立 Data Factory。 在本教學課程中，您會使用 Azure 入口網站部署 Azure Resource Manager 範本來建立資料處理站。 您也可以使用 [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 和 [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template) 部署 Resource Manager 範本。 如需其他 Data Factory 建立方法，請參閱 [教學課程︰建立您的第一個 Data Factory](../data-factory/quickstart-create-data-factory-dot-net.md)。

1. 按一下以下影像，在 Azure 入口網站中登入 Azure 並開啟 Resource Manager 範本。 範本是位於 https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json。 請參閱[範本中的 Data Factory 實體](#data-factory-entities-in-the-template)一節以取得範本中所定義的實體詳細資訊。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. 針對**資源群組**設定選取 [使用現有] 選項，然後選取您在上一個步驟 (使用 PowerShell 指令碼) 中建立的資源群組名稱。
3. 輸入資料處理站的名稱 (**Data Factory 名稱**)。 此名稱必須是全域唯一的。
4. 輸入您在上一個步驟中記下的**儲存體帳戶名稱**和**儲存帳戶金鑰**。
5. 閱讀**條款及條件**後，選取 [我同意上方所述的條款及條件]。
6. 選取 [釘選到儀表板] 選項。
6. 按一下 [購買/建立]。 您將會在儀表板上看到名稱為 [進行範本部署] 的圖格。 等到您資源群組的 [資源群組] 刀鋒視窗開啟。 您也可以按一下以您的資源群組名稱為標題的圖格，開啟資源群組刀鋒視窗。
6. 如果資源群組刀鋒視窗尚未開啟，按一下圖格以開啟資源群組。 除了儲存體帳戶資源，您現在應該會看到另外列出一個 Data Factory 資源。
7. 按一下您資料處理站的名稱 (您為 **Data Factory 名稱**參數所指定的值)。
8. 在 [Data Factory] 刀鋒視窗中，按一下 [圖表] 圖格。 此圖顯示一個具有輸入資料集與輸出資料集的活動︰

    ![Azure Data Factory HDInsight 隨選 Hive 活動管線圖](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    資源會在 Resource Manager 範本中定義。
9. 按兩下 [AzureBlobOutput] 。
10. 在 [最近更新的配量] 上，您應該會看到一個配量。 如果狀態為 [進行中]，請等到其變更為 [就緒]。 建立 HDInsight 叢集通常需要大約 **20 分鐘**的時間。

### <a name="check-the-data-factory-output"></a>檢查 Data Factory 輸出

1. 使用最後一個工作階段中的相同程序來檢查 adfgetstarted 容器的內容。 除了 adfgetsarted ，有兩個新容器：

   * 具有遵循模式之名稱的容器︰`adf<yourdatafactoryname>-linkedservicename-datetimestamp`。 此容器是 HDInsight 叢集的預設容器。
   * adfjobs︰這個容器是 ADF 作業記錄檔的容器。

     如同您在 Resource Manager 範本中所設定，Data Factory 的輸出會儲存在 **afgetstarted** 中。
2. 按一下 [adfgetstarted] 。
3. 按兩下 [partitioneddata] 。 您會看到 **year=2014** 資料夾，因為所有 Web 記錄檔的日期皆為 2014 年。

    ![Azure Data Factory HDInsight 隨選 Hive 活動管線輸出](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    如果您向下鑽研清單，您會看到一月、二月和三月的 3 個資料夾。 而且每個月都有記錄檔。

    ![Azure Data Factory HDInsight 隨選 Hive 活動管線輸出](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>範本中的 Data Factory 實體
用於資料處理站的最上層 Resource Manager 範本看起來如下︰

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>定義資料處理站
您可以在 Resource Manager 範本中定義資料處理站，如下列範例所示︰  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
DataFactoryName 是您在部署範本時指定的資料處理站名稱。 目前只有美國東部、美國西部和北歐地區支援 Data Factory。

### <a name="defining-entities-within-the-data-factory"></a>在資料處理站內定義實體
下列的 Data Factory 實體定義於 JSON 範本中︰

* [Azure 儲存體連結服務](#azure-storage-linked-service)
* [HDInsight 隨選連結服務](#hdinsight-on-demand-linked-service)
* [Azure Blob 輸入資料集](#azure-blob-input-dataset)
* [Azure Blob 輸出資料集](#azure-blob-output-dataset)
* [具有複製活動的管線](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure 儲存體連結服務
Azure 儲存體已連結的服務會連結 Azure 儲存體帳戶至資料處理站。 在本教學課程中，相同的儲存體帳戶會做為預設 HDInsight 儲存體帳戶、輸入資料儲存體和輸出資料儲存體。 因此，您只定義一個 Azure 儲存體連結服務。 在連結的服務定義中，您指定 Azure 儲存體帳戶的名稱和金鑰。 如需用來定義 Azure 儲存體連結服務之 JSON 屬性的詳細資料，請參閱 [Azure 儲存體連結服務](../data-factory/connector-azure-blob-storage.md)。

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString** 會使用 storageAccountName 和 storageAccountKey 參數。 您在部署範本時指定這些參數的值。  

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight 隨選連結服務
在隨選 HDInsight 連結服務定義中，您可以指定由 Data Factory 服務用來在執行階段建立 HDInsight Hadoop 叢集的組態參數值。 請參閱[計算連結服務](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service)文章，以取得關於用來定義 HDInsight 隨選連結服務之 JSON 屬性的詳細資訊。  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
請注意下列幾點：

* Data Factory 會為您建立**以 Linux 為基礎的** HDInsight 叢集。
* HDInsight Hadoop 叢集會並存於與儲存體帳戶相同的區域中。
* 請注意 timeToLive  設定。 Data Factory 會在叢集閒置 30 分鐘後自動刪除叢集。
* HDInsight 叢集會在您於 JSON 中指定的 Blob 儲存體 (**linkedServiceName**) 建立**預設容器**。 HDInsight 不會在刪除叢集時刪除此容器。 這是設計的行為。 在使用 HDInsight 隨選連結服務時，除非有現有的即時叢集 (**timeToLive**)，否則每當需要處理配量時，就會建立 HDInsight 叢集，並在處理完成時予以刪除。

如需詳細資訊，請參閱 [HDInsight 隨選連結服務](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) 。

> [!IMPORTANT]
> 隨著處理的配量越來越多，您會在 Azure Blob 儲存體中看到許多容器。 如果在疑難排解作業時不需要這些容器，建議您加以刪除以降低儲存成本。 這些容器的名稱遵循下列模式："adf**yourdatafactoryname**-**linkedservicename**-datetimestamp"。 請使用 [Microsoft 儲存體總管](http://storageexplorer.com/) 之類的工具刪除 Azure Blob 儲存體中的容器。

#### <a name="azure-blob-input-dataset"></a>Azure Blob 輸入資料集
在輸入資料集定義中，您可以指定 blob 容器、資料夾和包含輸入資料之檔案的名稱。 請參閱 [Azure Blob 資料集屬性](../data-factory/connector-azure-blob-storage.md)，以取得用來定義 Azure Blob 資料集之 JSON 屬性的詳細資訊。

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

請注意下列在 JSON 定義中的特定設定值︰

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Azure Blob 輸出資料集
在輸出資料集定義中，您可以指定 blob 容器和包含輸出資料之資料夾的名稱。 請參閱 [Azure Blob 資料集屬性](../data-factory/connector-azure-blob-storage.md)，以取得用來定義 Azure Blob 資料集之 JSON 屬性的詳細資訊。  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

FolderPath 會指定包含輸出資料的資料夾路徑︰

```json
"folderPath": "adfgetstarted/partitioneddata",
```

[資料集可用性](../data-factory/concepts-datasets-linked-services.md) 設定如下︰

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

在 Azure Data Factory 中，輸出資料集可用性會推動管線。 在此範例中，每個月會在當月的最後一天產生配量 (EndOfInterval)。 

#### <a name="data-pipeline"></a>Data Pipeline
您可以定義在隨選 Azure HDInsight 叢集上執行 Hive 指令碼以轉換資料的管線。 請參閱[管線 JSON](../data-factory/concepts-pipelines-activities.md)，以取得用來在此範例中定義管線的 JSON 元素之描述。

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

此管線包含一個活動，HDInsightHive 活動。 由於開始和結束日期都在 2016 年 1 月，因此只處理一個月的資料 (配量)。 活動的開始和結束都擁有過去的日期，因此 Data Factory 會立即處理月份的資料。 如果結束為未來日期，則 Data Factory 屆時會建立另一個配量。 如需詳細資訊，請參閱 [Data Factory 排程和執行](../data-factory/v1/data-factory-scheduling-and-execution.md)。

## <a name="clean-up-the-tutorial"></a>清除教學課程

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>刪除隨選 HDInsight 叢集所建立的 blob 容器
使用 HDInsight 隨選連結服務時，除非有現有的即時叢集 (timeToLive)，否則每當需要處理配量時，就會建立 HDInsight 叢集，並在處理完成時刪除此叢集。 對於每個叢集，Azure Data Factory 會在 Azure Blob 儲存體中建立 blob 容器以做為叢集的預設儲存體帳戶。 即使已刪除 HDInsight 叢集，但不會刪除預設 Blob 儲存體容器和相關聯的儲存體帳戶。 這是設計的行為。 隨著處理的配量越來越多，您會在 Azure Blob 儲存體中看到許多容器。 如果在疑難排解作業時不需要這些容器，建議您加以刪除以降低儲存成本。 這些容器的名稱會遵循模式︰`adfyourdatafactoryname-linkedservicename-datetimestamp`。

刪除 **adfjobs** 和 **adfyourdatafactoryname-linkedservicename-datetimestamp** 資料夾。 Adfjobs 容器包含來自 Azure Data Factory 的作業記錄。

### <a name="delete-the-resource-group"></a>刪除資源群組
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 用來以群組方式部署、管理及監視您的解決方案。  刪除資源群組將會刪除群組內的所有元件。  

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側面板上的 [資源群組]  。
3. 按一下您在 PowerShell 指令碼中建立的資源群組名稱。 如果列出太多的資源群組，請使用篩選器。 會在新的刀鋒視窗中開啟資源群組。
4. 除非您與其他專案共用資源群組，否則 [資源]  圖格應列出預設儲存體帳戶和 Data Factory。
5. 按一下刀鋒視窗頂端的 [刪除]。 這麼做會刪除儲存體帳戶和此儲存體帳戶中儲存的資料。
6. 輸入資源群組名稱以確認刪除，然後按一下刪除。

如果您不想在刪除資源群組時刪除儲存體帳戶，可以考慮以下區隔商務資料與預設儲存體帳戶的架構。 在此情況下，您會有一個資源群組用於包含商務資料的儲存體帳戶，而另一個資源群組則用於 HDInsight 連結服務的預設儲存體帳戶和 Data Factory。 當您刪除第二個資源群組時，並不會影響商務資料儲存體帳戶。 若要這樣做：

* 將下列程式碼以及 Resource Manager 範本中的 Microsoft.DataFactory/datafactories 資源加入最上層資源群組。 它會建立儲存體帳戶：

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* 將新的連結服務點加入至新的儲存體帳戶︰

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* 使用其他 dependsOn 和 additionalLinkedServiceNames 設定 HDInsight 隨選連結服務︰

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>後續步驟
在本文中，您已學會如何使用 Azure Data Factory 來建立隨選 HDInsight 叢集來處理 Hive 作業。 若要閱讀更多資訊︰

* [Hadoop 教學課程：開始在 HDInsight 中使用以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight 文件](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Data Factory 文件](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>附錄

### <a name="azure-cli-script"></a>Azure CLI 指令碼
您可以使用 Azure CLI ，而不是使用 Azure PowerShell 來執行教學課程。 若要使用 Azure CLI，先依照下列指示安裝 Azure CLI：

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>使用 Azure CLI 準備儲存體並複製檔案

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

容器名稱為 *adfgetstarted*。 讓它保持原狀。 否則，您必須更新 Resource Manager 範本。 如需有關此 CLI 指定碼的說明，請參閱 [使用 Azure CLI 搭配 Azure 儲存體](../storage/common/storage-azure-cli.md)。
