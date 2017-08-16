---
title: "Hadoop 元件和版本 - Azure HDInsight | Microsoft Docs"
description: "了解 HDInsight 中的 Hadoop 元件和版本，以及此 Hortonworks Data Platform 雲端發佈的可用服務層級。"
keywords: "hadoop 版本, hadoop 生態系統元件, hadoop 元件, 如何檢查 hadoop 版本"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: bprakash
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 26512049dba3d4dde575c5a67ba884fb56374236
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>可以搭配 HDInsight 使用的 Hadoop 元件和版本有哪些？

了解 Microsoft Azure HDInsight 中的 Apache Hadoop 生態系統元件和版本，以及標準和進階服務層級。 此外，了解如何檢查 HDInsight 中的 Hadoop 元件版本。 

每個 HDInsight 版本都是 Hortonworks Data Platform (HDP) 版本的雲端發佈。

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>可以搭配不同 HDInsight 版本使用的 Hadoop 元件
Azure HDInsight 支援多個可隨時部署的 Hadoop 叢集版本。 每一個版本選擇都會建立特定版本的 HDP 發佈，以及該發佈內包含的一組元件。 截至 2017 年 2 月 17 日為止，Azure HDInsight 預設使用的叢集版本為 3.5 版，且以 HDP 2.5 為基礎。

下表列出與 HDInsight 叢集版本相關聯的元件版本。 

> [!NOTE]
> HDInsight 服務的預設版本可能會變更，恕不另行通知。 如果您有版本相依性，請在使用 .NET SDK 搭配 Azure PowerShell 和 Azure CLI 建立叢集時指定 HDInsight 版本。

| 元件 | HDInsight 3.6 (預設值) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop 和 YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive 和 HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase (英文) |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (僅限 Linux) |1.6.2 + 2.0 (僅限 Linux) |1.6.0 (僅限 Linux) |1.5.2 (僅限 Linux 實驗性組建) |1.3.1 (僅限 Windows) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>檢查目前的 Hadoop 元件版本資訊

在 HDInsight 的更新中，可能會變更與 HDInsight 叢集版本相關聯的 Hadoop 生態系統元件版本。 若要檢查 Hadoop 元件和驗證叢集所使用的版本，請使用 Ambari REST API。 **GetComponentInformation** 命令會擷取服務元件的相關資訊。 如需詳細資訊，請參閱 [Ambari 文件][ambari-docs] (英文)。

針對 Windows 叢集，另一種取得元件版本的方式是使用遠端桌面登入叢集，然後檢查 C:\apps\dist\ 目錄的內容。

> [!IMPORTANT]
> Linux 是 HDInsight 版本 3.4 或更新版本上唯一使用的作業系統。 如需詳細資訊，請參閱 [HDInsight 上的 Windows 停用項目](#hdinsight-windows-retirement)。

### <a name="release-notes"></a>版本資訊

請參閱 [HDInsight 版本資訊](hdinsight-release-notes.md) ，以取得 HDInsight 最新版本的其他版本資訊。

## <a name="supported-hdinsight-versions"></a>支援的 HDInsight 辦本
下表列出目前可在 Azure 入口網站上取得的 HDInsight 版本。 其中會列出對應至每個 HDInsight 版本的 HDP 版本以及產品發行日期。 如果已知支援到期日與停用日期，也會一併提供。

> [!NOTE]
> 當版本的支援到期之後，就可能無法透過 Microsoft Azure 傳統入口網站取得它。 不過，您可透過 Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) 命令中的 `Version` 參數和 .NET SDK 持續取得叢集版本，直到版本停用日期為止。
> 
> 依預設，系統會為 HDInsight 2.1 和更新版本部署具有兩個前端節點的高可用性叢集。 它們不適用於 HDInsight 版本 1.6 叢集。

| HDInsight 版本 | HDP 版本 | VM OS | 高可用性 | 發行日期 | Azure 入口網站上的可用性 | 支援到期日 | 停用日期 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16 |是 |2017 年 4 月 4 日 |是 | | |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16 |是 |2016 年 9 月 30 日 |是 |2017 年 9 月 5 日 |2018 年 5 月 31 日 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |是 |2016 年 3 月 29 日 |是 |2016 年 12 月 29 日 |2018 年 1 月 9 日 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |是 |2015 年 12 月 2 日 |是 |2016 年 6 月 27 日 |2018 年 7 月 31 日 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |是 |2015 年 12 月 2 日 |是 |2016 年 6 月 27 日 |2017 年 7 月 31 日 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 或 Windows Server 2012 R2 |是 |2015 年 2 月 18 日 |否 |2016 年 3 月 1 日 |2017 年 4 月 1 日 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |是 |2014 年 6 月 24 日 |否 |2015 年 5 月 18 日 |2016 年 6 月 30 日 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |是 |2014 年 2 月 11 日 |否 |2014 年 9 月 17 日 |2015 年 6 月 30 日 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |是 |2013 年 10 月 28 日 |否 |2014 年 5 月 12 日 |2015 年 5 月 31 日 |
| HDInsight 1.6 |HDP 1.1 | |否 |2013 年 10 月 28 日 |否 |2014 年 4 月 26 日 |2015 年 5 月 31 日 |

## <a name="hdinsight-windows-retirement"></a>HDInsight Windows 停用項目
Microsoft Azure HDInsight 版本 3.3 是 Windows 上的最後一個 HDInsight 版本。 Windows 上 HDInsight 的停用日期是 2018 年 7 月 31 日。 如果您在 Windows 上有任何 3.3 版或更早版本的 HDInsight 叢集，就必須在 2018 年 7 月 31 日之前移轉至 Linux 上的 HDInsight 叢集 (HDInsight 版本 3.5 或更新版本)。 移轉至 Linux OS，可讓您保留建立或調整 HDInsight 叢集的能力。 對於 Windows 上 HDInsight 版本 3.3 的支援已在 2016 年 6 月 27 日到期。

從 HDInsight 版本 3.4 開始，Microsoft 只在 Linux OS 上發行 HDInsight。 因此，HDInsight 內的部分元件僅適用於 Linux。 其中包含 Apache Ranger、Kafka、Interactive Hive、Spark、HDInsight 的應用程式，以及作為主要檔案系統的 Azure Data Lake Store。 未來的 HDInsight 版本僅適用於 Linux OS。 未來不會在 Windows 上推出任何 HDInsight 版本。 

## <a name="faqs"></a>常見問題集

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>停用 Windows 上 HDInsight 的時間表為何？
Windows 上 HDInsight 的停用日期是 2018 年 7 月 31 日。 如果您的區域與規劃的停用日期不同，將個別通知您。 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>停用 Windows 上的 HDInsight 會對現有客戶產生何種影響？
停用 Windows 上的 HDInsight 之後，您便無法建立新的 HDInsight Windows 叢集，或調整現有的 HDInsight Windows 叢集。 對於 HDInsight 版本 3.3 的支援已在 2016 年 6 月 27 日到期。 因此，不會對 HDInsight 3.3 或更早版本提供任何支援或錯誤修正。 未來的 HDInsight 版本僅適用於 Linux OS。 未來不會在 Windows 上推出任何 HDInsight 版本。
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>哪些 Windows 上的 HDInsight 版本會受到影響？
Azure HDInsight 版本 3.3 是最後一個適用於 Windows 的 HDInsight 版本。 您必須在 Windows 上的 HDInsight 停用之前，將所有的 HDInsight Windows 叢集版本 3.3 或更早版本移轉至 Linux 上的 HDInsight 版本 3.5 或更新版本。 將您的叢集移轉至 Linux 上的 HDInsight，可讓您保留建立新叢集或調整現有叢集的能力。 

### <a name="what-do-i-need-to-do"></a>我需要做什麼事嗎？
在 2018 年 7 月 31 日之前，將您的 HDInsight Windows 叢集移轉到支援的 HDInsight Linux 叢集。 深入了解 [HDInsight 移轉文件](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux)。 如需 Azure HDInsight 版本的詳細資訊，請參閱[支援的版本](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions)清單。 

### <a name="where-do-i-find-the-cluster-os-type"></a>哪裡可以找到叢集 OS 類型？
在 Azure 入口網站中，移至 HDInsight 叢集的概觀頁面，然後在 [基本資訊] 下方找到 [叢集類型]。 叢集 OS 類型會列在該頁面上。 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>我無法在 2018 年 7 月 31 日之前移轉至 HDInsight Linux 叢集。 這樣會對我的 HDInsight Windows 叢集產生何種影響？
HDInsight Windows 叢集會依原樣執行，但您無法建立新的 HDInsight Windows 叢集，或調整現有的 HDInsight Windows 叢集。 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>我的叢集具有 .NET 相依性。 如何在 Linux 上解析此相依性？
您可以使用 [Mono 專案](http://www.mono-project.com/) \(英文\) 來解析 Linux 叢集相依性。 這個 .NET 開放原始碼實作適用於 HDInsight Linux 叢集。 深入了解 [HDInsight 移轉文件](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux)。 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>我是 Windows 上 HDInsight 的新客戶。 該如何建立 HDInsight Windows 叢集？
從 2017 年 7 月 3 日開始，只有現有的 HDInsight Windows 客戶可以建立新的 HDInsight Windows 叢集。 新客戶無法在 Azure 入口網站中，使用 PowerShell 或 SDK 來建立 HDInsight Windows 叢集。 我們建議新客戶建立 Linux HDInsight 叢集。 現有的客戶可以在 Windows上 HDInsight 的停用日期之前，建立新的 HDInsight Windows 叢集。 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>從 Windows 上的 HDInsight 移至 Linux 上的 HDInsight 是否會有相關聯的價格影響？
否，此價格對於任一 OS 上的 HDInsight 都一樣。 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>移至僅使用 Linux 上的 HDInsight，對客戶有哪些相關聯的優點？
* 透過 HDInsight 服務，可對開放原始碼巨量資料技術提供快速的上市時間
* 支援大型社群和生態系統
* 能夠讓開放原始碼社群練習積極開發 Hadoop 和較新的巨量資料技術

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>Linux 上的 HDInsight 是否提供 Windows 上的 HDInsight 中未提供的額外功能？
從 HDInsight 版本 3.4 開始，Microsoft 只在 Linux OS 上發行 HDInsight。 因此，HDInsight 內的部分元件僅適用於 Linux。 其中包含 Apache Ranger、Kafka、Interactive Hive、Spark、HDInsight 的應用程式，以及作為主要檔案系統的 Azure Data Lake Store。 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 叢集版本的服務等級協定
服務等級協定 (SLA) 是根據「支援期間」來定義。 支援期間是 Microsoft 客戶服務與支援中心支援 HDInsight 叢集版本的一段時間。 如果已超過版本的「支援到期日」，則表示該 HDInsight 叢集不在支援期間內。 如需支援版本的詳細資訊，請參閱[支援的 HDInsight 叢集版本](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux)清單。 特定 HDInsight 版本 X (在較新的 X+1 版本推出後) 的支援到期日計算方式會以下列較晚的時間為準：  

* 公式 1：將 HDInsight 叢集版本 X 的發行日期加上 180 天。
* 公式 2：將 Azure 入口網站中推出 HDInsight 叢集版本 X+1 的日期加上 90 天。

「停用日期」是在此日期之後便無法在 HDInsight 上建立叢集版本。 從 2017 年 7 月 31 日開始，您就無法在 HDInsight 叢集的停用日期之後調整叢集。 

> [!NOTE]
> HDInsight Windows 叢集 (包括版本 2.1、3.0、3.1、3.2 和 3.3) 均可在 Azure 客體 OS 系列第 4 版上執行，此系列使用 64 位元版的 Windows Server 2012 R2。 Azure 客體 OS 系列第 4 版支援 .NET Framework 版本 4.0、4.5、4.5.1 和 4.5.2。

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>與 HDInsight 版本相關聯的 Hortonworks 版本資訊

本節提供 Hortonworks Data Platform 發佈的版本資訊，以及與 HDInsight 搭配使用的 Apache 元件。
* HDInsight 叢集 3.6 版採用以 [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) 為基礎的 Hadoop 散發套件。
* HDInsight 叢集 3.5 版採用以 [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html) 為基礎的 Hadoop 散發套件。 HDInsight 叢集版本 3.5 是在 Azure 入口網站中建立的「預設」 Hadoop 叢集。
* HDInsight 叢集 3.4 版採用以 [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)為基礎的 Hadoop 散發套件。
* HDInsight 叢集 3.3 版採用以 [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)為基礎的 Hadoop 散發。

  * [Apache Storm 版本資訊](https://storm.apache.org/2015/11/05/storm0100-released.html) \(英文\) 可從 Apache 網站上取得。
  * [Apache Hive 版本資訊](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) \(英文\) 可從 Apache 網站上取得。
* HDInsight 叢集 3.2 版採用以 [Hortonworks Data Platform 2.2][hdp-2-2] 為基礎的 Hadoop 散發套件。

  * 特定 Apache 元件的可用版本資訊如下：[Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) 及 [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。
* HDInsight 叢集版本 3.1 使用以 [Hortonworks Data Platform 2.1.7][hdp-2-1-7] 為基礎的 Hadoop 散發套件。 在 2014 年 11 月 7 日之前建立的 HDInsight 3.1 叢集是以 [Hortonworks Data Platform 2.1.1][hdp-2-1-1] 為基礎。
* HDInsight 叢集 3.0 版採用以 [Hortonworks Data Platform 2.0][hdp-2-0-8] 為基礎的 Hadoop 散發套件。
* HDInsight 叢集 2.1 版採用以 [Hortonworks Data Platform 1.3][hdp-1-3-0] 為基礎的 Hadoop 散發套件。
* HDInsight 叢集 1.6 版採用以 [Hortonworks Data Platform 1.1][hdp-1-1-0] 為基礎的 Hadoop 散發套件。

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard 和 HDInsight Premium

Azure HDInsight 提供兩種類型的巨量資料雲端提供項目：_標準_和_進階_。 下表列出「只」適用於 HDInsight Premium 的功能。 表格中未明確說明的功能均適用於 HDInsight Standard 和 Premium。

> [!NOTE]
> HDInsight Premium 提供項目目前為預覽版，僅適用於 Linux 叢集。

| HDInsight Premium 功能 | 說明 |
| --- | --- |
| 已加入網域的 HDInsight 叢集 |將 HDInsight 叢集加入 Azure Active Directory (Azure AD) 網域，以獲得企業級安全性。 在 HDInsight Premium 中，您可以設定企業員工清單，指出可透過 Azure AD 進行驗證以登入 HDInsight 叢集的員工。 企業系統管理員可以使用 [Apache Ranger](http://hortonworks.com/apache/ranger/) \(英文\)，針對 Hive 安全性設定角色型存取控制，並將資料存取限制為只有在需要時使用。 最後，系統管理員可以稽核員工所存取的資料，以及對存取控制原則所做的變更，因而達到高度的公司資源控管。 如需詳細資訊，請參閱[設定已加入網域的 HDInsight 叢集](hdinsight-domain-joined-configure.md)。 |

### <a name="cluster-types-supported-in-hdinsight-premium"></a>HDInsight Premium 中支援的叢集類型
下表列出 HDInsight Premium 中支援的叢集類型。

| 叢集類型 | 標準 | 高階 (預覽) |
| --- | --- | --- |
| Hadoop |是 |是 (僅限 HDInsight 3.6) |
| Spark |是 |否 |
| HBase |是 |否 |
| Storm |是 |否 |
| R 伺服器 |是 |否 |
| 互動式 Hive (預覽) |是 |否 |
| Kafka (預覽) |是 |否 | 

### <a name="support-for-azure-data-lake-store-in-hdinsight-premium"></a>HDInsight Premium 中對於 Azure Data Lake Store 的支援

HDInsight Premium 叢集不支援使用 Azure Data Lake Store 當做主要儲存體。 但是，您可以使用 Azure Data Lake Store 作為含有 HDInsight Premium 叢集的附加元件儲存體。

### <a name="pricing-and-sla"></a>價格和 SLA
如需 HDInsight Premium 的價格和 SLA 的詳細資訊，請參閱 [HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>適用於叢集的預設節點設定和虛擬機器大小
下表列出 HDInsight 叢集的預設虛擬機器 (VM) 大小。

> [!IMPORTANT]
> 如果您的叢集需要 32 個以上的背景工作角色節點，則必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。
> 
> 

* 所有支援的區域，巴西南部和日本西部除外︰

  | 叢集類型 | Hadoop | HBase | Storm | Spark | R 伺服器 |
  | --- | --- | --- | --- | --- | --- |
  | 前端：預設 VM 大小 |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | 前端：建議的 VM 大小 |D3 v2、D4 v2、D12 v2 |D3 v2、D4 v2、D12 v2 |A3、A4、A5 |D12 v2、D13 v2、D14 v2 |D12 v2、D13 v2、D14 v2 |
  | 背景工作：預設 VM 大小 |D3 v2 |D3 v2 |D3 v2 |Windows：D12 v2；Linux：D4 v2 |Windows：D12 v2；Linux：D4 v2 |
  | 背景工作：建議的 VM 大小 |D3 v2、D4 v2、D12 v2 |D3 v2、D4 v2、D12 v2 |D3 v2、D4 v2、D12 v2 |Windows：D12 v2、D13 v2、D14 v2；Linux：D4 v2、D12 v2、D13 v2、D14 v2 |Windows：D12 v2、D13 v2、D14 v2；Linux：D4 v2、D12 v2、D13 v2、D14 v2 |
  | ZooKeeper：預設 VM 大小 | |A3 |A2 | | |
  | ZooKeeper：建議的 VM 大小 | |A3、A4、A5 |A2、A3、A4 | | |
  | 邊緣：預設 VM 大小 | | | | |Windows：D12 v2；Linux：D4 v2 |
  | 邊緣：建議的 VM 大小 | | | | |Windows：D12 v2、D13 v2、D14 v2；Linux：D4 v2、D12 v2、D13 v2、D14 v2 |
* 僅限巴西南部和日本西部 (沒有 v2 大小)：

  | 叢集類型 | Hadoop | HBase | Storm | Spark | R 伺服器 |
  | --- | --- | --- | --- | --- | --- |
  | 前端：預設 VM 大小 |D3 |D3 |A3 |D12 |D12 |
  | 前端：建議的 VM 大小 |D3、D4、D12 |D3、D4、D12 |A3、A4、A5 |D12、D13、D14 |D12、D13、D14 |
  | 背景工作：預設 VM 大小 |D3 |D3 |D3 |Windows：D12；Linux：D4 |Windows：D12；Linux：D4 |
  | 背景工作：建議的 VM 大小 |D3、D4、D12 |D3、D4、D12 |D3、D4、D12 |Windows：D12、D13、D14；Linux：D4、D12、D13、D14 |Windows：D12、D13、D14；Linux：D4、D12、D13、D14 |
  | ZooKeeper：預設 VM 大小 | |A2 |A2 | | |
  | ZooKeeper：建議的 VM 大小 | |A2、A3、A4 |A2、A3、A4 | | |
  | 邊緣：預設 VM 大小 | | | | |Windows：D12；Linux：D4 |
  | 邊緣：建議的 VM 大小 | | | | |Windows：D12、D13、D14；Linux：D4、D12、D13、D14 |

> [!NOTE]
> - 前端稱為 Storm 叢集類型的 Nimbus。
> - 背景工作角色稱為 Storm 叢集類型的「監督員」。
> - 背景工作角色稱為 HBase 叢集類型的「區域」。

## <a name="next-steps"></a>後續步驟
- [針對 Hadoop、Spark 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)
- [從 Windows PC 在 HDInsight 上的 Hadoop 中作業](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

