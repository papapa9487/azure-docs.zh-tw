---
title: "Azure Machine Learning 預覽功能的教學課程文章 - 命令列介面  | Microsoft Docs"
description: "此教學課程逐步解說從命令列介面完成 Iris 分類端對端所需的所有步驟。"
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c2a3b9702afd99c29b64133a05515a1b5f395130
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>教學課程：使用命令列介面分類 Iris
Azure Machine Learning 服務 (預覽) 是一套整合性的端對端資料科學，以及進階分析解決方案，適用於專業資料科學家準備資料、開發實驗並部署雲規模模型。

在本教學課程中，您會了解如何使用 Azure Machine Learning 預覽功能中的命令列介面 (CLI)，以進行： 
> [!div class="checklist"]
> * 設定測試帳戶並建立工作區
> * 建立專案
> * 將實驗提交至多個計算目標
> * 升級並註冊定型模型
> * 部署 Web 服務以評分新資料

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件
- 您需要存取 Azure 訂用帳戶與權限，以建立該訂用帳戶中的資源。 
- 您必須遵循[安裝及建立快速入門](quickstart-installation.md)，以安裝 Azure Machine Learing Workbench 應用程式。 

  >[!NOTE]
  >您只需要在本機上安裝 Azure Machine Learning Workbench 即可。 您只需要遵循＜安裝 Azure Machine Learning Workbench＞一節中的步驟進行操作，因為帳戶建立以及建立新專案的步驟，將會透過本文中的命令行完成。
 
## <a name="getting-started"></a>開始使用
Azure Machine Learning 命令列介面 (CLI) 可讓您執行端對端資料科學工作流程所需的所有工作。 您可透過下列方式存取 CLI 工具：

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>選項 1。 從 Azure ML Workbench 登入對話方塊啟動 Azure ML CLI
當您啟動 Azure ML Workbench 並且第一次登入時，如果您還沒有測試帳戶的存取權，會出現下列畫面：

![找不到帳戶](media/tutorial-iris-azure-cli/no_account_found.png)

按一下對話方塊中的 [命令列視窗] 連結，以啟動命令列視窗。

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>選項 2。 從 Azure ML Workbench 應用程式啟動 Azure ML CLI
如果您已具備測試帳戶的存取權，便可以成功登入。 接著，您便可以按一下 [檔案] --> [開啟命令提示字元] 功能表，開啟命令列視窗。

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>選項 3。 在任意命令列視窗中啟用 Azure ML CLI
您也可以在任何命令列視窗中啟用 Azure ML CLI。 只要啟動命令視窗，並輸入下列命令：

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
若要進行永久變更，您可以在 Windows 上使用 `SETX`。 對於 macOS，您可以使用 `setenv`。

>[!TIP]
>您可以藉由設定上述環境變數，在您最愛的終端機視窗中啟用 Azure CLI。

## <a name="step-1-log-in-to-azure"></a>步驟 1. 登入 Azure
第一個步驟是從 AMLWorkbench 應用程式開啟 CLI (檔案 > 開啟命令提示字元)。 這麼做可確保我們使用正確的 Python 環境，而且有可用的 ML CLI 命令。 

然後，我們需要在 CLI 中設定正確的內容，以存取和管理 Azure 資源。
 
```bash
az login
az account set -s d128f140-94e6-1206-80a7-954b9d27d007
```

> [!TIP]
> 若要取得所有訂用帳戶的清單，請執行： 
>```
>az account list -o table
>```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>步驟 2. 建立新的 Azure Machine Learning 測試帳戶與工作區
我們首先會建立一個新的測試帳戶和一個新的工作區。 如需測試帳戶與工作區的詳細資料，請參閱 [Azure Machine Learning 概念](overview-general-concepts.md)。 

> [!NOTE]
> 測試帳戶需要儲存體帳戶，用來儲存實驗執行的輸出。 儲存體帳戶名稱在 Azure 中必須為全域唯一，因為沒有與其相關聯的 URL。 您的測試帳戶名稱用於代表您建立一個新的儲存體帳戶。 請務必使用唯一的名稱，否則您會收到錯誤，例如_「已使用名稱為 amlsampleexp 的儲存體帳戶。」_ 或者，您可以使用 `--storage` 引數，以使用現有的儲存體帳戶。

```bash
az group create --name amlsample --location eastus2
az ml account experimentation create --name amlsampleexp --resource-group amlsample
az ml account experimentation create --name amlsampleexp --resource-group amlsample --storage /subscriptions/6d48cffb-b787-47bd-8d20-1696afa33b67/resourceGroups/existing/providers/Microsoft.Storage/storageAccounts/mystorageacct
az ml workspace create --name amlsamplew --account amlsampleexp --resource-group amlsample
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>步驟 2.a (選用) 與同事共用工作區
我們將在這裡探討如何與同事共用工作區的存取權。 共用存取測試帳戶或專案的步驟是相同的。 只需要更新獲取 Azure 資源識別碼的方式。

```bash
az ml workspace show --name amlsamplew --account amlsampleexp --resource-group amlsample 
az role assignment create --assignee ahgyger@microsoft.com --role owner --scope "/subscriptions/d128f140-94e6-4175-87a7-954b9d27db16/resourceGroups/amlsample/providers/Microsoft.MachineLearningExperimentation/accounts/amlsampleexp/workspaces/amlsamplew"
```

> [!TIP]
> 您需要使用同事的實際電子郵件地址，而非別名。 

## <a name="step-3-create-a-new-project"></a>步驟 3. 建立新專案
下一個步驟是建立新專案。 有數種方式可以開始使用新專案。

### <a name="create-a-new-blank-project"></a>建立新的空白專案

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\
```

### <a name="create-a-new-project-with-template-files"></a>使用範本檔案建立新的專案
範本檔案不是範例，但這些檔案為您的新專案提供了一個框架。 專案已預先植入兩個檔案：`train.py` 和 `score.py`。

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>建立與雲端 Git 儲存機制相關聯的新專案
我們可以建立與雲端 Git 儲存機制相關聯的新專案。 每次提交實驗時，將會快照專案內容，以作為執行歷程記錄分支中的 Git 認可。 如需詳細資料，請參閱[將 Git 儲存機制與 Azure Machine Learning Workbench 專案搭配使用](using-git-ml-project.md)。

> [!NOTE]
> Azure Machine Learning 僅支援具有 Git 的空白 Team 專案 (VSTS) 作為版本控制。

> [!TIP]
> 如果您收到錯誤訊息「儲存機制 URL 可能無效或使用者可能沒有存取權」，您可以在 VSTS (安全性，新增個人存取權杖) 中建立安全性權杖，並在建立專案時，使用 __vststoken__ 引數。 

```bash
az ml project create --name 9_25_2 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --vststoken m2fholwwrcn7u4nrdqfxx007u5rztjfhgofnemvuvtue6pbwo3sa
```

### <a name="sample_create"></a>從線上範例建立新專案
在此範例中，我們使用 Git 中樞專案中的範本，並在建立新專案時使用該範本。 

```bash
# List the project samples
az ml project sample list

# Create a new project from a sample
az ml project create --name 9_25_3 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```

建立專案後，請先將您的目錄變更為該專案，然後再進行下一步。

```bash
cd c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\9_25_1
```

## <a name="step-4-run-the-training-experiment"></a>步驟 4 執行訓練實驗 
下列步驟假設您具有包含 Iris 範例的專案 (請參閱[從線上範例建立新專案](#sample_create))。

### <a name="prepare-your-environment"></a>準備您的環境 
對於 Iris 範例，我們需要安裝 matplotlib。

```bash
pip install matplotlib
```

###  <a name="submit-the-experiment"></a>提交實驗

```bash
# Execute the file
az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>隨著遞減的正規化速率反覆執行實驗
運用一些創意，只要將 python 指令碼放在一起，即可透過不同的正規化速率提交實驗。 (您可能需要編輯檔案，以指向正確的專案路徑。)

```bash
python run.py
```

## <a name="step-5-view-run-history"></a>步驟 5。 檢視執行歷程記錄
下列命令會列出先前執行的記錄。 

```bash
az ml history list -o table
```
執行上述命令會顯示屬於此專案的所有執行清單。 您也可以看到精確度和正規化速率指標也一併列出。 這可以輕鬆地從清單中找出最佳執行。

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>步驟 5.a 檢視由特定執行建立的附件 
若要檢視特定執行相關聯的附件，我們可以使用執行歷程記錄的資訊命令。

```bash
az ml history info --run 9_16_4_1505589545267 --artifact driver_log
```

若要從執行下載構件，您可以使用下列命令：

```bash
# Stream a given attachment 
az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>步驟 6. 升級執行的構件 
我們所做的其中一個執行有更好的 AUC；因此，我們想利用它建立一個評分 Web 服務，以部署到生產。 若要這麼做，我們必須先將構件升級至資產。

```bash
az ml history promote --run 9_25_1505346632975 --artifact-path outputs/model.pkl --name model.pkl
```

這將在您的專案目錄中建立一個包含 pickle.link 的__資產__資料夾。 此連結檔案用來追蹤升級檔案的版本。

## <a name="step-7-download-the-files-to-be-operationalized"></a>步驟 7. 下載要實際運作的檔案
我們現在需要下載升級的檔案，讓我們能夠使用這些檔案來建立預測 Web 服務。 

```bash
az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>步驟 8。 設定您的模型管理環境 
我們會建立一個環境以部署 Web 服務。 我們可以使用 Docker 在本機電腦上執行 Web 服務。 或將其部署至 ACS 叢集，以進行可高度調整的作業。 

```bash
# Create new environment
az ml env setup -l eastus2 -n amlsamplesenv
# Once setup is complete, set your environment for current context
az ml env set -g amlsamplesenvrg -n amlsamplesenv
```

## <a name="step-9-create-a-model-management-account"></a>步驟 9. 建立模型管理帳戶 
需要有模型管理帳戶，才能部署及追蹤生產環境中的模型。 

```bash
az ml account modelmanagement create -n amlsamplesacct -g amlsamplesenvrg -l eastus2
```

## <a name="step-10-create-a-web-service"></a>步驟 10. 建立 Web 服務
然後，建立 Web 服務，使用我們部署的模型傳回預測。 

```bash
az ml service create realtime -m modelfilename -f score.py -r python –n amlsamplews
```

## <a name="step-10-run-the-web-service"></a>步驟 10. 執行 Web 服務
使用來自上一步驟之輸出的 Web 服務識別碼，我們可以呼叫 Web 服務並進行測試。 

```
# Get web service usage infomration
az ml service usage realtime -i <web service id>

# Call the web service with the run command:
az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>刪除所有資源 
刪除我們建立的所有資源以完成本教學課程，除非您想要繼續進行此作業！ 

若要這麼做，只需刪除包含所有資源的資源群組即可。 

```bash
az group delete --name amlsample
az group delete --name amlsamplesenvrg
```

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已經了解如何使用 Azure Machine Learning 預覽功能以 
> [!div class="checklist"]
> * 設定測試帳戶並建立工作區
> * 建立專案
> * 將實驗提交至多個計算目標
> * 升級並註冊定型模型
> * 為模型管理建立模型管理帳戶
> * 建立部署 Web 服務的環境
> * 部署 Web 服務並評分新資料


