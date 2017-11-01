---
title: "在 Azure 資料處理站管線中使用自訂活動"
description: "了解如何建立自訂活動，並在 Azure 資料處理站管線中使用這些活動。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shengc
ms.openlocfilehash: e470071ca0ff45fce0a410b18ea9a91e1925af4b
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>在 Azure 資料處理站管線中使用自訂活動
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-use-custom-activities.md)
> * [第 2 版 - 預覽](transform-data-using-dotnet-custom-activity.md)

您可以在 Azure Data Factory 管線中使用兩種活動。

- [資料移動活動](copy-activity-overview.md)，可在[支援的來源與接收資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)之間移動資料。
- 使用 Azure HDInsight、Azure Batch 及 Azure Machine Learning 等計算服務來轉換資料的[資料轉換活動](transform-data.md)。 

若要將資料移入/移出 Data Factory 不支援的資料存放區，或者以 Data Factory 不支援的方式轉換/處理資料，您可以利用自己的資料移動或轉換邏輯建立**自訂活動**，然後在管線中使用活動。 自訂活動會在虛擬機器的 **Azure Batch** 集區上執行自訂程式碼邏輯。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱 [(自訂) 第 1 版的 DotNet 活動](v1/data-factory-use-custom-activities.md)。
 

如果您不熟悉 Azure Batch 服務，請參閱下列主題：

* [Azure Batch 基本知識](../batch/batch-technical-overview.md) ，以取得 Azure Batch 服務的概觀。
* [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) Cmdlet 可建立 Azure Batch 帳戶 (或) [Azure 入口網站](../batch/batch-account-create-portal.md)，以使用 Azure 入口網站建立 Azure Batch 帳戶。 如需使用此 Cmdlet 的詳細指示，請參閱 [使用 PowerShell 管理 Azure Batch 帳戶](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) 主題。
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) Cmdlet 可建立 Azure Batch 集區。

## <a name="azure-batch-linked-service"></a>Azure Batch 已連結的服務 
下列 JSON 會定義範例 Azure Batch 已連結的服務。 如需詳細資訊，請參閱 [Azure Data Factory 支援的計算環境](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 若要深入了解 Azure Batch 已連結的服務，請參閱[計算已連結的服務](compute-linked-services.md)一文。 

## <a name="custom-activity"></a>自訂活動

下列 JSON 片段會定義具有範例自訂活動的管線。 活動定義具有對 Azure Batch 已連結的服務之參考。 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

在此範例中，helloworld.exe 是自訂應用程式，儲存在 resourceLinkedService 中使用之 Azure 儲存體帳戶的 customactv2/helloworld 資料夾。 自訂活動會提交此自訂應用程式以在 Azure Batch 上執行。 您可以取代在 Azure Batch 集區節點之目標作業系統上執行之任何偏好應用程式的命令。 

下表描述此活動特有的屬性之名稱和描述。 

| 屬性              | 說明                              | 必要 |
| :-------------------- | :--------------------------------------- | :------- |
| 名稱                  | 管線中的活動名稱     | 是      |
| 說明           | 說明活動用途的文字。  | 否       |
| 類型                  | 針對自訂活動，活動類型是**自訂**。 | 是      |
| linkedServiceName     | Azure Batch 的已連結的服務。 若要深入了解此已連結的服務，請參閱[計算已連結的服務](compute-linked-services.md)一文。  | 是      |
| 命令               | 要執行的自訂應用程式命令。 如果應用程式已經可以在 Azure Batch 集區節點上使用，則可以略過 resourceLinkedService 和 folderPath。 例如，您可以將命令指定為 `cmd /c dir`，該命令原生受 Windows Batch 集區節點支援。 | 是      |
| resourceLinkedService | 對儲存體帳戶 (自訂應用程式儲存所在) 的 Azure 儲存體已連結的服務 | 否       |
| folderPath            | 自訂應用程式及其所有相依項目的資料夾路徑 | 否       |
| referenceObjects      | 現有已連結的服務和資料集的陣列。 參考的已連結的服務和資料集會傳遞至 JSON 格式的自訂應用程式，讓您的自訂程式碼可以參考 Data Factory 的資源 | 否       |
| extendedProperties    | 使用者定義的屬性，可以傳遞至 JSON 格式的自訂應用程式，讓您的自訂程式碼可以參考其他屬性 | 否       |

## <a name="executing-commands"></a>執行命令

您可以使用自訂活動直接執行命令。 在以下範例中，我們在目標 Azure Batch 集區的節點上執行 "echo hello world" 命令，並將輸出列印至 stdout。 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>傳遞物件和屬性

這個範例會示範如何使用 referenceObjects 和 extendedProperties 將 Data Factory 物件和使用者定義屬性傳遞至自訂應用程式。 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

當活動執行時，referenceObjects 和 extendedProperties 會儲存在部署至與 SampleApp.exe 相同執行資料夾的下列檔案： 

- activity.json

  儲存 extendedProperties 和自訂活動的屬性。 

- linkedServices.json

  儲存 referenceObjects 屬性中定義之已連結的服務的陣列。 

- datasets.json

  儲存 referenceObjects 屬性中定義之資料集的陣列。 

下列範例程式碼示範 SampleApp.exe 如何從 JSON 檔案存取必要的資訊： 

```C#
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>擷取執行輸出

  您可以使用下列 PowerShell 命令啟動管線的執行： 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  管線正在執行時，您可以使用下列命令檢查執行的輸出： 

  ```.powershell
  while ($True) {
      $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

      if(!$result) {
          Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
      }
      elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
          Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
      }
      else {
          Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
          $result
          break
      }
      ($result | Format-List | Out-String)
      Start-Sleep -Seconds 15
  }

  Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
  $result.Output -join "`r`n"

  Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
  $result.Error -join "`r`n"
  ```

  您的自訂應用程式的 **stdout** 和 **stderr** 會儲存至您在使用工作的 GUID 建立 Azure Batch 已連結的服務時，定義的 Azure 儲存體已連結的服務中的 **adfjobs** 容器。 您可以從活動執行輸出取得詳細路徑，如下列程式碼片段所示： 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
如果您想要在下游活動中取用 stdout.txt 的內容，可以在 "@activity('MyCustomActivity').output.outputs[0]" 運算式中取得 stdout.txt 檔案的路徑。 

  > [!IMPORTANT]
  > - activity.json、linkedServices.json 和 datasets.json 會儲存在 Bath 工作的執行階段資料夾。 針對此範例，activity.json、linkedServices.json 和 datasets.json 會儲存在 "https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/" 路徑。 您必須視需要個別加以清除。 
  > - 針對已連結的服務使用自我裝載整合執行階段，機密資訊 (例如金鑰或密碼) 就會由自我裝載整合執行階段加密，以確保認證保留在客戶定義的私人網路環境中。 某些機密欄位由您的自訂應用程式以這樣的方式參考時，可能會遺失。 視需要在 extendedProperties 中使用 SecureString，而不是使用已連結的服務參考。 

## <a name="difference-between-custom-activity-in-azure-data-factory-v2-and-custom-dotnet-activity-in-azure-data-factory-v1"></a>Azure Data Factory V2 中的自訂活動和 (自訂) Azure Data Factory V1 中的 DotNet 活動之間的差異 

  在 Azure Data Factory V1 中，您必須實作 (自訂) DotNet 活動的程式碼，做法是建立一個 .NET 類別庫專案，其中有會實作 IDotNetActivity 介面 Execute 方法的類別。 (自訂) DotNet 活動 JSON 承載中的已連結的服務、資料集、擴充的屬性，會以強型別物件傳遞至執行方法。 如需詳細資訊，請參閱 [(自訂) 第 1 版的 DotNet](v1/data-factory-use-custom-activities.md)。 因此，您的自訂程式碼需要以 .Net Framework 4.5.2 撰寫，並在以 Windows 為基礎的 Azure Batch 集區節點上執行。 

  在 Azure Data Factory V2 自訂活動中，您不需要實作 .Net 介面。 您現在可以直接執行命令、指令碼，以及執行您自己的自訂程式碼 (已編譯為可執行檔)。 做法是一併指定 Command 屬性與 folderPath 屬性。 自訂活動會在 folderpath 中上傳可執行檔和相依性，並為您執行命令。 

  自訂活動 JSON 承載中定義的已連結的服務、資料集 (在 referenceObjects 中定義)、擴充的屬性，可以 JSON 檔案的形式由您的可執行檔存取。 您可以使用 JSON 序列化程式存取所需的屬性，如之前的 SampleApp.exe 程式碼範例中所示。 

  由於 Azure Data Factory V2 自訂活動中新引進的改變，您可以使用您慣用的語言自由撰寫自訂程式碼邏輯，然後在 Azure Batch 支援的 Windows 和 Linux 作業系統上執行。 

  如果您的現有 .Net 程式碼是針對 V1 (自訂) DotNet 活動撰寫，您需要依據下列的高階指導方針修改程式碼，才能將其用於 V2 自訂活動：  

  > - 將專案由 .Net 類別庫變更為主控台應用程式。 
  > - 以 Main 方法啟動應用程式，不必再使用 IDotNetActivity 介面的 Execute 方法。 
  > - 以 JSON 序列化程式讀取和剖析已連結的服務、資料集和活動 (而不是強型別物件)，並將所需屬性的值傳遞給您的主要自訂程式碼邏輯。 請參閱之前的 SampleApp.exe 程式碼作為範例。 
  > - 不再支援記錄器物件，可執行檔的輸出可以列印到主控台，並儲存到 stdout.txt。 
  > - 不再需要 Microsoft.Azure.Management.DataFactories NuGet 套件。 
  > - 編譯您的程式碼、將可執行檔和相依性上傳至 Azure 儲存體，並在 folderPath 屬性中定義路徑。 

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch 的自動調整
您也可以建立具有 **自動調整** 功能的 Azure Batch 集區。 例如，您可以用 0 專用 VM 和依據暫止工作數目自動調整的公式，建立 Azure Batch 集區。 

這裡的範例公式會有下列行為：當集區一開始建立時，它會以 1 部 VM 啟動。 $PendingTasks 計量會定義執行中 + 作用中 (已排入佇列) 狀態的工作數目。  公式會尋找過去 180 秒內的平均擱置中工作數目，並據以設定 TargetDedicated。 它會確保 TargetDedicated 一律不會超過 25 部 VM。 因此，當提交新工作時，集區會自動成長而且工作會完成，VM 會依序成為可用，而且自動調整規模功能會壓縮那些 VM。 您可以視需要調整 startingNumberOfVMs 及 maxNumberofVMs。

自動調整公式：

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

如需詳細資訊，請參閱 [自動調整 Azure Batch 集區中的計算節點](../batch/batch-automatic-scaling.md) 。

如果集區使用預設的 [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)，Batch 服務在執行自訂活動之前，可能需要 15-30 分鐘的時間準備 VM。  如果集區使用不同的 autoScaleEvaluationInterval，Batch 服務可能需要 autoScaleEvaluationInterval + 10 分鐘。


## <a name="next-steps"></a>後續步驟
請參閱下列文章，其說明如何以其他方式轉換資料： 

* [U-SQL 活動](transform-data-using-data-lake-analytics.md)
* [Hive 活動](transform-data-using-hadoop-hive.md)
* [Pig 活動](transform-data-using-hadoop-pig.md)
* [MapReduce 活動](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 串流活動](transform-data-using-hadoop-streaming.md)
* [Spark 活動](transform-data-using-spark.md)
* [Machine Learning 批次執行活動](transform-data-using-machine-learning.md)
* [預存程序活動](transform-data-using-stored-procedure.md)
