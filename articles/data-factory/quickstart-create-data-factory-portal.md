---
title: "使用 Azure 入口網站建立 Azure 資料處理站 | Microsoft Docs"
description: "建立 Azure 資料處理站，將資料從雲端資料存放區 (Azure Blob 儲存體) 複製到另一個雲端資料存放區 (Azure SQL Database)。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: b884d7f08311cc60dc3af500f552d525d23b91e6
ms.contentlocale: zh-tw
ms.lasthandoff: 09/26/2017

---
# <a name="create-a-data-factory-using-the-azure-portal"></a>使用 Azure 入口網站來建立資料處理站
Azure Data Factory 是雲端式資料整合服務，可讓您在雲端建立資料驅動工作流程，以便協調及自動進行資料移動和資料轉換。 使用 Azure Data Factory，您可以建立和排程資料驅動工作流程 (稱為管線)，這類工作流程可以從不同資料存放區內嵌資料，使用計算服務 (例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure Machine Learning) 來處理/轉換資料，以及將輸出資料發佈至資料存放區 (例如 Azure SQL 資料倉儲)，以供商業智慧 (BI) 應用程式使用。 

在這個快速入門中，您會使用 Azure 入口網站來建立資料處理站。 建立資料處理站之後，您需要使用 PowerShell、.NET SDK、Python SDK 或 REST API，啟動會在將資料從來源資料存放區複製到目的地資料存放區的資料管線。 目前，您無法使用 Azure 入口網站在資料處理站中建立管線。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-data-factory"></a>建立 Data Factory
以下是您會在本快速入門中執行的步驟：
1. 按一下左邊功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
   ![新增->DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 刀鋒視窗中，輸入 **ADFTutorialDataFactory** 作為 [名稱]。 
      
     ![新增 Data Factory 刀鋒視窗](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您收到錯誤，請變更 Data Factory 名稱 (例如 yournameADFTutorialDataFactory)，然後試著重新建立。 請參閱 [Data Factory - 命名規則](naming-rules.md) 主題，以了解 Data Factory 成品的命名規則。
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。 
4. 針對 [資源群組]，請執行下列其中一個步驟︰
     
      - 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。 
      - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
      本快速入門的某些步驟是假設您使用 **ADFTutorialResourceGroup** 作為資源群組名稱。 若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
4. 對 [版本] 選取 [V2 (預覽)]。
5. 選取 Data Factory 的 [位置]  。 目前，您只可以在**美國東部**區域建立 V2 資料處理站。 不過，資料處理站中使用的計算和資料存放區可以位在其他區域。 
6. 選取 [釘選到儀表板]。     
7. 按一下 [建立] 。
      
      > [!IMPORTANT]
      > 若要建立 Data Factory 執行個體，您必須是訂用帳戶/資源群組層級的 [Data Factory 參與者](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) 角色成員。
      > 
      > Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。             
3. 在儀表板上，您會看到狀態如下的下列圖格︰**正在部署資料處理站**。 

    ![部署資料處理站圖格](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
1. 建立完成之後，您會看到如圖所示的 [Data Factory]  刀鋒視窗。
   
   ![Data Factory 首頁](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)


## <a name="next-steps"></a>後續步驟
在此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 瀏覽[教學課程](tutorial-copy-data-dot-net.md)以了解使用 Data Factory 的更多案例。 
