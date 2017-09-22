---
title: "設定 Azure 虛擬網路中的 HBase 叢集複寫 | Microsoft Docs"
description: "了解如何針對負載平衡、高可用性、零停機時間移轉和更新，以及災害復原來設定 HDInsight 版本之間的 HBase 複寫。"
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 87f4d219a678cab78980af2ff8b98ae55739f04c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/09/2017

---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>設定 Azure 虛擬網路中的 HBase 叢集複寫

了解如何在虛擬網路內或 Azure 中的兩個虛擬網路之間設定 HBase 複寫。

叢集複寫會使用來源推入方法。 HBase 叢集可以是來源、目的地，或可同時滿足兩個角色。 複寫不是同步進行。 複寫的目標最終會一致。 如果來源在複寫啟用時接收到資料行系列的編輯，該編輯會傳播到所有目的地叢集。 當資料從一個叢集複寫到另一個時，會追蹤來源叢集和已取用資料的所有叢集，以避免複寫迴圈。

在本教學課程中，您會設定來源與目的地之間的複寫。 若需其他叢集拓撲，請參閱 [Apache HBase 參考指南](http://hbase.apache.org/book.html#_cluster_replication)。

以下是適用於單一虛擬網路的 HBase 複寫使用案例︰

* 負載平衡。 例如，您可以在目的地叢集上執行掃描或 MapReduce 工作，以及在來源叢集上內嵌資料。
* 新增高可用性。
* 在 HBase 叢集之間移轉資料。
* 將 Azure HDInsight 叢集從一個版本升級到另一個版本。

以下是適用於兩個虛擬網路的 HBase 複寫使用案例︰

* 設定災害復原。
* 負載平衡與分割應用程式。
* 新增高可用性。

您可以從 [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication) 使用[指令碼動作](hdinsight-hadoop-customize-cluster-linux.md)指令碼複寫叢集。

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## <a name="set-up-the-environments"></a>設定環境

您有三個組態選項：

- 有兩個 HBase 叢集位於單一 Azure 虛擬網路中。
- 有兩個 HBase 叢集位於相同區域但不同的兩個虛擬網路中。
- 有兩個 HBase 叢集位於不同區域且不同的兩個虛擬網路中 (異地複寫)。

為了協助您設定環境，我們建立了一些 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-overview.md)。 如果您偏好使用其他方法設定環境，請參閱：

- [在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)
- [在 Azure 虛擬網路上建立 HBase 叢集](hdinsight-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>設定一個虛擬網路

若要在相同的虛擬網路中建立兩個 HBase 叢集，請選取下列圖像。 範本會儲存在 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/)。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>在相同區域中設定兩個虛擬網路

若要在相同區域中建立具有虛擬網路對等互連的兩個虛擬網路以及兩個 HBase 叢集，請選取下列圖像。 範本會儲存在 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/)。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



在此案例需要[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)。 範本可啟用虛擬網路對等互連。   

HBase 複寫會使用 ZooKeeper VM 的 IP 位址。 您必須設定目的地 HBase ZooKeeper 節點的靜態 IP 位址。

**設定靜態 IP 位址**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左功能表上選取 [資源群組]。
3. 選取包含目的地 HBase 叢集的資源群組。 這是當您使用 Resource Manager 範本來建立環境時所指定的資源群組。 您可以使用篩選來縮減清單。 您可以看到包含兩個虛擬網路的資源清單。
4. 選取包含目的地 HBase 叢集的虛擬網路。 例如，選取 **xxxx-vnet2**。 系統就會列出名稱開頭為 **nic-zookeepermode-** 的三個裝置。 這三個裝置為 ZooKeeper VM。
5. 選取其中一個 ZooKeeper VM。
6. 選取 [IP 組態]。
7. 在清單中，選取 **ipConfig1**。
8. 選取 [靜態]，並複製或記下實際 IP 位址。 在執行指令碼動作啟用複寫時，需要該 IP 位址。

  ![HDInsight HBase 複寫 ZooKeeper 靜態 IP 位址](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. 重複步驟 6 以設定其他兩個 ZooKeeper 節點的靜態 IP 位址。

如果是跨虛擬網路的案例，您必須在呼叫 `hdi_enable_replication.sh` 指令碼動作時使用 **ip** 切換。

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>在兩個不同區域中設定兩個虛擬網路

若要在兩個不同區域中建立兩個虛擬網路，請選取下列圖像。 範本會儲存於全域 Azure Blob 容器中。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

建立兩個虛擬網路之間的 VPN 閘道。 如需指示，請參閱[建立具有站對站連線的虛擬網路](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

HBase 複寫會使用 ZooKeeper VM 的 IP 位址。 您必須設定目的地 HBase ZooKeeper 節點的靜態 IP 位址。 若要設定靜態 IP，請參閱本文的[在相同區域中設定兩個虛擬網路](#set-up-two-virtual-networks-in-the-same-region)一節。

如果是跨虛擬網路的案例，您必須在呼叫 `hdi_enable_replication.sh` 指令碼動作時使用 **ip** 切換。

## <a name="load-test-data"></a>載入測試資料

當您複寫叢集時，您必須指定要複寫的資料表。 在本節中，您會把部分資料載入到來源叢集中。 在下一節中，您將會啟用兩個叢集之間的複寫。

若要建立一個**連絡人**資料表，並將部分資料插入資料表中，請依照 [HBase 教學課程：開始在 HDInsight 中搭配以 Linux 為基礎的 Hadoop 使用 Apache HBase](hdinsight-hbase-tutorial-get-started-linux.md) 中的指示。

## <a name="enable-replication"></a>啟用複寫

下列步驟會說明如何從 Azure 入口網站呼叫指令碼動作指令碼。 如需了解如何使用 Azure PowerShell 和 Azure 命令列介面 (Azure CLI) 執行指令碼動作，請參閱[使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

**從 Azure 入口網站啟用 HBase 複寫**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 開啟來源 HBase 叢集。
3. 從 [叢集] 功能表中，選擇 [指令碼動作]。
4. 在頁面的頂端，選取 [提交新項目] 。
5. 選取或輸入下列資訊︰

  1. **名稱**：輸入「啟用複寫」。
  2. **Bash 指令碼 URL**：輸入 **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**。
  3.  **前端**：務必選取此項目。 清除其他節點類型。
  4. **參數**：下列範例參數會針對所有現有的資料表啟用複寫，然後將來源叢集的所有資料複製到目的地叢集：

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. 選取 [ **建立**]。 指令碼執行需要花費一些時間，特別是在使用 **-copydata** 引數的情況下。

必要的引數︰

|名稱|說明|
|----|-----------|
|-s, --src-cluster | 指定來源 HBase 叢集的 DNS 名稱。 例如：-s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | 指定目的地 (複本) HBase 叢集的 DNS 名稱。 例如：-s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | 指定來源 HBase 叢集上 Ambari 的管理員密碼。 |
|-dp, --dst-ambari-password | 指定目的地 HBase 叢集上 Ambari 的管理員密碼。|

選擇性的引數︰

|名稱|說明|
|----|-----------|
|-su, --src-ambari-user | 指定來源 HBase 叢集上 Ambari 的管理員使用者名稱。 預設值為 **admin**。 |
|-du, --dst-ambari-user | 指定目的地 HBase 叢集上 Ambari 的管理員使用者名稱。 預設值為 **admin**。 |
|-t, --table-list | 指定要複寫的資料表。 例如：--table-list="table1;table2;table3"。 如果您未指定資料表，則會複寫所有現有的 HBase 資料表。|
|-m, --machine | 指定用來執行指令碼動作的前端節點。 值為 **hn1** 或 **hn0**。 因為 **hn0** 前端節點通常較忙碌，我們建議您使用 **hn1**。 如果您從 HDInsight 入口網站或 Azure PowerShell 以指令碼動作執行 $0 指令碼，則使用此選項。|
|-ip | 當您啟用兩個虛擬網路之間的複寫時會需要此引數。 此引數會作為交換器，以使用複本叢集 (而非 FQDN 名稱) 的 ZooKeeper 節點靜態 IP 位址。 啟用複寫之前，您必須預先設定靜態 IP 位址。 |
|-cp, -copydata | 在已啟用複寫的資料表上，啟用現有資料的移轉。 |
|-rpm, -replicate-phoenix-meta | 在 Phoenix 系統資料表上啟用複寫。 <br><br>*請謹慎使用此選項。* 建議您在使用此指令碼前，於複本叢集上重新建立 Phoenix 資料表。 |
|-h, --help | 顯示使用資訊。 |

[指令碼](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh)的 `print_usage()` 區段有參數的詳細說明。

成功部署指令碼動作之後，您可以使用 SSH 連接到目的地 HBase 叢集，並確認已複寫資料。

### <a name="replication-scenarios"></a>複寫案例

下列清單會顯示一些一般使用情況和其參數設定︰

- **在兩個叢集之間的所有資料表上啟用複寫**。 這種情況下，資料表上不需要複製或移轉現有資料，且不會使用 Phoenix 資料表。 使用下列參數︰

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **在特定資料表上啟用複寫**。 如要在 table1、table2 和 table3 上啟用複寫，請使用下列參數：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **在特定資料表上啟用複寫，並複製現有資料**。 如要在 table1、table2 和 table3 上啟用複寫，請使用下列參數：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **在所有資料表上啟用複寫，並將 Phoenix 中繼資料從來源複寫到目的地**。 Phoenix 中繼資料複寫並非萬無一失。 請謹慎使用。 使用下列參數︰

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>複製並移轉資料

啟用複寫後，有兩個可用來複製或移轉資料的個別指令碼動作指令碼：

- [適用於小型資料表的指令碼](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (表格約數 GB 大小，且整體複製預估會在一小時內完成)

- [適用於大型資料表的指令碼](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (預估複製時間會超過一小時的表格)

您可以依照[啟用複寫](#enable-replication)中描述的相同程序，來呼叫指令碼動作。 使用下列參數︰

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

[指令碼](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh)的 `print_usage()` 區段有參數的詳細說明。

### <a name="scenarios"></a>案例

- **針對到目前為止 (目前時間戳記) 所有已編輯的資料列複製特定資料表 (test1、test2 和 test3)**：

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  或：

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **以指定時間範圍複製特定資料表**：

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>停用複寫

若要停用複寫，可從 [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) 使用另一個指令碼動作指令碼。 您可以依照[啟用複寫](#enable-replication)中描述的相同程序，來呼叫指令碼動作。 使用下列參數︰

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

[指令碼](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)的 `print_usage()` 區段有參數的詳細說明。

### <a name="scenarios"></a>案例

- **停用所有資料表上的複寫**：

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  或

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **停用特定資料表 (table1、table2 和 table3) 上的複寫**：

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在虛擬網路內或兩個虛擬網路之間設定 HBase 複寫。 若要深入了解 HDInsight 與 HBase ，請參閱下列文章：

* [開始使用 HDInsight 中的 Apache HBase][hdinsight-hbase-get-started]
* [HDInsight HBase 概觀][hdinsight-hbase-overview]
* [在 Azure 虛擬網路上建立 HBase 叢集][hdinsight-hbase-provision-vnet]
* [使用 HBase 分析即時 Twitter 情緒][hdinsight-hbase-twitter-sentiment]
* [在 HDInsight (Hadoop) 中使用 Storm 和 HBase 分析感應器資料][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

