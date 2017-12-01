---
title: "使用 Git 存放庫搭配 Azure Machine Learning Workbench 專案 | Microsoft Docs"
description: "本文說明如何使用 Git 存放庫並搭配 Azure Machine Learning Workbench 專案。"
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 0cd447a52964578dd2348a786dd57a45ea87516e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>使用 Git 存放庫搭配 Azure Machine Learning Workbench 專案
本文件提供 Azure Machine Learning Workbench 如何使用 Git 提供版本控制，並確保資料科學實驗重現性的相關資訊。 還提供如何將您的專案與雲端 Git 存放庫產生關聯的指示。

## <a name="introduction"></a>簡介
Azure Machine Learning Workbench 的設計基本上就是與 Git 整合在一起。 建立新專案時，專案資料夾會自動「Git 初始化」作為本機 Git 存放庫 (存放庫)。 同時，也會建立第二個隱藏本機 Git 存放庫，包含名為 _AzureMLHistory / < project_GUID >_ 的分支，來追蹤每個執行的專案資料夾變更。 

將 Azure ML 專案與 Git 存放庫 (裝載於 Visual Studio Team 服務 (VSTS) 專案中) 產生關聯，可在本機和遠端啟用自動版本控制。 此關聯會讓可存取遠端存放庫的任何人將最新原始程式碼下載到另一部電腦 (漫遊)。  

> [!NOTE]
> VSTS 有其自己的存取控制清單，與 Azure Machine Learning 測試服務無關。 在 Git 存放庫和 Azure ML 工作區或專案之間，使用者存取權可能不同，還可能需要管理。 因此，如果您想要將 Azure ML 專案分享給小組成員，包括程式碼層級存取，則除了只分享工作區，您還需要明確授與適當存取權讓他/她存取 VSTS Git 存放庫。 

Git 也可讓您使用「主要」分支，或在存放庫上建立其他分支，，以明確管理版本控制。 您可以只使用本機 Git 存放庫，也可以推送至遠端 Git 存放庫 (如果已佈建)。

此圖表描述 VSTS Git 存放庫與 Azure ML 專案之間的關聯性：

![AML Git](media/using-git-ml-project/aml_git.png)

若要開始使用遠端 Git 存放庫，請遵循下列基本指示。

> [!NOTE]
> 目前，Azure Machine Learning 只在 VSTS 帳戶上支援 Git 存放庫。 已規劃在未來支援一般 Git 存放庫 (例如 GitHub 等)。

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>步驟 1. 建立 Azure ML 測試帳戶
建立 Azure ML 測試帳戶並安裝 Azure ML Workbench 應用程式 (如果尚未這麼做)。 如需詳細資料，請參閱[安裝和建立快速入門](quickstart-installation.md)。

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>步驟 2. 建立 Team 專案，或使用現有的 Team 專案
從 [Azure 入口網站](https://portal.azure.com/)，建立新的 **Team 專案**。
1. 按一下 **+**
2. 搜尋**「Team 專案」**
3. 輸入必要資訊。
    - 名稱：小組名稱。
    - 版本控制：**Git**
    - 訂用帳戶：具有 Azure Machine Learning 測試帳戶的訂用帳戶。
    - 位置：最好停留在接近 Azure Machine Learning 實驗資源的區域。
4. 按一下 [建立] 。 

![從 Azure 入口網站建立 Team 專案](media/using-git-ml-project/create_vsts_team.png)

請務必以用來存取 Azure Machine Learning Workbench 的 Azure Active Directory (AAD) 帳戶登入。 否則，系統無法使用您的 AAD 認證加以存取，除非您使用命令列來建立 Azure ML 專案，並提供個人存取權杖以存取 Git 存放庫。 稍後將深入討論。

建立 Team 專案之後，您就可以繼續下一個步驟。

若要直接瀏覽至剛才建立的 Team 專案，URL 是 `https://<team_project_name>.visualstudio.com`。

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>步驟 3. 建立含有遠端 Git 存放庫的新 Azure ML 專案
啟動 Azure ML Workbench，並建立新的專案。 在 [Git 存放庫] 文字方塊中，填入您從步驟 2 取得的 VSTS Git 存放庫 URL。 通常看起來像這樣：`http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![建立含有 Git 存放庫的 Azure ML 專案](media/using-git-ml-project/create_project_with_git_rep.png)

您也可以使用命令列工具來建立專案。 您可以選擇提供個人存取權杖。 Azure ML 可以使用此權杖來代表您存取 Git 存放庫，而非仰賴您的 AAD 認證：

```
# create a new project with a Git repo and personal access token.
$ az ml project create -a <experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <VSTS personal access token>
```
> [!IMPORTANT]
> 如果您選擇的 Git 存放庫已經有_主要_分支，就可以選擇空白的專案範本。 Azure ML 只會在本機複製_主要_分支，並將 `aml_config` 資料夾和其他專案中繼資料檔案新增到本機專案資料夾。 但如果您選擇任何其他專案範本，Git 存放庫就一定不能有_主要_分支，否則會發生錯誤。 替代方式為使用 `az ml project create` 命令列工具建立專案並提供 `--force` 切換。 這會刪除原始主要分支上的檔案，並以所選範本中的新檔案取代它們。

現在已建立新的 Azure ML 專案，而且啟用遠端 Git 存放庫整合，可以開始進行。 專案資料夾永遠 Git 初始化為本機 Git 存放庫。 Git「遠端」設定為遠端 VSTS Git 存放庫，因此可以將認可推送至遠端 Git 存放庫。

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>步驟 3a。 將現有的 Azure ML 專案與 VSTS Git 存放庫產生關聯
(選擇性) 您也可以建立不含 VSTS Git 存放庫的 Azure ML 專案，只依賴本機 Git 存放庫來取得執行歷程記錄快照集。 稍後，您可以使用下列命令，將 VSTS Git 存放庫與這個現有的 Azure ML 專案產生關聯：

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

> [!NOTE] 
> 您只能在沒有與它相關聯的 Git 存放庫之 Azure ML 專案上執行更新存放庫作業。 而且一旦 Git 存放庫相關聯後，就無法加以移除。

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>步驟 4. 在 Git 存放庫中擷取專案快照集
現在，您可以在專案中執行幾次指令碼，並於每次執行之間做些變更。 您可以從桌面應用程式來這麼做，或從 CLI 使用 `az ml experiment submit` 命令。 如需詳細資訊，請參閱[分類鳶尾花教學課程](tutorial-classifying-iris-part-1.md)。 每次執行時，如果專案資料夾中的任何檔案有任何變更，則會認可整個專案資料夾的快照集，並推送至名為 `AzureMLHistory/<Project_GUID>` 之分支下的遠端 Git 存放庫。 您可以瀏覽至 VSTS Git 存放庫 URL 並尋找此分之來檢視分支及認可。 

> [!NOTE] 
> 僅在指令碼執行之前才會認可快照集。 目前，資料準備執行或 Notebook 資料格執行不會觸發快照集。

![執行歷程記錄分支](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> 您最好不是使用 Git 命令在您自己的歷程記錄分支中運作。 如此一來，可能會弄亂執行歷程記錄。 請使用主要分支，或改為您自己 Git 作業的其他分支。

## <a name="step-5-restore-a-previous-project-snapshot"></a>步驟 5。 還原先前的專案快照集 
若要從 Azure ML Workbench 將整個專案資料夾還原至先前的執行歷程記錄專案狀態快照集的狀態：
1. 按一下活動列中的 [執行] (沙漏圖示)。
2. 從 [執行清單] 檢視中，按一下您想要還原的執行。
3. 從 [執行詳細資料] 檢視中，按一下 [還原]。

![執行歷程記錄分支](media/using-git-ml-project/restore_project.png)

或者，您可以從 Azure ML Workbench CLI 視窗中使用下列命令。

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

執行此命令會以該特定執行開始時所建立的快照集，覆寫整個專案資料夾。 但是，您的專案會維持在最新分支上。 這表示在目前專案資料夾中，您將會**遺失所有變更**。 因此，執行此命令時，請格外小心。 在執行上述作業之前，您需要 Git 以將變更認可到最新分支。 詳細資訊請見下文。

## <a name="step-6-use-the-master-branch"></a>步驟 6. 使用主要分支
為了避免不小心遺失目前的專案狀態，有一種作法是將專案認可到 Git 存放庫的主要分支 (或您自行建立的任何分支)。 您可以從命令列直接使用 Git (或選擇您慣用的其他 Git 用戶端工具) 來操作「主要」分支。 例如：

```sh
# check status to make sure you are on the master branch (or branch of your choice)
$ git status

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'these are my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

現在，您可以依照步驟 5，安心地將專案還原至較早的快照集，而且知道一定可以回到您剛才在主要分支上所做的認可。

## <a name="authentication"></a>驗證
如果您只是依賴 Azure ML 中的執行歷程記錄功能來建立專案快照集和進行還原，則不需要擔心 Git 存放庫驗證。 測試服務層會負責處理。

不過，如果您使用 Git 工具來管理版本控制，則必須在 VSTS 上適當地處理遠端 Git 存放庫的驗證。 在 Azure ML 中，遠端 Git 存放庫會新增至本機存放庫作為使用 HTTPS 通訊協定的 Git 遠端。 這表示當您發出 Git 命令至遠端 (例如推送或提取) 時，需要提供使用者名稱，以及密碼或個人存取權杖。 請遵循[這些指示](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate)，在 VSTS Git 存放庫中建立個人存取權杖。

## <a name="next-steps"></a>後續步驟
了解如何使用 Team Data Science Process 來組織專案結構，請參閱[使用 TDSP 建構專案](how-to-use-tdsp-in-azure-ml.md)
