---
title: "管理已加入網域的 HDInsight 叢集 - Azure | Microsoft Docs"
description: "了解如何管理已加入網域的 HDInsight 叢集"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 0fc32960fc2f1ae69315dbfd6bfb8c34c4adc0fa
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>管理已加入網域的 HDInsight 叢集
認識已加入網域的 HDInsight 叢集中的使用者和角色，了解如何管理已加入網域的 HDInsight 叢集。

## <a name="users-of-domain-joined-hdinsight-clusters"></a>已加入網域的 HDInsight 叢集的使用者
未加入網域的 HDInsight 叢集有兩個使用者帳戶，是在叢集建立期間建立的︰

* **Ambari 系統管理員**：此帳戶亦稱為「Hadoop 使用者」或「HTTP 使用者」。 此帳戶可以用來登入 Ambari，網址是 https://&lt;clustername>.azurehdinsight.net。 它也可以用來在 Ambari 檢視上執行查詢、透過外部工具 (即 PowerShell、Templeton、Visual Studio)執行作業、以及使用 Hive ODBC 驅動程式和 BI 工具 (即 Excel、PowerBI、或 Tableau) 進行驗證。
* **SSH 使用者**︰此帳戶可以搭配 SSH 使用，執行 sudo 命令。 它具有 Linux VM 上的根權限。

除了 Ambari 系統管理員和 SSH 使用者之外，已加入網域的 HDInsight 叢集有三個新的使用者。

* **Ranger 系統管理員**︰此帳戶是本機的 Apache Ranger 系統管理員帳戶。 不是 Active Directory 網域使用者。 此帳戶可以用來設定原則、將其他使用者設為系統管理員、或委派系統管理工作 (讓某些使用者可以管理原則)。 預設的使用者名稱是 admin，密碼則是與 Ambari 系統管理員的密碼相同。 可以在 Ranger 的 [設定] 頁面中更新密碼。
* **叢集系統管理員網域使用者**︰此帳戶是被指定為 Hadoop 叢集系統管理員 (包括 Ambari 和 Ranger) 的 Active Directory 網域使用者。 您必須在叢集建立期間提供此使用者的認證。 此使用者有下列權限：

  * 在叢集建立期間，將機器加入網域，並將它們放入您指定的 OU 中。
  * 在叢集建立期間，於您指定的 OU 中建立服務主體。
  * 建立反向 DNS 項目。

    請注意，其他的 AD 使用者也有這些權限。

    叢集內有些端點 (例如，Templeton) 不受 Ranger 管理，因此不安全。 這些端點會針對所有使用者鎖定，只有叢集系統管理員網域使用者除外。
* **一般**︰在叢集建立期間，您可以提供多個 Active Directory 群組。 這些群組中的使用者都將同步至 Ranger 和 Ambari。 這些使用者是網域使用者，只有存取 Ranger 管理之端點 (例如Hiveserver2) 的權限。 所有 RBAC 原則和稽核皆不適用於這些使用者。

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>已加入網域的 HDInsight 叢集的角色
已加入網域的 HDInsight 有下列角色︰

* 叢集系統管理員
* 叢集操作員
* 服務管理員
* 服務操作員
* 叢集使用者

**若要查看這些角色的權限**

1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 按一下左側功能表中的 [角色]。
3. 按一下藍色問號可查看權限︰

    ![已加入網域的 HDInsight 角色權限](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>開啟 Ambari 管理 UI
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在刀鋒視窗中開啟您的 HDInsight 叢集。 請參閱[列出和顯示叢集](../hdinsight-administer-use-management-portal.md#list-and-show-clusters)。
3. 按一下頂端功能表中的 [儀表板]  開啟 Ambari。
4. 使用叢集系統管理員網域使用者的名稱和密碼登入 Ambari：
5. 按一下右上角的 [管理] 下拉式功能表，然後按一下 [管理 Ambari]。

    ![已加入網域的 HDInsight 管理 Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    UI 看起來像這樣：

    ![已加入網域的 HDInsight 的 Ambari 管理 UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>列出從您的 Active Directory 同步處理的網域使用者
1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 按一下左側功能表中的 [使用者] 。 您應該會看到從您的 Active Directory 同步至 HDInsight 叢集的所有使用者。

    ![已加入網域的 HDInsight 的 Ambari 管理 UI 列出使用者](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>列出從您的 Active Directory 同步處理的網域群組
1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 按一下左側功能表中的 [群組]。 您應該會看到從您的 Active Directory 同步至 HDInsight 叢集的所有群組。

    ![已加入網域的 HDInsight 的 Ambari 管理 UI 列出群組](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>設定 Hive 檢視權限
1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 按一下左側功能表中的 [檢視]。
3. 按一下 [HIVE] 以顯示詳細資料。

    ![已加入網域的 HDInsight 的 Ambari 管理 UI Hive 檢視](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. 按一下 [Hive 檢視] 連結以設定 Hive 檢視。
5. 向下捲動至 [權限]。

    ![已加入網域的 HDInsight 的 Ambari 管理 UI Hive 檢視設定權限](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. 按一下 [新增使用者] 或 [新增群組]，然後指定可以使用 Hive 檢視的使用者或群組。

## <a name="configure-users-for-the-roles"></a>設定角色的使用者
 若要查看角色及其權限的清單，請參閱[已加入網域的 HDInsight 叢集的角色](#roles-of-domain---joined-hdinsight-clusters)。

1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 按一下左側功能表中的 [角色]。
3. 按一下 [新增使用者] 或 [新增群組] 以將使用者或群組指定至不同角色。

## <a name="next-steps"></a>後續步驟
* 如需設定已加入網域的 HDInsight 叢集，請參閱[設定已加入網域的 HDInsight 叢集](apache-domain-joined-configure.md)。
* 如需設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInisight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 若要在已加入網域的 HDInsight 叢集上使用 SSH 執行 Hive 查詢，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。
