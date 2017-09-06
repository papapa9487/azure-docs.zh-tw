---
title: "使用 Azure CLI 管理 Hadoop 叢集 - Azure HDInsight| Microsoft Docs"
description: "了解如何使用 Azure 命令列介面來管理 Azure HDInsight 中的 Hadoop 叢集。 Azure CLI 適用於 Windows、Mac 和 Linux。"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: cc1b78e430ab46f367fafcc16e1240aa76a9ba64
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>使用 Azure CLI 管理 HDInsight 中的 Hadoop 叢集
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

了解如何使用 [Azure 命令列介面](../cli-install-nodejs.md)來管理 Azure HDInsight 中的 Hadoop 叢集。 Azure CLI 是在 Node.js 中實作。 此工具可在任何支援 Node.js 的平台上使用，包括 Windows、Mac 和 Linux。 目前 HDInsight 並不支援 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview)。

本文只涵蓋搭配 HDInsight 使用 Azure CLI。 如需如何使用 Azure CLI 的一般指南，請參閱[安裝及設定 Azure CLI][azure-command-line-tools]。

## <a name="prerequisites"></a>先決條件
開始閱讀本文之前，您必須符合下列必要條件：

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **Azure CLI** - 請參閱[安裝及設定 Azure CLI](../cli-install-nodejs.md) 以取得安裝和設定資訊。
* **連線到 Azure**，使用下列命令：

    ```cli
    azure login
    ```
  
    如需使用公司或學校帳戶驗證的詳細資訊，請參閱[從 Azure CLI 連線至 Azure 訂用帳戶](../xplat-cli-connect.md)。
* 使用下列命令來**切換至 Azure 資源管理員模式**：
  
    ```cli
    azure config mode arm
    ```

若要取得說明，請使用 **-h** 參數。  例如：

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>使用 CLI 建立叢集
請參閱[使用 Azure CLI 建立 HDInsight 中的叢集](hdinsight-hadoop-create-linux-clusters-azure-cli.md)。

## <a name="list-and-show-cluster-details"></a>列出和顯示叢集詳細資料
使用下列命令，以列出並顯示叢集詳細資料：

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![叢集清單的命令列檢視][image-cli-clusterlisting]

## <a name="delete-clusters"></a>刪除叢集
使用下列命令來刪除叢集：

```cli
azure hdinsight cluster delete <Cluster Name>
```

您也可以刪除包含叢集的資源群組來刪除叢集。 請注意，這將會刪除群組中的所有資源 (包括預設儲存體帳戶)。

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>調整叢集
若要變更 Hadoop 叢集大小：

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>啟用/停用叢集 HTTP 存取

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>啟用/停用叢集 RDP 存取

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>後續步驟
本文中，您學到如何執行不同的 HDInsight 叢集管理工作。 若要深入了解，請參閱下列文章：

* [使用 Azure 入口網站管理 HDInsight][hdinsight-admin-portal]
* [使用 Azure PowerShell 管理 HDInsight][hdinsight-admin-powershell]
* [開始使用 Azure HDInsight][hdinsight-get-started]
* [如何使用 Azure CLI][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "列出和顯示叢集"

