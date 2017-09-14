---
title: "使用 Azure PowerShell 管理 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何管理 Data Lake Analytics 帳戶、資料來源、作業及目錄項目。 "
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 65bf5928428b21e98c893a9de8ca596329329411
ms.contentlocale: zh-tw
ms.lasthandoff: 09/05/2017

---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure PowerShell 來管理 Azure Data Lake Analytics 帳戶、資料來源、作業及目錄項目。 

## <a name="prerequisites"></a>必要條件

建立 Data Lake Analytics 帳戶時，您必須知道：

* **訂用帳戶 ID**：您 Data Lake Analytics 帳戶所在的 Azure 訂用帳戶 ID。
* **資源群組**：包含您 Data Lake Analytics 帳戶的 Azure 資源群組名稱。
* **Data Lake Analytics 帳戶名稱**：此帳戶名稱只能包含小寫字母和數字。
* **預設 Data Lake Store 帳戶**：每個 Data Lake Analytics 帳戶都有一個預設的 Data Lake Store 帳戶。 這些帳戶必須位於相同的位置。
* **位置**：您 Data Lake Analytics 帳戶的位置，例如「美國東部 2」或其他支援的位置。 您可以在我們的[價格頁面](https://azure.microsoft.com/pricing/details/data-lake-analytics/)上看到支援的位置。

本教學課程中的 PowerShell 程式碼片段會使用這些變數來儲存此資訊

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>登入

使用訂用帳戶 ID 來登入。

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

使用訂用帳戶名稱來登入。

```
Login-AzureRmAccount -SubscriptionName $subname 
```

`Login-AzureRmAccount` Cmdlet 一律會提示輸入認證。 您可以使用下列 Cmdlet 來避免出現提示：

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="managing-accounts"></a>管理帳戶

### <a name="create-a-data-lake-analytics-account"></a>建立 Data Lake Analytics 帳戶

如果您還沒有可使用的[資源群組](../azure-resource-manager/resource-group-overview.md#resource-groups)，請建立一個。 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

每個 Data Lake Analytics 帳戶都需要預設 Data Lake Store 帳戶，用於儲存記錄。 您可以重複使用現有的帳戶，或建立一個帳戶。 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

資源群組和 Data Lake Store 帳戶一旦可用，請建立 Data Lake Analytics 帳戶。

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>取得帳戶的相關資訊

取得帳戶的相關詳細資料。

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

檢查特定的 Data Lake Analytics 帳戶是否存在。 此 Cmdlet 會傳回 `True` 或 `False`。

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

檢查特定的 Data Lake Store account 帳戶是否存在。 此 Cmdlet 會傳回 `True` 或 `False`。

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="listing-accounts"></a>列出帳戶

列出目前訂用帳戶內的 Data Lake Analytics 帳戶。

```powershell
Get-AdlAnalyticsAccount
```

列出特定資源群組內的 Data Lake Analytics 帳戶。

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>管理防火牆規則

列出防火牆規則。

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

新增防火牆規則。

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

變更防火牆規則。

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

移除防火牆規則。

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```



允許 Azure IP 位址。

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>管理資料來源
Azure Data Lake Analytics 目前支援下列資料來源：

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure 儲存體](../storage/common/storage-introduction.md)

當您建立 Analytics 帳戶時，必須指定一個 Data Lake Store 帳戶作為預設的資料來源。 預設的 Data Lake Store 帳戶是用來儲存工作中繼資料與工作稽核記錄。 建立 Data Lake Analytics 帳戶之後，您可以新增其他 Data Lake Store 帳戶和/或儲存體帳戶。 

### <a name="find-the-default-data-lake-store-account"></a>尋找預設的 Data Lake Store 帳戶

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

您可以用 `IsDefault` 屬性篩選資料來源清單，藉此方式尋找預設的 Data Lake Store 帳戶：

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>建立資料來源

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>列出資料來源

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>提交 U-SQL 作業

### <a name="submit-a-string-as-a-u-sql-script"></a>以 U-SQL 指令碼形式提交字串

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```


### <a name="submit-a-file-as-a-u-sql-script"></a>以 U-SQL 指令碼形式提交檔案

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>列出帳戶中的作業

### <a name="list-all-the-jobs-in-the-account"></a>列出帳戶中的所有作業。 

輸出包含目前執行中作業和最近完成的作業。

```powershell
Get-AdlJob -Account $adla
```


### <a name="list-a-specific-number-of-jobs"></a>列出特定數目的作業

預設會在提交時排序作業清單。 因此，最新提交的作業會顯示在最前面。 ADLA 帳戶預設會記住 180 天內的作業，但 Ge-AdlJob Cmdlet 預設只會傳回前 500 個作業。 請使用 -Top 參數來列出特定數目的作業。

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```


### <a name="list-jobs-based-on-the-value-of-job-property"></a>根據作業屬性的值列出作業

使用 `-State` 參數。 您可以結合以下這些值：

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

使用 `-Result` 參數來偵測已結束的工作是否順利完成。 它有下列值：

* Cancelled
* Failed
* None
* Succeeded

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```


`-Submitter` 參數可協助您識別由誰提交工作。

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

`-SubmittedAfter` 用於篩選時間範圍。


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>分析作業記錄

使用 Azure PowerShell 來分析已在 Data Lake 分析中執行之作業的記錄，是一項強大的技術。 您可以使用這項技術來深入了解使用情形和成本。 若要深入了解，請參閱[作業記錄分析存放庫範例](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>取得管線和週期的相關資訊

使用 `Get-AdlJobPipeline` Cmdlet 來查看先前提交作業的管線資訊。

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla

$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

使用 `Get-AdlJobRecurrence` Cmdlet 來查看先前提交作業的週期資訊。

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>取得作業的相關資訊

### <a name="get-job-status"></a>取得作業狀態

取得特定作業的狀態。

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>檢查作業輸出

在作業結束之後，請列出資料夾中的檔案，來檢查輸出檔案是否存在。

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>管理執行中的作業

### <a name="cancel-a-job"></a>取消工作

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>等候作業結束

您可以不重複執行 `Get-AdlAnalyticsJob` 直到作業結束，而是使用 `Wait-AdlJob` Cmdlet 來等候作業結束。

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>管理計算原則

### <a name="list-existing-compute-policies"></a>列出現有的計算原則

`Get-AdlAnalyticsComputePolicy` Cmdlet 會擷取 Data Lake Analytics 帳戶的計算原則清單。

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>建立計算原則

`New-AdlAnalyticsComputePolicy` Cmdlet 會為 Data Lake Analytics 帳戶建立新的計算原則。 這個範例會將指定使用者的可用 AU 上限設定為 50，將最低作業優先權設為 250。

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>檢查檔案是否存在。

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>上傳和下載

上傳檔案。

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

以遞迴方式上傳整個資料夾。

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

下載檔案。

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

以遞迴方式下載整個資料夾。

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> 如果上傳或下載程序中斷，您可以搭配 ``-Resume`` 旗標來重新執行該 Cmdlet，以嘗試繼續該程序。

## <a name="manage-catalog-items"></a>管理目錄項目

U-SQL 目錄是用來建構資料和程式碼，讓 U-SQL 指令碼可以共用它們。 目錄可以讓 Azure Data Lake 中的資料具有可能的最高效能。 如需詳細資訊，請參閱 [使用 U-SQL 目錄](data-lake-analytics-use-u-sql-catalog.md)。

### <a name="list-items-in-the-u-sql-catalog"></a>列出 U-SQL 目錄中的項目

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

列出 ADLA 帳戶中所有資料庫的所有組件。

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>取得目錄項目的相關詳細資料

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>在目錄中建立認證

在 U-SQL 資料庫內，為裝載於 Azure 中的資料庫建立認證物件。 目前，U-SQL 認證是您可以透過 PowerShell 建立的唯一目錄項目類型。

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

### <a name="get-basic-information-about-an-adla-account"></a>取得 ADLA 帳戶的相關基本資訊

只要指定帳戶名稱，下列程式碼就會查詢該帳戶的相關基本資訊

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>使用 Azure

### <a name="get-details-of-azurerm-errors"></a>取得 AzureRm 錯誤詳細資料

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>確認您是否是以系統管理員身分執行

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>尋找 TenantID

從訂用帳戶名稱：

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

從訂用帳戶 ID：

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

從網域位址 (例如 "contoso.com")


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>列出您的所有訂用帳戶和租用戶識別碼

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>使用範本建立 Data Lake Analytics 帳戶

您也可以運用下列 PowerShell 指令碼來使用「Azure 資源群組」範本：

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

如需詳細資訊，請參閱[使用 Azure Resource Manager 範本來部署應用程式](../azure-resource-manager/resource-group-template-deploy.md)和[編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

**範例範本**

請將下列文字儲存成 `.json` 檔案，然後運用上面的 PowerShell 指令碼來使用此範本。 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>後續步驟
* [Microsoft Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* 運用 [Azure 入口網站](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md) 來開始使用 Data Lake Analytics
* 運用 [Azure 入口網站](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 來管理 Azure Data Lake Analytics 

