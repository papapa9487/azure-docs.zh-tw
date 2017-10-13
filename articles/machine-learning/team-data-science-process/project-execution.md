---
title: "執行資料科學專案 - Azure  | Microsoft Docs"
description: "資料科學家如何以可追蹤、版本控制，以及共同作業的方式執行資料科學專案。"
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: 8c318f87243d0c98b6a42bebcdffb433f9cc456e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="execution-of-data-science-projects"></a>執行資料科學專案

本文件說明資料科學家如何藉由使用 [Team 資料科學程序](overview.md) (TDSP)，在專案小組中以系統化、版本控制，以及共同作業的方式執行資料科學專案。 TDSP 是 Microsoft 開發的架構，提供結構化的一系列活動，以有效率地執行雲端式、預測性分析解決方案。 如需人員角色的大綱，以及對此程序進行標準化之資料科學小組所處理相關聯工作的大綱，請參閱 [Team 資料科學程序角色和工作](roles-tasks.md)。 

本主題包含如何執行以下作業的指示： 

1. 針對專案中涉及的工作項目進行**短期衝刺計劃**。<br> 如果您不熟悉短期衝刺計劃，您可以在[這裡](https://en.wikipedia.org/wiki/Sprint_(software_development) "這裡")找到詳細資料和一般資訊。 
2. **新增工作項目**至短期衝刺。
3. **連結工作項目與編碼活動**，由 git 追蹤。
4. 執行**程式碼檢閱**。 


>[AZURE.NOTE] 我們概述在下列指示集合中使用 Visual Studio Team Services (VSTS) 設定 TDSP 小組環境所需的步驟。 我們會指定如何使用 VSTS 完成這些工作，因為這是我們在 Microsoft 中實作 TDSP 的方式。 上述清單中的項目 (3) 和 (4) 是您選擇使用 VSTS 時自然得到的好處。 如果針對您的群組使用另一個程式碼裝載平台，必須由小組負責人完成的工作通常不會變更。 但是完成這些工作的方式將會不同。 例如，區段 6 中的項目：**連結工作項目與 git 分支**，可能無法像在 VSTS 上一樣簡單。

下圖說明實作資料科學專案時涉及的典型短期衝刺計劃、編碼，以及原始檔控制工作流程：

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1.<a name='Terminology-1'></a>術語 

在 TDSP 短期衝刺計劃架構中，有四種常用的**工作項目**類型：**功能**、**使用者劇本**、**工作**和 **Bug**。 每個 Team 專案會針對所有工作項目維護單一待處理項目。 在 Team 專案底下的 git 存放庫層級沒有任何待處理項目。 其定義如下：

- **功能**：對應至專案參與的功能。 用戶端的不同參與會被視為不同的功能。 同樣地，最好將用戶端專案的不同階段視為不同的功能。 如果您選擇例如 ***ClientName-EngagementName*** 的結構描述來為功能命名，則您可以輕易地從名稱本身辨識專案/參與的內容。
- **劇本**：劇本是與端對端完成功能 (專案) 所需的工作項目不同的工作項目。 劇本的範例包括：
    - 取得資料 
    - 瀏覽資料 
    - 產生功能
    - 建立模型
    - 運用模型 
    - 重新定型模型
- **工作**：工作是可指派的程式碼或文件工作項目或其他活動，必須完成這些項目才能完成特定劇本。 例如，劇本「取得資料」中的工作可能是：
    -  取得 SQL Server 的認證 
    -  將資料上傳至 SQL 資料倉儲。 
- **Bug**：Bug 通常是指完成工作時完成之現有程式碼或文件所需的修正。 如果 BUG 是因為分別遺漏階段或工作所造成，它可以提升為劇本或工作。 

>[AZURE.NOTE] 我們從資料科學中使用的軟體程式碼管理 (SCM) 借用功能、劇本、工作及 BUG 的概念。 它們可能會與傳統 SCM 定義稍有不同。

##  2.<a name='SprintPlanning-2'></a>短期衝刺計劃 

短期衝刺計劃對於專案優先順序，以及資源規劃和配置相當有用。 許多資料科學家正在參與許多專案，每個專案都可能需要幾個月才能完成。 專案通常是以不同的步調進行。 在 VSTS 伺服器上，您可以輕鬆地建立、管理和追蹤 Team 專案中的工作項目，並且執行短期衝刺計劃，以確保您的專案如預期般前進。 

請遵循[此連結](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning)以取得 VSTS 中短期衝刺計劃的逐步指示。 


##  3.<a name='AddFeature-3'></a>新增功能  

在 Team 專案底下建立專案存放庫之後，請移至小組 [概觀] 分頁，然後按一下 [管理工作]。

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

若要在待處理項目中包含功能，請按一下 待處理項目  -->  功能  -->  新增，輸入功能**標題** (通常是您的專案名稱)，然後按一下新增。

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

按兩下您剛才建立的功能。 填入描述、指派此功能的小組成員，以及設定此功能的計劃參數。 

您也可以將此功能連結至專案存放庫。 按一下 [開發] 區段底下的 [新增連結]。 完成編輯功能之後，按一下 [儲存並關閉] 以結束。


##  4.<a name='AddStoryunderfeature-4'></a>在功能底下新增劇本 

在功能底下可以新增劇本以描述完成 (功能) 專案所需的主要步驟。 若要新增劇本，請按一下待處理項目檢視中功能左側的 **+** 符號。  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

您可以在快顯視窗中編輯劇本的詳細資料，例如狀態、描述、註解、計劃和優先順序。

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

您也可以按一下 [開發] 底下的 [+ 新增連結]，將此劇本連結至現有的存放庫。 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5.<a name='AddTaskunderstory-5'></a>將工作新增至劇本 

工作是完成各個劇本所需的特定詳細步驟。 完成劇本的所有工作之後，劇本應該也會完成。 

若要將工作新增至劇本，請按一下劇本項目旁邊的 **+** 符號，選取 [工作]，然後在快顯視窗中填入此工作的詳細資訊。

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

建立功能、劇本和工作之後，您可以在 [待處理項目] 或 [面板] 檢視中檢視它們，來追蹤其狀態。

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6.<a name='Linkaworkitemwithagitbranch-6'></a>連結工作項目與 git 分支 

VSTS 提供連結工作項目 (劇本或工作) 與 git 分支的便利方式。 這可讓您將劇本或工作直接連接至與它相關聯的程式碼。 

若要將工作項目連接至新分支，請按兩下工作項目，然後在快顯視窗中按一下 [+ 新增連結] 底下的 [建立新的分支]。  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

提供這個新分支的資訊，例如分支名稱、基底 git 存放庫和分支。 選擇的 git 存放庫必須是工作項目所屬之相同 Team 專案底下的存放庫。 基底分支可以是主要分支或其他某些現有的分支。

![11](./media/project-execution/project-execution-11-create-a-branch.png)

為每個劇本工作項目建立 git 分支是很好的作法。 然後，您可以根據劇本分支，為每個工作工作項目建立分支。 以對應到劇本-工作關係的這種階層方式組織分支，在您有許多人員使用相同專案的不同劇本時，或者有許多人員使用相同劇本的不同工作時，都很有幫助。 當每個小組成員使用不同的分支時，以及當共用分支的時候每個成員使用不同程式碼或其他構件時，衝突能夠降至最低。 

下圖說明建議的 TDSP 分支策略。 您可能不需要像這裡示範的這麼多分支，特別是當您只有一個或兩個人員使用相同專案，或只有一個人員使用劇本的所有工作。 但是將開發分支與主要分支分隔開來，永遠是良好的作法。 這可協助防止發行分支被開發活動中斷。 git 分支模型的完整說明可以在[成功的 git 分支模型](http://nvie.com/posts/a-successful-git-branching-model/)中找到。

![12](./media/project-execution/project-execution-12-git-branches.png)

若要切換至您想要使用的分支，請在殼層命令中執行下列命令 (Windows 或 Linux)。 

    git checkout <branch name>

將 <分支名稱\> 變更為「主要」，會讓您切換回**主要**分支。 切換至工作分支之後，您可以開始使用該工作項目，開發完成此項目所需的程式碼或文件構件。 

您也可以將工作項目連結至現有的分支。 在工作項目的 [詳細資料] 分頁中，不是按一下 [建立新的分支]，而是按一下 [+ 新增連結]。 然後，選取您想要連結至工作項目的分支。 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

您也可以使用 git bash 命令建立新的分支。 如果 <基底分支名稱\> 遺漏，<新的分支名稱\> 會根據_主要_分支。 
    
    git checkout -b <new branch name> <base branch name>


##  7.<a name='WorkonaBranchandCommittheChanges-7'></a>使用分支並且認可變更 

現在假設您對工作項目的 data\_ingestion 分支進行一些變更，例如在本機機器中的分支上新增 R 檔案。 假設您是在 Git 殼層的該分支中，使用下列 Git 命令，可以將新增的 R 檔案認可至此工作項目的分支：

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8.<a name='CreateapullrequestonVSTS-8'></a>在 VSTS 上建立提取要求 

當您在一些認可和推送之後準備就緒時，若要將目前的分支合併至其基底分支，您可以在 VSTS 伺服器上提交**提取要求**。 

請移至 Team 專案的主要分頁，然後按一下 [程式碼]。 選取要合併的分支和分支合併所在的 git 存放庫名稱。 然後按一下提取要求、按一下 新的提取要求，在分支上的工作合併至其基底分支之前，建立提取要求檢閱。

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

填入有關這項提取要求的一些描述、新增檢閱者，然後送出。

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9.<a name='ReviewandMerge-9'></a>檢閱及合併 

建立提取要求時，您的檢閱者會取得電子郵件通知，以檢閱提取要求。 檢閱者必須檢查變更是否有作用，以及在可行時測試要求者的變更。 根據其評量，檢閱者可以核准或拒絕提取要求。 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

完成檢閱之後，按一下 [完成] 按鈕，工作分支便會合併到其基底分支。 您可以選擇在工作分支合併之後將其刪除。 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

確認左上角要求標示為「已完成」。 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

當您返回 [程式碼] 底下的存放庫時，系統會告訴您，您已切換到主要分支。

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

您也可以使用下列 Git 命令，將工作分支合併到其基底分支，並且在合併之後刪除工作分支：

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10.<a name='DataQualityReportUtility-10'></a>互動式資料探索、分析和報告 (IDEAR) 公用程式

此 R Markdown 型公用程式提供彈性且互動式的工具，以評估及瀏覽資料集。 使用者可以使用最少的編碼，從資料集快速產生報告。 使用者可以按一下按鈕，將他在互動式工具中看到的探索結果匯出至最終報告，這份報告可以傳遞給用戶端或用來決定要在後續模型化步驟中包含哪些變數。

目前，此工具只適用於記憶體中的資料框架。 若要指定要探索之資料集的參數，需要 .yaml 檔案。 如需詳細資訊，請參閱 [TDSP 資料科學公用程式中的 IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils)。


##  11.<a name='ModelingUtility-11'></a>基準模型化和報告公用程式

此公用程式提供可自訂的半自動工具，來執行具有超參數清除的模型建立，並且比較這些模型的精確度。 

模型建立公用程式是 R Markdown 檔案，可執行以產生自封式 html 輸出，具有目錄以輕鬆瀏覽其不同區段。 執行 Markdown 檔案時會執行三個演算法 (knit)：使用 glmnet 套件的正規化迴歸、使用 randomForest 套件的隨機樹系，以及使用 xgboost 套件的提升樹狀結構)。 每個演算法都會產生定型模型。 然後會比較這些模型的準確度，並且報告相對功能重要性繪圖。 目前有兩個公用程式：一個適用於二進位分類工作，另一個適用於迴歸工作。 兩者之間的主要差異是為這些學習工作指定控制參數和精確度計量的方式。 

Yaml 檔案是用來指定：

- 資料輸入 (SQL 來源或 R-資料檔案) 
- 資料的哪個部分用於定型，哪個部分用於測試
- 要執行哪些演算法 
- 模型最佳化之控制參數的選擇：
    - 交叉驗證 
    - 啟動程序
    - 摺疊交叉驗證
- 每個演算法的超參數集合。 

演算法數目、最佳化的摺疊數目、超參數，以及要執行清除之超參數集合的數目，也可以在 Yaml 檔案中修改以快速地執行模型。 例如，可以使用較低數目的 CV 摺疊、較低數目的參數集合來執行。 如果獲得保證，也可以使用較高數目的 CV 摺疊或較大數目的參數集合，以便更全面地執行。

如需詳細資訊，請參閱 [TDSP 資料科學公用程式中的自動化模型化和報告公用程式](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)。


##  12.<a name='PowerBI-12'></a> Power BI 儀表板追蹤專案進度

資料科學群組管理員、小組負責人和專案負責人需要追蹤其專案的進度，哪些工作已完成、由誰完成，以及仍然在待辦事項清單上。 如果您使用 VSTS，您可以建立 Power BI 儀表板來追蹤與 Git 存放庫相關聯的活動和工作項目。 如需有關如何連接 Power BI 與 Visual Studio Team Services 的詳細資訊，請參閱[連接 Power BI 與 Team Services](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs)。 

若要深入了解如何建立 Power BI 儀表板和報告，在 VSTS 的資料連接至 Power BI 之後，追蹤您的 Git 存放庫活動和工作項目，請參閱[建立 Power BI 儀表板和報告](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs)。 

以下是我們建置以追蹤 Git 活動和工作項目的兩個簡單範例儀表板。 在第一個範例儀表板中，git 認可活動是依據不同的使用者、不同的日期，以及不同的存放庫列出。 您可以輕鬆細分篩選到您感興趣的項目。

![23](./media/project-execution/project-execution-23-powerbi-git.png)

在第二個範例儀表板中，會顯示不同反覆項目中的工作項目 (劇本和工作)。 它們是根據受指派者和優先順序層級進行分組，並且根據狀態著色。

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>後續步驟

此外也會提供完整的端對端逐步解說，說明 **特定案例** 之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)主題中會列出這些逐步解說以及簡短說明的連結。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。 

針對使用 Azure Machine Learning Studio 的 Team Data Science Process，如需其中執行步驟的範例，請參閱[使用 Azure ML](http://aka.ms/datascienceprocess) 學習路徑。