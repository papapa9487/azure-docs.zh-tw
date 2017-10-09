---
title: "在 Azure Machine Learning Workbench 中漫遊及共同作業 | Microsoft Docs"
description: "已知問題清單和協助疑難排解的指南"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 156dd1b7f928df22b3feb9e7a13396d3b53a91d7
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>在 Azure Machine Learning Workbench 中漫遊及共同作業
本文件將引導您 Azure Machine Learning Workbench 如何跨電腦協助專案漫遊，以及啟用與小組成員的共同作業。 

當您使用遠端 Git 存放庫 (存放庫) 連結建立 Azure Machine Learning 專案時，專案中繼資料和快照集會儲存在雲端中。 雲端連結可讓您從不同的電腦存取專案 (漫遊)。 您也可以提供同事存取權，從而進行共同作業。 

## <a name="prerequisites"></a>必要條件
首先，使用測試帳戶的存取權安裝 Azure Machine Learning Workbench。 如需詳細資訊，請遵循[安裝指南](quickstart-installation.md)。

其次，存取 [Visual Studio Team System](https://www.visualstudio.com)，並建立您專案要連結的存放庫。 如需 Git 的詳細資訊，請參考[使用 Git 存放庫搭配 Azure Machine Learning Workbench](using-git-ml-project.md) 文章。

## <a name="create-a-new-azure-machine-learning-project"></a>建立新的 Azure Machine Learning 專案
啟動 Azure Machine Learning Workbench，並建立新的專案 (例如，_鳶尾花_)。 將有效的 VSTS Git 存放庫 URL 填入 **Visualstudio.com GIT 存放庫 URL** 文字方塊中。 
>[!IMPORTANT]
>如果您沒有 Git 存放庫的讀/寫存取權且 Git 存放庫不是空的，專案建立就會失敗，也就是它已經有主要分支。

一旦建立專案後，在專案內的任何指令碼上提交幾個執行。 這個動作會將遠端 Git 存放庫的執行歷程記錄分支認可至專案狀態。 

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

>請注意，萬一您在 Azure ML 目錄中的資料夾與專案有完全相同的名稱，下載就會失敗。 目前，您必須將現有的資料夾重新命名，以便解決此問題。


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

# Add Bob to the Experimentation Account as a Reader.
# Bob now has read access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Reader --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as a Contributor.
# Bob now has read/write access to all projects under the Workspace by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <workspace ARM ID>

# find ARM ID of the project 
az ml project show --query "id"

# Add Bob to the Project as an Owner.
# Bob now has read/write access to the Project, and can add others too.
az role assignment create --assignee bob@contoso.com --role Owner --scope <project ARM ID>
```

角色指派之後，Bob 可以直接或透過繼承看到 Workbench 專案清單中的專案。 應用程式可能需要重新啟動，才能看到專案。 接著，Bob 就可以下載專案，如[漫遊區段](#roaming)中所述，並與 Alice 共同作業。 

在專案上共同作業的所有使用者之執行歷程記錄會認可到相同的遠端 Git 存放庫。 因此，當 Alice 執行執行時，Bob 就可以在 Workbench 應用程式的執行歷程記錄區段中看到執行。 Bob 也可以將專案還原成任何執行的狀態，包括 Alice 所啟動的執行。 

共用專案的遠端 Git 存放庫可讓 Alice 和 Bob 也能在主要分支上共同作業。 如有需要，他們也可以建立個人分支，並使用 Git 提取要求及合併來共同作業。 

### <a name="using-azure-portal-to-add-users"></a>使用 Azure 入口網站來新增使用者
<a name="portal"></a>

Azure Machine Learning 測試帳戶、工作區及專案都是 Azure Resource Manager 資源。 您可以使用 [Azure 入口網站](https://portal.azure.com)中的 [存取控制] 連結來指派角色。 

從 [所有資源] 檢視找出您要新增使用者的資源。 按一下頁面內的 [存取控制] (IAM) 連結。 新增使用者 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">


