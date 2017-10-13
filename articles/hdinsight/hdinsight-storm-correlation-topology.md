---
title: "使用 HDInsight 上的 Storm 和 HBase 讓事件隨著時間而相互關聯"
description: "了解如何使用 HDInsight 上的 Storm 和 HBase 讓在不同的時間抵達的事件相互關聯。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 17d11479-2d02-4790-8d29-05fb38351479
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/07/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 06630096383601e48e8f69f8553314cee42f5f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="correlate-events-that-arrive-at-different-times-using-storm-and-hbase"></a>在不同時間使用 Storm 和 HBase 抵達的事件相互關聯

搭配 Apache Store 使用永續性資料存放區，可以讓在不同時間抵達的資料項目相互關聯。 例如，連結使用者工作階段的登入和登出事件，可計算工作階段的持續時間長度。

在本文件中，您會學習如何建立基本的 C# Storm 拓撲，以便追蹤使用者工作階段的登入和登出事件，並計算工作階段的持續時間。 此拓撲會使用 HBase 做為永續性資料存放區。 HBase 也可讓您對歷程記錄資料執行批次查詢來產生額外的見解。 例如，在特定時間週期內已開始或結束多少個使用者工作階段。

## <a name="prerequisites"></a>必要條件

* Visual Studio 和 HDInsight tools for Visual Studio。 如需詳細資訊，請參閱[開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)。

* Apache Storm on HDInsight 叢集 (Windows)。

  > [!WARNING]
  > 雖然在 2016/10/28 之後建立的 Linux 架構 Storm 叢集可支援 SCP.NET 拓撲，但 2016/10/28 起提供的 HBase SDK for .NET 套件無法在 Linux 上的 HDInsight 中正確運作

* Apache HBase on HDInsight 叢集 (Linux 或 Windows 型)。

  > [!IMPORTANT]
  > Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* 開發環境為 [Java](https://java.com) 1.7 或更新版本。 當拓撲提交到 HDInsight 叢集時，會使用 Java 來封裝它。

  * **JAVA_HOME** 環境變數必須指向包含 Java 的目錄。
  * **%JAVA_HOME%/bin** 目錄必須在路徑中

## <a name="architecture"></a>架構

![透過拓撲的資料流程圖表](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

需要有事件來源的通用識別碼，才能讓事件相互關聯。 例如，使用者識別碼、工作階段識別碼，或其他資料：a) 唯一的資料，b) 包含在所有傳送至 Storm 的資料。 此範例使用 GUID 值代表工作階段識別碼。

此範例包含兩個 HDInsight 叢集：

* HBase：歷程記錄資料的持續性資料存放區
* Storm：用來擷取傳入的資料

資料是由 Storm 拓撲隨機產生，並包括下列項目：

* 工作階段識別碼：可唯一識別每個工作階段的 GUID
* 事件：START 或 END 事件。 在此範例中，START 一律發生於 END 之前
* 時間：事件的時間。

此資料已處理並儲存在 HBase 中。

### <a name="storm-topology"></a>Storm 拓撲

當工作階段開始時， **START** 事件會由拓撲接收並記錄至 HBase。 收到 **END** 事件時，拓撲會擷取 **START** 事件並計算兩個事件之間的時間。 此 [持續時間] 值接著會連同 **END** 事件資訊儲存在 HBase 中。

> [!IMPORTANT]
> 雖然此拓撲示範基本模式，但生產方案必須採用下列案例的設計：
>
> * 未按順序抵達的事件
> * 重複的事件
> * 卸除的事件

本範例拓撲包含下列元件：

* Session.cs：藉由建立隨機工作階段識別碼、開始時間，以及工作階段的持續時間長度，模擬使用者工作階段。

* Spout.cs：建立 100 個工作階段、發出 START 事件、等候每個工作階段隨機逾時，然後發出 END 事件。 然後回收結束的工作階段，以產生新的工作階段。

* HBaseLookupBolt.cs：使用工作階段識別碼來查閱 HBase 中的工作階段資訊。 處理 END 事件時，它會尋找對應的 START 事件並計算工作階段的持續時間。

* HBaseBolt.cs：將資訊儲存在 HBase 中。

* TypeHelper.cs：有助於讀取/寫入 HBase 時的類型轉換。

### <a name="hbase-schema"></a>HBase 結構描述

在 HBase 中，資料會儲存在具有下列結構描述/設定的資料表中：

* 資料列索引鍵：工作階段識別碼做為此資料表中資料列的索引鍵。

* 資料行系列：系列名稱為 'cf'。 儲存在此系列中的資料行如下：

  * 事件：START 或 END。

  * 時間：事件發生的時間 (以毫秒為單位)。

  * 持續時間：START 和 END 事件之間的長度。

* 版本：'cf' 系列會設定為每個資料列保留 5 個版本。

  > [!NOTE]
  > 版本是針對特定資料列索引鍵儲存的先前值的記錄檔。 根據預設，HBase 只會針對資料列的最新版本傳回此值。 在此情況下，相同的資料列使用於所有事件 (START、END)，而資料列的每個版本都是以時間戳記值識別。 版本可提供針對特定識別碼記錄之事件的歷程記錄檢視。

## <a name="download-the-project"></a>下載專案

您可以從 [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation)下載範例專案。

此下載包含下列 C# 專案：

* CorrelationTopology：針對使用者工作階段隨機發出開始與結束事件的 C# Storm 拓撲。 每個工作階段持續 1 到 5 分鐘。

* SessionInfo：C# 主控台應用程式，用以建立 HBase 資料表，以及提供範例查詢來傳回儲存的工作階段資料相關資訊。

## <a name="create-the-table"></a>建立資料表

1. 在 Visual Studio 中開啟 **SessionInfo** 專案。

2. 在 [方案總管] 中，於 **SessionInfo** 專案上按一下滑鼠右鍵，然後選取 [屬性]。

    ![已選取屬性的功能表螢幕擷取畫面](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. 選取 [ **設定**]，然後設定下列值：

   * HBaseClusterURL：HBase 叢集的 URL。 例如，https://myhbasecluster.azurehdinsight.net。

   * HBaseClusterUserName：您的叢集的 admin/HTTP 使用者帳戶

   * HBaseClusterPassword：admin/HTTP 使用者帳戶的密碼

   * HBaseTableName：要用於此範例的資料表名稱

   * HBaseTableColumnFamily：資料行系列名稱

     ![設定對話方塊的影像](./media/hdinsight-storm-correlation-topology/settings.png)

4. 執行方案。 出現提示時，選取用以在 HBase 叢集上建立資料表的 'c' 索引鍵。

## <a name="build-and-deploy-the-storm-topology"></a>建立和部署 Storm 拓撲

1. 在 Visual Studio 中開啟 [ **CorrelationTopology** ] 方案。

2. 在 [方案總管] 中，於 **CorrelationTopology** 專案上按一下滑鼠右鍵，然後選取 [屬性]。

3. 在 [屬性] 視窗中，選取 [設定] 並輸入此專案的組態值。 前 5 項是 **SessionInfo** 專案使用的相同值：

   * HBaseClusterURL：HBase 叢集的 URL。 例如，https://myhbasecluster.azurehdinsight.net。

   * HBaseClusterUserName：您的叢集的 admin/HTTP 使用者帳戶。

   * HBaseClusterPassword：admin/HTTP 使用者帳戶的密碼。

   * HBaseTableName：要用於此範例的資料表名稱。 這個值與 SessionInfo 專案中使用的資料表名稱相同。

   * HBaseTableColumnFamily：資料行系列名稱。 這個值與 SessionInfo 專案中使用的資料行系列名稱相同。

   > [!IMPORTANT]
   > 請勿變更 HBaseTableColumnNames，因為預設值是 **SessionInfo** 用來擷取資料的名稱。

4. 儲存屬性，然後建置專案。

5. 在 [方案總管] 中，於專案上按一下滑鼠右鍵，然後選取 [提交至 Storm on HDInsight]。 如果出現提示，請輸入您 Azure 訂用帳戶的認證。

   ![提交至 Storm 功能表項目的影像](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6. 在 [提交拓撲] 對話方塊中，選取要將此拓撲部署到的 Storm 叢集。

   > [!NOTE]
   > 第一次提交拓撲時，可能需要幾秒鐘的時間來擷取您的 HDInsight 叢集名稱。

7. 拓撲上傳並提交至叢集後，[Storm 拓撲檢視] 會開啟並顯示執行中的拓撲。 若要重新整理資料，請選取 **CorrelationTopology**，並使用頁面右上方的 [重新整理] 按鈕。

   ![拓撲檢視的影像](./media/hdinsight-storm-correlation-topology/topologyview.png)

   拓撲開始產生資料時，[已發出] 中的值會遞增。

   > [!NOTE]
   > 如果 [ **Storm 拓撲檢視** ] 並未自動開啟，請使用下列步驟來開啟：
   >
   > 1. 在 [方案總管] 中，展開 **Azure**，然後展開 **HDInsight**。
   > 2. 以滑鼠右鍵按一下正在執行拓撲的 Storm 叢集，然後選取 [檢視 Storm 拓撲]

## <a name="query-the-data"></a>查詢資料

發出資料後，使用下列步驟來查詢資料。

1. 回到 **SessionInfo** 專案。 如果不在執行中，請啟動它的新執行個體。

2. 出現提示時，選取 **s** 以搜尋 START 事件。 系統會提示您輸入開始和結束時間來定義時間範圍，發生於這兩個時間之間的事件才會傳回。

    輸入開始和結束時間時，請使用下列格式：HH:MM 和 'am' 或 'pm'。 例如，11:20pm。

    若要傳回記錄的事件，請使用早於 Storm 拓撲部署的開始時間，以及現在的結束時間。 傳回的資料包含類似下列文字的項目︰

        Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

搜尋 END 事件和 START 事件的方式相同。 不過，END 事件會在 START 事件之後的 1 到 5 分鐘之間隨機產生。 您可能必須嘗試幾個時間範圍，以便找出 END 事件。 END 事件也會包含工作階段的持續時間，也就是 START 事件時間和 END 事件時間之間的差異。 以下是 END 事件的資料範例：

    Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [!NOTE]
> 雖然您輸入的時間值都是當地時間，但查詢所傳回的時間會是 UTC。

## <a name="stop-the-topology"></a>停止拓撲

當您準備要停止拓撲時，請回到 Visual Studio 中的 **CorrelationTopology** 專案。 在 [Storm 拓撲檢視] 中，選取拓撲，然後使用拓撲檢視頂端的 [刪除] 按鈕。

## <a name="delete-your-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>後續步驟

如需更多 Storm 範例，請參閱 [Storm on HDInsight 上的範例拓撲](hdinsight-storm-example-topology.md)。
