---
title: "在 HDInsight 中搭配使用 Hadoop Hive 與 PowerShell - Azure | Microsoft Docs"
description: "使用 PowerShell 在 HDInsight 的 Hadoop 中執行 Hive 查詢"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/16/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: e1cb2e4a1fc82fb43082e79a5feba71b81b3eaa8
ms.contentlocale: zh-tw
ms.lasthandoff: 07/28/2017

---
# <a name="run-hive-queries-using-powershell"></a>使用 PowerShell 執行 Hive 查詢
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

本文件提供在 Azure 資源群組模式中使用 Azure PowerShell 的範例，以便在 HDInsight 叢集的 Hadoop 中執行 Hive 查詢。

> [!NOTE]
> 本文件不提供範例中使用的 HiveQL 陳述式所執行的工作詳細的描述。 如需此範例中使用的 HiveQL 的相關資訊，請參閱 [在 HDInsight 上搭配 Hadoop 使用 Hive](hdinsight-use-hive.md)。

**必要條件**

* **Azure HDInsight 叢集**︰不論叢集是 Windows 型或 Linux 型。

  > [!IMPORTANT]
  > Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* **具有 Azure PowerShell 的工作站**。

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-hive-queries-using-azure-powershell"></a>使用 Azure PowerShell 執行 Hive 查詢

Azure PowerShell 提供 *Cmdlet* ，可讓您從遠端在 HDInsight 上執行 Hive 查詢。 就內部而言，Cmdlet 會對 HDInsight 叢集上的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) 進行 REST 呼叫。

在遠端 HDInsight 叢集中執行 Hive 查詢時，會使用下列 Cmdlet：

* **Add-AzureRmAccount**：向您的 Azure 訂用帳戶驗證 Azure PowerShell
* **New-AzureRmHDInsightHiveJobDefinition**：使用指定的 HiveQL 陳述式建立「作業定義」
* **Start-AzureRmHDInsightJob**：將工作定義傳送至 HDInsight、啟動工作，然後傳回可用來檢查工作狀態的 *工作* 物件
* **Wait-AzureRmHDInsightJob**：使用工作物件來檢查工作的狀態。 它會等到工作完成，或等到等候時間超過。
* **Get-AzureRmHDInsightJobOutput**：用來擷取工作的輸出
* **Invoke-AzureRmHDInsightHiveJob**：用來執行 HiveQL 陳述式。 這個 Cmdlet 會阻止查詢完成，然後傳回結果
* **Use-AzureRmHDInsightCluster**：設定要用於 **Invoke-AzureRmHDInsightHiveJob** 命令的目前的叢集

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集中執行工作：

1. 使用編輯器，將下列程式碼儲存為 **hivejob.ps1**。

    [!code-powershell[主要](../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. 開啟新的 **Azure PowerShell** 命令提示字元。 將目錄變更至 **hivejob.ps1** 檔案的位置，然後使用下列命令來執行指令碼：

        .\hivejob.ps1

    執行指令碼時，系統會提示您輸入叢集名稱和該叢集的 HTTPS/管理帳戶認證。 系統可能也會提示您登入 Azure 訂用帳戶。

3. 作業完成時，應該會傳回類似下列文字的資訊：

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 如前所述， **Invoke-Hive** 可以用來執行查詢，並等候回應。 使用下列指令碼，查看 Invoke-Hive 如何運作：

    [!code-powershell[主要](../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    輸出看起來會像下列文字：

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > 如果 HiveQL 查詢的時間很長，您可以使用 Azure PowerShell **Here-Strings** Cmdlet 或 HiveQL 指令碼檔案。 下列程式碼片段說明如何使用 **Invoke-Hive** Cmdlet 來執行 HiveQL 指令碼檔案。 您必須將 HiveQL 指令碼檔案上傳至 wasb://。
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > 如需 **Here-Strings** 的詳細資訊，請參閱<a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">使用 Windows PowerShell Here-Strings</a>。

## <a name="troubleshooting"></a>疑難排解

如果在工作完成時未傳回任何資訊，則可能是處理期間發生錯誤。 若要檢視這項工作的錯誤資訊，請將下列內容新增至 **hivejob.ps1** 檔案的結尾，並儲存它，然後重新予以執行。

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

這個 Cmdlet 會傳回執行作業時寫入伺服器上之 STDERR 的資訊。

## <a name="summary"></a>摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集中執行 Hive 查詢、監視工作狀態，以及擷取輸出。

## <a name="next-steps"></a>後續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)
* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

