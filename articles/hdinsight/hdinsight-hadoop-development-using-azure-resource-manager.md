---
title: "移轉至 HDInsight 的 Azure Resource Manager 工具 | Microsoft Docs"
description: "如何移轉至 HDInsight 叢集的 Azure Resource Manager 開發工具"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: 
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 9fec253fbf9136cf86a84a6c7b407ea522996226
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>移轉至以 Azure Resource Manager 為基礎的開發工具 (適用於 HDInsight 叢集)

HDInsight 正在取代以 Azure Service Manager (ASM) 為基礎的工具 (適用於 HDInsight)。 如果您已搭配使用 Azure PowerShell、Azure CLI 或 HDInsight.NET SDK 與 HDInsight 叢集，建議您可以使用以 Azure Resource Manager (ARM) 為基礎的 PowerShell、CLI 和.NET SDK 版本。 本文章提供有關如何移轉至以 ARM 為基礎的新方法指標。 只要適用，本文也將點出 HDInsight ASM 和 ARM 之間在方法上的差異。

> [!IMPORTANT]
> 對以 ASM 為基礎的 PowerShell CLI 和.NET SDK 的支援將於 **2017 年 1 月 1 日**中止。
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>將 Azure CLI 移轉至 Azure Resource Manager
現在 Azure CLI 是預設為 Azure Resource Manager (ARM) 模式，除非您是自先前的安裝版本升級；在此情況下，您可能需要使用 `azure config mode arm` 命令以切換到 ARM 模式。

Azure CLI 提供用來搭配使用 Azure Service Management (ASM) 與 HDInsight 的基本命令，和使用 ARM 時相同；不過某些參數和切換參數可能會有新的名稱，而且當使用 ARM 時，會有許多新的參數可供使用。 例如，您現在可以使用 `azure hdinsight cluster create` 來指定建立叢集所在的 Azure 虛擬網路，或是 Hive 和 Oozie 的中繼存放區資訊。

透過 Azure Resource Manager 來使用 HDInsight 的基本命令為︰

* `azure hdinsight cluster create` - 建立新的 HDInsight 叢集
* `azure hdinsight cluster delete` - 刪除現有的 HDInsight 叢集
* `azure hdinsight cluster show` - 顯示現有叢集的相關資訊
* `azure hdinsight cluster list` - 列出適用於您 Azure 訂用帳戶的 HDInsight 叢集

使用 `-h` 切換參數來檢查每個命令可用的參數和切換參數。

### <a name="new-commands"></a>新的命令
可用於 Azure Resource Manager 的新命令︰

* `azure hdinsight cluster resize` - 在叢集中動態變更背景工作節點數目
* `azure hdinsight cluster enable-http-access` - 啟用 HTTP 對叢集的存取 (預設為開啟)
* `azure hdinsight cluster disable-http-access` - 停用 HTTP 對叢集的存取
* `azure hdinsight script-action` - 在叢集上提供建立/管理指令碼動作的命令
* `azure hdinsight config` -提供可用於建立組態檔的命令，該組態檔可與 `hdinsight cluster create` 命令一起使用，以提供組態資訊。

### <a name="deprecated-commands"></a>已取代的命令
如果您使用 `azure hdinsight job` 命令將工作提交至 HDInsight 叢集，便無法透過 ARM 的命令來使用。 如果您需要以程式設計方式，自指令碼將工作提交至 HDInsight，您應該改用 HDInsight 所提供的 REST API。 如需有關如何使用 REST API 提交工作的詳細資訊，請參閱下列文件。

* [使用 Curl 搭配執行 MapReduce 工作與 HDInsight 上的 Hadoop](hdinsight-hadoop-use-mapreduce-curl.md)
* [使用 Curl 搭配執行 Hive 查詢 與 HDInsight 上的 Hadoop](hdinsight-hadoop-use-hive-curl.md)
* [使用 Curl 搭配執行 Pig 工作與 HDInsight 上的 Hadoop](hdinsight-hadoop-use-pig-curl.md)

如需有關執行 MapReduce、Hive 和 Pig 互動方式的其他方法，請參閱[搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)、[搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md) 和[搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)。

### <a name="examples"></a>範例
**建立叢集**

* 舊的命令 (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* 新的命令 (ARM) - `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**刪除叢集**

* 舊的命令 (ASM) - `azure hdinsight cluster delete myhdicluster`
* 新的命令 (ARM) - `azure hdinsight cluster delete mycluster -g myresourcegroup`

**列出叢集**

* 舊的命令 (ASM) - `azure hdinsight cluster list`
* 新的命令 (ARM) - `azure hdinsight cluster list`

> [!NOTE]
> 針對清單命令，當使用 `-g` 來指定資源群組，將只會傳回在指定資源群組中的叢集。
> 
> 

**顯示叢集資訊**

* 舊的命令 (ASM) - `azure hdinsight cluster show myhdicluster`
* 新的命令 (ARM) - `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>將 Azure PowerShell 移轉至 Azure Resource Manager
有關 Azure PowerShell 在 Azure Resource Manager (ARM) 模式中的一般資訊，請參閱 [搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。

Azure PowerShell ARM Cmdlet 可與 ASM Cmdlet 並存安裝。 來自兩種模式的 Cmdlet 可依其名稱來區分。  ARM 模式的 Cmdlet 名稱中有「AzureRmHDInsight」，對比 ASM 模式中的「AzureHDInsight」。  例如，「New-AzureRmHDInsightCluster」對比「New-AzureHDInsightCluster」。 某些參數和切換參數可能會有新的名稱，而且當使用 ARM 時，會有許多新的參數可供使用。  例如，數個 Cmdlet 需要名為 -ResourceGroupName 的新切換參數。 

在您可以使用 HDInsight Cmdlet 之前，必須連線到您的 Azure 帳戶，並建立新的資源群組︰

* Login-AzureRmAccount 或 [Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx)。 請參閱 [使用 Azure Resource Manager 驗證服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>重新命名 Cmdlet
若要在 Windows PowerShell 主控台中列出 HDInsight ASM Cmdlet：

    help *azurermhdinsight*

下表列出 ASM Cmdlet 和其在 ARM 模式中的名稱︰

| ASM cmdlets | ARM cmdlets |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>新的 Cmdlet
下列是只在 ARM 模式中使用的新 Cmdlet。 

**指令碼動作相關的 Cmdlet：**

* **Get-AzureRmHDInsightPersistedScriptAction**︰取得叢集的持續性指令碼動作，並依時間先後順序列出，或取得有關指定持續性指令碼動作的詳細資料。 
* **Get AzureRmHDInsightScriptActionHistory**︰ 取得叢集的指令碼動作記錄，並依反向的時間先後順序列出，或取得有關先前執行指令碼動作的詳細資料。 
* **Remove-AzureRmHDInsightPersistedScriptAction**︰自 HDInsight 叢集移除持續性指令碼動作。
* **Set-AzureRmHDInsightPersistedScriptAction**︰將先前執行的指令碼動作設定為持續性指令碼動作。
* **Submit-AzureRmHDInsightScriptAction**︰將新的指令碼動作提交至 Azure HDInsight 叢集。 

如需關於其他使用方式的詳細資訊，請參閱 [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

**叢集身分識別相關的 Cmdlet：**

* **Add-AzureRmHDInsightClusterIdentity**︰將叢集身分識別新增至叢集組態物件，以便讓 HDInsight 叢集可以存取 Azure Data Lake 存放區。 請參閱 [使用 Azure PowerShell 建立具 Data Lake Store 的 HDInsight 叢集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)。

### <a name="examples"></a>範例
**建立叢集**

舊的命令 (ASM)： 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

新的命令 (ARM)：

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**刪除叢集**

舊的命令 (ASM)：

    Remove-AzureHDInsightCluster -name $clusterName 

新的命令 (ARM)：

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**列出叢集**

舊的命令 (ASM)：

    Get-AzureHDInsightCluster

新的命令 (ARM)：

    Get-AzureRmHDInsightCluster 

**顯示叢集**

舊的命令 (ASM)：

    Get-AzureHDInsightCluster -Name $clusterName

新的命令 (ARM)：

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>其他範例
* [建立 HDInsight 叢集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [提交 Hive 工作](hdinsight-hadoop-use-hive-powershell.md)
* [提交 Pig 工作](hdinsight-hadoop-use-pig-powershell.md)
* [提交 Sqoop 工作](hdinsight-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-arm-based-hdinsight-net-sdk"></a>移轉至以 ARM 為基礎的 HDInsight .NET SDK
以 Azure Service Management (ASM) 為基礎的 [HDInsight.NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) 現在已被取代。 建議您使用以 Azure Resource Management (ARM) 為基礎的 [HDInsight.NET SDK](https://msdn.microsoft.com/library/azure/mt271028.aspx)。 下列以 ASM 為基礎的 HDInsight 封裝會被取代。

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

本節提供有關如何使用以 ARM 為基礎的 SDK 來執行特定工作的詳細資訊指標。

| 如何...使用以 ARM 為基礎的 HDInsight SDK | 連結 |
| --- | --- |
| 使用 .NET SDK 建立 HDInsight 叢集 |請參閱 [使用.NET SDK 來建立 HDInsight 叢集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| 搭配使用指令碼動作與 .NET SDK 來自訂叢集 |請參閱 [使用指令碼動作來自訂 HDInsight Linux 叢集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| 搭配使用 Azure Active Directory 與 .NET SDK，以互動方式驗證應用程式 |請參閱 [使用 .NET SDK 執行 Hive 查詢](hdinsight-hadoop-use-hive-dotnet-sdk.md)。 在本文中的程式碼片段會使用互動式驗證方法。 |
| 搭配使用 Azure Active Directory 與 .NET SDK，以非互動方式驗證應用程式 |請參閱 [建立 HDInsight 的非互動式應用程式](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| 使用 .NET SDK 提交 Hive 作業 |請參閱 [提交 Hive 工作](hdinsight-hadoop-use-hive-dotnet-sdk.md) |
| 使用 .NET SDK 提交 Pig 作業 |請參閱 [提交 Pig 工作](hdinsight-hadoop-use-pig-dotnet-sdk.md) |
| 使用 .NET SDK 提交 Sqoop 作業 |請參閱 [提交 Sqoop 工作](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |
| 使用 .NET SDK 列出 HDInsight 叢集 |請參閱 [列出 HDInsight 叢集](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| 使用.NET SDK 調整 HDInsight 叢集 |請參閱 [調整 HDInsight 叢集](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| 使用.NET SDK 授與/撤銷存取至 HDInsight 叢集 |請參閱 [「授與/撤銷存取至 HDInsight 叢集」](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| 使用 .NET SDK 更新 HDInsight 叢集的 HTTP 使用者認證 |請參閱 [「更新 HDInsight 叢集的 HTTP 使用者認證」](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| 尋找使用.NET SDK 的 HDInsight 叢集預設儲存體帳戶 |請參閱 [「尋找 HDInsight 叢集預設的儲存體帳戶」](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| 使用 .NET SDK 刪除 HDInsight 叢集 |請參閱 [「刪除 HDInsight 叢集」](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>範例
下列是關於如何使用以 ASM 為基礎的 SDK 和以 ARM 為基礎的 SDK 的對等程式碼片段，來執行作業的一些範例。

**建立叢集 CRUD 用戶端**

* 舊的命令 (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager (ARM)
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* 新的命令 (ARM) (服務主體的授權)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* 新的命令 (ARM) (使用者的授權)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**建立叢集**

* 舊的命令 (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* 新的命令 (ARM)
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**啟用 HTTP 存取**

* 舊的命令 (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* 新的命令 (ARM)
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**刪除叢集**

* 舊的命令 (ASM)
  
        client.DeleteCluster(dnsName);
* 新的命令 (ARM)
  
        client.Clusters.Delete(resourceGroup, dnsname);

