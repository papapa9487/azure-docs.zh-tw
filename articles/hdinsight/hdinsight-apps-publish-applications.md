---
title: "發行 HDInsight 應用程式 - Azure | Microsoft Docs"
description: "了解如何建立和發佈 HDInsight 應用程式。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 0ccaee16a19b4338099a93f994553caaa6aa57e3
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>將 HDInsight 應用程式發佈到 Azure Marketplace
HDInsight 應用程式是使用者可以在以 Linux 為基礎的 HDInsight 叢集上安裝的應用程式。 Microsoft 獨立軟體廠商 (ISV) 或您可以自己開發這些應用程式。 在此文章中，您會學習如何將 HDInsight 應用程式發佈到 Azure Marketplace。  如需發佈到 Azure Marketplace 的一般資訊，請參閱[將提供項目發佈到 Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md)。

HDInsight 應用程式採用「自備授權 (BYOL)」模型，其中的應用程式提供者負責將應用程式授權給一般使用者，而 Azure 只會向一般使用者收取其所建資源的費用，例如 HDInsight 叢集與其 VM/節點。 目前，Azure 不經手應用程式本身的計費。

其他 HDInsight 應用程式相關文章︰

* [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)︰了解如何將 HDInsight 應用程式安裝到您的叢集。
* [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何安裝和測試自訂 HDInsight 應用程式。

## <a name="prerequisites"></a>必要條件
若要將自訂應用程式提交至 Marketplace，您必須建立並測試您的自訂應用程式。 請參閱下列文章：

* [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何安裝和測試自訂 HDInsight 應用程式。

您還必須註冊開發人員帳戶。 請參閱[將提供項目發佈到 Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) 和[建立 Microsoft 開發人員帳戶](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。

## <a name="define-application"></a>定義應用程式
將應用程式發佈至 Azure Marketplace 涉及兩個步驟。  首先，定義 **createUiDef.json** 檔，以指出與您的應用程式相容的叢集，然後從 Azure 入口網站發佈範本。 下一節是範例 createUiDef.json 檔案。

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| 欄位 | 描述 | 可能的值 |
| --- | --- | --- |
| types |與應用程式相容的叢集類型。 |Hadoop、HBase、Storm、Spark (或這些類型的任意組合) |
| tiers |與應用程式相容的叢集層。 |Standard、Premium (或兩者) |
| versions |與應用程式相容的 HDInsight 叢集類型。 |3.4 |

## <a name="application-install-script"></a>應用程式安裝指令碼
每當應用程式安裝在 (現有或新的) 叢集上，就會在叢集上安裝邊緣節點並執行應用程式安裝指令碼。
  > [!IMPORTANT]
  > 應用程式安裝指令碼的名稱必須是特定叢集中唯一的名稱 (採用以下的格式)：
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > 請注意，指令碼名稱有三個部分︰
  > 
  > 1. 指令碼名稱前置應該包含應用程式名稱或與該應用程式相關的名稱。
  > 2. "-" 以方便閱讀。
  > 3. 唯一的字串函數，並以應用程式名稱做為參數。
  > 
  > 在保存的指令碼動作清單中，上述範例最終會變成 hue-install-v0-4wkahss55hlas。 如需 JSON 承載的範例，請參閱 [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json)。
  > 
安裝指令碼必須具有下列特性：
1. 確定指令碼具有等冪性。 指令碼的多個呼叫應產生相同的結果。
2. 指令碼必須是正確版本。 當您升級或測試變更時，請為指令碼使用不同位置，以便嘗試安裝應用程式的客戶不會受到影響。 
3. 在每個點的指令碼中加入適當的記錄。 指令碼記錄檔通常是對應用程式安裝問題進行偵錯的唯一方法。
4. 確定外部服務或資源的呼叫有足夠的重試次數，讓安裝不會受到暫時性網路問題的影響。
5. 如果您的指令碼在節點上啟動服務，請確定服務受到監視並設定為在節點重新開機時自動啟動。

## <a name="package-application"></a>封裝應用程式
建立 zip 檔案，其中包含安裝 HDInsight 應用程式時的所有必要檔案。 您需要[發佈應用程式](#publish-application)中的 zip 檔案。

* [createUiDefinition.json](#define-application)。
* mainTemplate.json。 請參閱[安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)中的範例。
* 所有必要的指令碼。

> [!NOTE]
> 應用程式檔案 (包括 Web 應用程式檔案 (若有的話)) 可以位於任何可公開存取的端點上。
> 

## <a name="publish-application"></a>發佈應用程式
請遵循下列步驟來發佈 HDInsight 應用程式︰

1. 登入 [Azure 發佈入口網站](https://publish.windowsazure.com/)。
2. 按一下左邊的 [方案範本]  來建立新的方案範本。
3. 輸入標題，然後按一下 [建立新的方案範本]。
4. 按一下 [建立開發人員中心帳戶並加入 Azure 方案]  以註冊您的公司 (如果尚未這麼做)。  請參閱 [建立 Microsoft 開發人員帳戶](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。
5. 按一下 [定義一些拓撲以便開始使用] 。 方案範本是所有其拓撲的「父項」。 您可以在一個供應項目/解決方案範本中定義多個拓撲。 當供應項目推送到預備環境時，它的所有拓撲也會一起推入。 
6. 輸入拓撲名稱，然後按一下加號。
7. 輸入新的版本，然後按一下加號。
8. 上傳在[封裝應用程式](#package-application)中準備的 zip 檔案。  
9. 按一下 [要求認證]。 Microsoft 認證團隊會檢閱檔案並認證拓撲。

## <a name="next-steps"></a>後續步驟
* [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)︰了解如何將 HDInsight 應用程式安裝到您的叢集。
* [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何將未發佈的 HDInsight 應用程式部署到 HDInsight。
* [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用指令碼動作來安裝其他應用程式。
* [使用 Azure Resource Manager 範本在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰了解如何呼叫 Resource Manager 範本來建立 HDInsight 叢集。
* [在 HDInsight 中使用空白邊緣節點](hdinsight-apps-use-edge-node.md)︰了解如何使用空白邊緣節點來存取 HDInsight 叢集、測試 HDInsight 應用程式，以及裝載 HDInsight 應用程式。


