---
title: "將 Azure 資料科學虛擬機器佈建為 IPython Notebook 伺服器 | Microsoft Docs"
description: "使用支援的工具，將資料科學虛擬機器佈建為 IPython Notebook 伺服器。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 95e1fa87-794a-4d03-80a4-af4f3f3ac31e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 88fe9673176cdade92faad4bbdcb2e1bd11f4a55
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="provision-azure-data-science-virtual-machines-as-ipython-notebook-servers"></a>將 Azure 資料科學虛擬機器佈建為 IPython Notebook 伺服器
這裡提供的指示會說明如何設定 Azure VM 和含有 SQL 服務的 Azure VM，以做為 IPython Notebook 伺服器。 Windows 虛擬機器是使用支援工具 (例如，IPython Notebook、Azure 儲存體總管及 AzCopy)，以及其他對於資料科學專案非常實用的公用程式來設定。 例如，Azure 儲存體總管和 AzCopy 會提供便利的方法，將資料從本機電腦上傳至 Azure 儲存體，或者從儲存體將資料下載到本機電腦。 

此功能表所連結的主題會說明如何設定 [Team Data Science Process (TDSP)](overview.md)所用的各種資料科學環境。

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

有數種類型的 Azure 虛擬機器可佈建並設定來做為雲端架構資料科學環境的一部分。 決定要使用哪一個虛擬機器類別，取決於要使用機器學習服務進行模型化的資料類型和數量，以及該資料在雲端中的目標目的地。 

* 如需進行這項決策時要考量之問題的指引，請參閱 [規劃您的 Azure Machine Learning 資料科學環境](plan-your-environment.md)。 
* 如需在進行進階分析時可能會遇到之部分案例的目錄，請參閱 [Azure Machine Learning 中進階分析程序和技術的案例](plan-sample-scenarios.md)

提供兩組指示：

* [將 Azure 虛擬機器設定為 IPython Notebook 伺服器供進階分析使用](../data-science-virtual-machine/setup-virtual-machine.md) 示範如何針對可使用某種 SQL 以外的 Azure 儲存體來儲存資料的情況，使用 IPython Notebook 和其他用來進行資料科學的工具來佈建 Azure 虛擬機器。
* [將 Azure SQL Server 虛擬機器設定為 IPython Notebook 伺服器供進階分析使用](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)示範如何針對可用來儲存資料的 SQL 資料庫情況，使用 IPython Notebook 和其他用來進行資料科學的工具來佈建 Azure SQL 虛擬機器。

完成佈建並設定之後，這些虛擬機器就已經準備好用來做為 IPython Notebook 伺服器，以進行資料探索和處理，以及其他需要與 Azure 機器學習服務和 Team Data Science Process (TDSP) 一起使用的工作。 [TDSP 學習路徑](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中說明了資料科學程序的後續步驟，其中可能包含將資料移至 SQL Server 或 HDInsight，並在其中處理資料與取樣，做為透過 Azure 學習機器服務從資料學習的準備。

> [!NOTE]
> Azure 虛擬機器的定價策略是「 **只針對您使用的項目進行付費**」。 若要確保未使用虛擬機器時不會被計費，其在 **Azure 傳統入口網站** 中的狀態必須是 [已停止 (已取消配置)](http://manage.windowsazure.com/)。 如需逐步指示或如何取消配置虛擬機器的相關資訊，請參閱[關閉並取消配置未使用的虛擬機器](../data-science-virtual-machine/setup-virtual-machine.md#shutdown)
> 
> 


