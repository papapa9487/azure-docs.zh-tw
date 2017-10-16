---
title: "Hadoop 安全性 - 已加入網域的 HDInsight 叢集 - Azure | Microsoft Docs"
description: "了解 ..."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/31/2016
ms.author: saurinsh
ms.openlocfilehash: 303be1d303df8074283cb1d37c74923cca80ae59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters-preview"></a>已加入網域之 HDInsight 叢集的 Hadoop 安全性簡介 (預覽)

到目前為止，Azure HDInsight 僅支援單一使用者本機系統管理員。這很適合用於比較小型的應用程式團隊或部門。 Hadoop 型工作負載在企業間越來越受青睞，而 Active Directory 型驗證、多使用者支援和角色型存取控制等企業級功能的需求日益重要。 使用已加入網域的 HDInsight 叢集時，您可以建立已加入 Active Directory 網域的 HDInsight 叢集、設定企業中可以透過 Azure Active Directory 進行驗證來登入 HDInsight 叢集的員工清單。 企業外部的任何人都無法登入或存取此 HDInsight 叢集。 企業系統管理員可以使用 [Apache Ranger](http://hortonworks.com/apache/ranger/)，針對 Hive 安全性設定角色型存取控制，進而將資料存取限制為僅限有需要時。 最後，系統管理員可以依照員工稽核資料存取，以及對存取控制原則所做的任何變更，因而達到高度的公司資源控管。

> [!NOTE]
> 此預覽中所述的新功能僅適用於 Hive 工作負載之以 Linux 為基礎的 HDInsight 叢集。 其他工作負載 (例如 HBase、Spark、Storm 和 Kafka) 將在未來版本中啟用。

> [!IMPORTANT]
> Oozie 未在已加入網域的 HDInsight 上啟用。

## <a name="benefits"></a>優點
企業安全性包含四大要件 – 周邊安全性、驗證、授權和加密。

![已加入網域的 HDInsight 叢集優點要件](./media/hdinsight-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png)。

### <a name="perimeter-security"></a>周邊安全性
使用虛擬網路和閘道服務可達成 HDInsight 中的周邊安全性。 現今，企業系統管理員可以在虛擬網路內建立 HDInsight 叢集，並使用網路安全性群組 (輸入或輸出防火牆規則) 來限制虛擬網路的存取。 只有輸入防火牆規則中所定義的 IP 位址能夠與 HDInsight 叢集通訊，因而提供周邊安全性。 使用閘道服務可以達成另一層的周邊安全性。 閘道是做為第一道防線的服務，可抵禦任何對於 HDInsight 叢集的連入要求。 它會接受要求、進行驗證，然後只允許要求傳遞至叢集中的其他節點，因而對叢集中的其他名稱和資料節點提供周邊安全性。

### <a name="authentication"></a>驗證
利用此公開預覽，企業系統管理員可以在[虛擬網路](https://azure.microsoft.com/services/virtual-network/)中佈建已加入網域的 HDInsight 叢集。 HDInsight 叢集的節點將會加入企業所管理的網域。 透過使用 [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) 即可達成此目的。 叢集中的所有節點便已加入企業所管理的網域。 使用這項設定，企業員工可以使用其網域認證來登入叢集節點。 他們也可以使用其網域認證向其他已核准的端點 (例如 Hue、Ambari 檢視、ODBC、JDBC、PowerShell 和 REST API) 進行驗證，進而與叢集互動。 系統管理員可以完全控制透過這些端點與叢集互動的使用者數量。

### <a name="authorization"></a>授權
大多數企業所依循的最佳作法，就是並非每一位員工都能存取所有的企業資源。 同樣地，在此版本中，系統管理員可以針對叢集資源定義角色型存取控制原則。 例如，系統管理員可以設定 [Apache Ranger](http://hortonworks.com/apache/ranger/) 以設定 Hive 的存取控制原則。 這項功能可確保員工只能存取他們要順利工作所需的資料量。 叢集的 SSH 存取也僅限於系統管理員。

### <a name="auditing"></a>稽核
除了防止未經授權的使用者存取 HDInsight 叢集資源，以及保護資料安全以外，還要稽核所有對於叢集資源的存取，而且需有此資料才能追蹤未經授權或意外的資源存取。 此預覽中，系統管理員可以檢視和報告所有對於 HDInsight 叢集資源和資料的存取。 系統管理員也可以檢視和報告在 Apache Ranger 支援的端點中對於存取控制原則進行的所有變更。 已加入網域的 HDInsight 叢集會使用熟悉的 Apache Ranger UI 來搜尋稽核記錄檔。 Ranger 會在後端使用 [Apache Solr](http://hortonworks.com/apache/solr/) 來儲存及搜尋記錄檔。

### <a name="encryption"></a>加密
保護資料對於符合組織安全性和規範需求而言極為重要，而除了限制未經授權的員工存取資料以外，還應該透過加密方式加以保護。 HDInsight 叢集的兩個資料存放區 (Azure 儲存體 Blob 和 Azure Data Lake 儲存體) 都支援透明的伺服器端待用[資料加密](../storage/common/storage-service-encryption.md)。 安全的 HDInsight 叢集會完美地與此伺服器端待用資料加密功能配合運作。

## <a name="next-steps"></a>後續步驟
* 如需設定已加入網域的 HDInsight 叢集，請參閱[設定已加入網域的 HDInisight 叢集](hdinsight-domain-joined-configure.md)。
* 如需管理已加入網域的 HDInsight 叢集，請參閱[管理已加入網域的 HDInisight 叢集](hdinsight-domain-joined-manage.md)。
* 如需設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInisight 叢集設定 Hive 原則](hdinsight-domain-joined-run-hive.md)。
* 若要在已加入網域的 HDInsight 叢集上使用 SSH 執行 Hive 查詢，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。
