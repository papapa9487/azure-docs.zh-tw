---
title: "適用於 Azure Cosmos DB 的 Visual Studio 已連線服務"
description: "透過 Visual Studio 已連線的服務讓開發人員輕鬆地連線其 Azure Cosmos DB 帳戶及管理資源"
services: cosmos-db
documentationcenter: 
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: 26d8399ce4ea23b51677def85b3732468dcbc02f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB：Visual Studio 已連線的服務 (預覽)

Visual Studio 已連線的服務可讓開發人員輕鬆地連線其 Azure Cosmos DB 帳戶及管理其資源。

您也可以在已連線的服務中使用 [資料總管] 來建立預存程序、UDF 和觸發程序，以執行伺服器端商務邏輯。 [資料總管] 會公開 API 中所有可用的內建程式設計資料存取，但可讓您輕鬆存取您的資料。

## <a name="prerequisites"></a>必要條件

請確定您具有下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。 
* Azure Cosmos DB 帳戶。 如果您還沒有帳戶，請遵循[建立 Azure Cosmos DB 帳戶](create-documentdb-dotnet.md)的步驟在 Azure 入口網站中建立帳戶，或請參閱[在已連線的服務工具中建立 Azure Cosmos DB 帳戶](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool)。 
* 如果您想要使用本機環境來進行開發，您可以使用 [Azure Cosmos DB 模擬器](local-emulator.md)。 環境便會模擬 Azure Cosmos DB 服務。
* [Visual Studio](http://www.visualstudio.com/)。
* 最新版的 Azure Cosmos DB 已連線服務。 您可以從 Visual Studio Marketplace 下載 Azure Cosmos DB 已連線服務，如下列螢幕擷取畫面所示。 在電腦中開啟 **Visual Studio**。 在 [工具] 功能表上，選取 [擴充功能和更新...]，然後選擇 [線上] / [Visual Studio Marketplace]。 輸入 **cosmosdb** 來搜尋版本。

    您也可以從 [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709) 安裝 Azure Cosmos DB 已連線的服務。

    ![Connected Service download bits.png 的螢幕擷取畫面](./media/connected-service/connected-service-downloadbits.png) 

## <a id="SetupVS"></a>設定您的 Visual Studio 方案
1. 在電腦上開啟 **Visual Studio**。
2. 從 [檔案] 功能表中，選取 [新增]，然後選擇 [專案]。
3. 在 [新增專案] 對話方塊中，選取 [Visual C#] / [主控台應用程式 (.NET Framework)] 或 [Web 應用程式 (.NET Framework)]，為專案命名，然後按一下 [確定]。

    ![[新增專案] 視窗的螢幕擷取畫面](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>新增已連線的服務及新增帳戶
1. 在 [方案總管] 中，以滑鼠右鍵按一下專案節點，然後選取 [新增] / [已連線的服務]。 或者，按一下 [專案] 功能表，然後選取 [新增已連線的服務]。

    ![[新增已連線的服務] 視窗的螢幕擷取畫面](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. 在 [已連線的服務] 頁面上，按一下 [已連線的服務] / [Azure Cosmos DB] 以開啟 [Azure Cosmos DB] 頁面。

    ![[Azure Cosmos DB] 視窗的螢幕擷取畫面](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. 按一下向下箭號以進行首次登入，或者新增帳戶。 在登入後，空白區域中就會顯示所有的 Azure Cosmos DB 帳戶。 選擇一個 Azure Cosmos DB 帳戶來新增至您的專案。

    ![登入並列出 DB 帳戶視窗的螢幕擷取畫面](./media/connected-service/connected-service-add-db-account.png)
4. 在新增 Azure Cosmos DB 帳戶後，系統就會在專案中新增 Azure Cosmos DB 帳戶之已連線服務的資料夾。 您可以重複步驟 1 到步驟 3，來新增多個 Azure Cosmos DB 帳戶。

    ![[已連線的服務] 資料夾視窗的螢幕擷取畫面](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. 在新增 Azure Cosmos DB 已連線的服務後，將專案修改為可透過下列其中一種方式存取 Azure Cosmos DB：

    * 安裝 Azure Cosmos DB 用戶端所需的某些 Nuget 套件。 您可以從套件組態檔看到這些套件。 

        ![新的 Azure Cosmos DB 套件組態](./media/connected-service/connected-service-packages-config.png)   
    
    * 在專案組態檔 (在此例為 App.config) 中新增 Azure Cosmos DB 連線 uri 和金鑰。 

        ![新的 Azure Cosmos DB 應用程式組態](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>開啟 Azure Cosmos DB Explorer
1. 以滑鼠右鍵按一下專案節點，然後選取 [開啟 Cosmos DB Explorer...]。

    ![嘗試開啟 [資料總管] 視窗的螢幕擷取畫面](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. 在 [選擇 Cosmos DB 帳戶] 頁面中，按一下下拉式清單以選取一個 Azure Cosmos DB 帳戶。

    ![[已新增帳戶已連線的服務] 視窗的螢幕擷取畫面](./media/connected-service/connected-service-open-explorer.png)
3. 按一下 [開啟]，便會顯示 [資料總管] 視窗。

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>在已連線的服務工具中建立 Azure Cosmos DB 帳戶
1. 在 [已連線的服務] 頁面的窗格左下方，按一下 [建立新的 Cosmos DB 帳戶] 以開啟 [建立 Cosmos DB 帳戶] 頁面。

    ![開啟 [建立 Azure Cosmos DB 帳戶] 項目點視窗的螢幕擷取畫面](./media/connected-service/connected-service-click-new-db-account.png)
2. 在 [建立 Cosmos DB 帳戶] 頁面上，指定您要用於此 Azure Cosmos DB 帳戶的組態。

    使用下列螢幕擷取畫面中的資訊作為參考，填妥 [建立 Cosmos DB 帳戶] 頁面中的欄位。 
 
    ![[新增 Azure Cosmos DB] 頁面](./media/connected-service/connected-service-create-new-account.png)        
3. 按一下 [建立]  來建立帳戶。

## <a name="use-data-explorer"></a>使用資料總管

在開啟資料總管後，您可以執行下列作業：
* 建立和刪除資料庫
* 建立和刪除集合
* 建立、刪除和篩選文件
* 建立和刪除預存程序
* 建立和刪除用來執行伺服器端商務邏輯的觸發程序和使用者定義的函式。 

![[新增 Azure Cosmos DB] 頁面](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>示範

觀賞下列影片以了解如何在 Visual Studio 中使用 Azure Cosmos DB 已連線的服務：[在 Visual Studio 中使用 Azure Cosmos DB 已連線的服務](https://go.microsoft.com/fwlink/?linkid=858711)

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解下列事項：

> [!div class="checklist"]
> * 建立 Azure Cosmos DB 帳戶
> * 新增已連線的服務及新增帳戶
> * 開啟 Azure Cosmos DB Explorer
> * 使用資料總管

現在，您已使用 Azure Cosmos DB 帳戶讓已連線的服務啟動並執行，接下來請繼續進行其中一個教學課程以開始開發您的解決方案：

* [在 .NET 中利用 SQL API 進行開發](tutorial-develop-documentdb-dotnet.md)。
* [Azure Cosmos DB：開始使用 SQL API 教學課程](documentdb-get-started.md)。
* 需要使用 Azure Cosmos DB 來執行規模和效能測試嗎？ 請參閱 [Azure Cosmos DB 的效能和級別測試](performance-testing.md)。
* 了解如何[監視 Azure Cosmos DB 帳戶](monitor-accounts.md)。

