---
title: "在 Azure Machine Learning Workbench 中漫遊及共同作業 | Microsoft Docs"
description: "已知問題清單和協助疑難排解的指南"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 50f48fb096cb907e050769a8a4159689eb25418c
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>在 Azure Machine Learning Workbench 中漫遊及共同作業
本文件將引導您 Azure Machine Learning Workbench 如何跨電腦協助專案漫遊，以及啟用與小組成員的共同作業。 

當您使用遠端 Git 存放庫 (存放庫) 連結建立 Azure Machine Learning 專案時，專案中繼資料和快照集會儲存在雲端中。 雲端連結可讓您從不同的電腦存取專案 (漫遊)。 您也可以提供同事存取權，從而進行共同作業。 

## <a name="prerequisites"></a>必要條件
首先，使用測試帳戶的存取權安裝 Azure Machine Learning Workbench。 如需詳細資訊，請遵循[安裝指南](quickstart-installation.md)。

其次，存取 [Visual Studio Team System](https://www.visualstudio.com)，並建立您專案要連結的存放庫。 如需 Git 的詳細資訊，請參考[使用 Git 存放庫搭配 Azure Machine Learning Workbench](using-git-ml-project.md) 文章。

## <a name="create-a-new-azure-machine-learning-project"></a>建立新的 Azure Machine Learning 專案
啟動 Azure Machine Learning Workbench，並建立新的專案 (例如，_鳶尾花_)。 將有效的 VSTS Git 存放庫 URL 填入 **Visualstudio.com GIT 存放庫 URL** 文字方塊中。 

> [!IMPORTANT]
> 如果您選擇的 Git 存放庫已經有 _master_ 分支，就可以選擇空白的專案範本。 Azure ML 只會在本機複製 _master_ 分支，並將 `aml_config` 資料夾和其他專案中繼資料檔案新增到本機專案資料夾。 但如果您選擇任何其他專案範本，Git 存放庫就一定不能有 _master_ 分支，否則會發生錯誤。 替代方式為使用 `az ml project create` 命令列工具建立專案並提供 `--force` 參數。 這會刪除原始 master 分支上的檔案，並以所選範本中的新檔案取代它們。

一旦建立專案後，在專案內的任何指令碼上提交幾個執行。 這個動作會將遠端 Git 存放庫的執行歷程記錄分支認可至專案狀態。 

> [!NOTE] 
> 只有執行觸發程序的指令碼會認可至執行歷程記錄分支。 資料準備執行或 Notebook 執行不會在執行歷程記錄分支上觸發專案快照集。

如果您已安裝 Git 驗證，也可以明確地在主要分支中操作，或建立新的分支。 

例如： 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>漫遊
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>在次要電腦上開啟 Azure Machine Learning Workbench
一旦 VSTS Git 存放庫與您的專案連結後，就可以從已安裝 Azure Machine Learning Workbench 的任何電腦存取_鳶尾花_專案。 

若要在另一部電腦上存取鳶尾花專案，您必須使用建立專案時所使用的相同認證來登入應用程式。 此外，您必須瀏覽至相同的測試帳戶和工作區。 _鳶尾花_專案會依字母順序與工作區內的其他專案一起列出。 

### <a name="download-project-on-second-machine"></a>下載次要電腦上的專案
在次要機器上開啟工作區時，與_鳶尾花_專案相鄰的圖示會與典型資料夾圖示不同。 下載圖示表示專案的內容位於雲端中，且必須先下載到目前的電腦。 

![建立專案](./media/roaming-and-collaboration/downloadable-project.png)

按一下 _iris_ 專案會開始下載動作。 過一會兒，當下載完成時，專案已準備好在次要電腦上進行存取。 

在 Windows 上，它是 `C:\Users\<username>\Documents\AzureML`

在 macOS 上，是在這裡：`/home/<username>/Documents/AzureML`

在未來版本中，我們計劃增強功能，讓您可選取目的地資料夾。 

> [!NOTE]
> 萬一您在 Azure ML 目錄中的資料夾與專案有完全相同的名稱，下載就會失敗。 目前，您必須將現有的資料夾重新命名，以便解決此問題。


### <a name="work-on-the-downloaded-project"></a>使用下載的專案 
新下載的專案會反映上次執行專案中的專案狀態。 每次提交執行時，專案狀態的快照集會自動認可到 VSTS Git 存放庫中的執行歷程記錄分支。 在次要電腦上具現化專案時，我們會使用與最新執行相關聯的快照集。 
 

## <a name="collaboration"></a>共同作業
您可以在連結到 VSTS Git 存放庫的專案上與小組成員共同作業。 您可以指派權限給測試帳戶、工作區及專案的使用者。 此時，您可以使用 Azure CLI 來執行 Azure Resource Manager 命令。 您也可以使用 [Azure 入口網站](https://portal.azure.com)。 請參閱[下列各節](#portal)。    

### <a name="using-command-line-to-add-users"></a>使用命令列來新增使用者
讓我們使用範例。 假設 Alice 是 e_Iris_ 專案的擁有者，且她需要與 Bob 共用存取。 

Alice 按一下 [檔案] 功能表，並選取 [命令提示字元] 功能表項目，隨即啟動設定為_鳶尾花_專案的命令提示字元。 接著，Alice 就能決定要授與 Bob 何種層級的存取權，方法是執行下列命令。  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

角色指派之後，Bob 可以直接或透過繼承看到 Workbench 專案清單中的專案。 應用程式可能需要重新啟動，才能看到專案。 接著，Bob 就可以下載專案，如[漫遊區段](#roaming)中所述，並與 Alice 共同作業。 

在專案上共同作業的所有使用者之執行歷程記錄會認可到相同的遠端 Git 存放庫。 因此，當 Alice 執行執行時，Bob 就可以在 Workbench 應用程式的執行歷程記錄區段中看到執行。 Bob 也可以將專案還原成任何執行的狀態，包括 Alice 所啟動的執行。 

共用專案的遠端 Git 存放庫可讓 Alice 和 Bob 也能在主要分支上共同作業。 如有需要，他們也可以建立個人分支，並使用 Git 提取要求及合併來共同作業。 

### <a name="using-azure-portal-to-add-users"></a>使用 Azure 入口網站來新增使用者
<a name="portal"></a>

Azure Machine Learning 測試帳戶、工作區及專案都是 Azure Resource Manager 資源。 您可以使用 [Azure 入口網站](https://portal.azure.com)中的 [存取控制] 連結來指派角色。 

從 [所有資源] 檢視找出您要新增使用者的資源。 按一下頁面內的 [存取控制] (IAM) 連結。 新增使用者 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>共同作業工作流程範例
讓我們透過一個逐步說明範例了解共同作業流程。 Contoso 員工 Alice 和 Bob 想要使用 Azure ML Workbench 在資料科學專案上共同作業。 他們的身分識別屬於相同的 Contoso Azure AD 租用戶。

1. Alice 先在 VSTS 專案中建立了一個空白的 Git 存放庫。 此 VSTS 專案應存在於 Contoso AAD 租用戶建立的 Azure 訂用帳戶中。 

2. 然後 Alice 在她的電腦上建立了一個 Azure ML 實驗帳戶、一個工作區，以及一個 Azure ML Workbench 專案。 她在建立專案時提供了 Git 存放庫 URL。

3. Alice 開始在專案上工作。 她建立了一些指令碼並執行了幾個執行。 系統會針對每個執行，將整個專案資料夾的快照集自動推入 Workbench 建立之 VSTS Git 存放庫的執行歷程記錄分支中，以作為認可。

4. Alice 現在已經很順利地進行工作。 她想要認可本機 _master_ 分支中的變更，並將變更推入 VSTS Git 存放庫 _master_ 分支中。 若要這樣做，她可以在專案開啟的情況下，從 Azure ML Workbench 啟動命令提示字元視窗，並發出下列命令：
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. 然後 Alice 將 Bob 新增到工作區作為參與者。 她可以從 Azure 入口網站這樣做，或使用上面說明的 `az role assignment` 命令。 她也將 VSTS Git 存放庫的讀取/寫入存取權授與 Bob。

6. Bob 現在在他自己的電腦上登入 Azure ML Workbench。 他可以看到 Alice 與他共用的工作區，以及該工作區底下列出的專案。 

7. Bob 按一下專案名稱，就會將專案下載到他的電腦。
    
    a. 下載的專案檔是執行歷程記錄中所記錄最近一次執行的快照集複製品。 它們並不是 master 分支中最新的認可。
    
    b. 本機專案資料夾會設定在 _master_ 分支上，並含有取消暫存的變更。

8. Bob 現在可以瀏覽 Alice 所執行的執行，以及還原任何之前執行的快照集。

9. Bob 想要取得 Alice 推送的最新變更，並開始在不同的分支上工作。 因此他從 Azure ML Workbench 開啟了命令提示字元視窗，然後執行下列命令：

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. Bob 現在修改專案並提交新的執行。 變更會在 _bob_ 分支上完成。 而且 Alice 也可以看到 Bob 的執行。

11. Bob 現在可以將他所做的變更推送到遠端 Git 存放庫中了。 為了避免與 Alice 工作所在的 _master_ 分支衝突，Bob 決定將他的工作推送到也稱為 _bob_ 的新遠端分支。

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. 然後 Bob 可以將他的程式碼中有哪些新酷炫玩意兒的相關資訊告知 Alice，並在遠端 Git 存放庫上建立從 _bob_ 分支到 _master_ 分支的提取要求。 然後 Alice 可以將提取要求合併到 _master_ 分支中。

## <a name="next-steps"></a>後續步驟
深入了解搭配 Azure ML Workbench 使用 Git 的相關資訊：[使用 Git 存放庫搭配 Azure Machine Learning Workbench 專案](using-git-ml-project.md)