---
title: "Azure Machine Learning 服務的安裝快速入門 | Microsoft Docs"
description: "本快速入門說明如何建立 Azure Machine Learning 資源，以及如何安裝 Azure Machine Learning Workbench。"
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: hero-article
ms.date: 09/20/2017
ms.openlocfilehash: bb3ac21d2e8b8efd7b4f6943fcc3256f18cbdd68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>建立 Azure Machine Learning 預覽帳戶，並安裝 Azure Machine Learning Workbench
Azure Machine Learning 是一套整合的端對端資料科學以及進階分析解決方案，可供專業資料科學家用來以雲端規模準備資料、開發測試以及部署模型。

本快速入門示範如何在 Azure Machine Learning 服務預覽中建立測試與模型管理帳戶。 它也會顯示如何安裝 Azure Machine Learning Workbench 桌面應用程式和 CLI 工具。 接下來，您可以使用不受時間影響的[鳶尾花資料集](https://en.wikipedia.org/wiki/iris_flower_data_set)快速導覽 Azure Machine Learning 預覽功能來建置模型，以根據部分實體特性預測鳶尾花的類型。  

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件
Azure Machine Learning Workbench 桌面應用程式目前只能安裝在下列作業系統上： 
- Windows 10
- Windows Server 2016
- macOS Sierra

>[!NOTE]
>目前不支援 macOS High Sierra。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-azure-machine-learning-accounts"></a>建立 Azure Machine Learning 帳戶
使用 Azure 入口網站來佈建 Azure Machine Learning 帳戶。 
1. 選取入口網站左上角的 [新增] 按鈕 (+)。

2. 在 [搜尋] 列中輸入「機器學習」。 選取名為 **Machine Learning 測試 (預覽)** 的搜尋結果。  按一下星星圖示，讓此選取項目成為 Azure 入口網站中的我的最愛。

   ![Azure Machine Learning 搜尋](media/quickstart-installation/portal-more-services.png)

3. 按一下 [+ 新增] 來設定新的 Machine Learning 測試帳戶。 詳細表單隨即開啟。

   ![Machine Learning 測試帳戶](media/quickstart-installation/portal-create-experimentation.png)

4. 使用下列資訊填寫 Machine Learning 測試表單：

   設定|建議的值|說明
   ---|---|---
   測試帳戶名稱 | _唯一的名稱_ |選擇可識別您帳戶的唯一名稱。 您可以使用您自己的名稱，或最能識別測試的部門或專案名稱。 名稱長度必須介於 2 到 32 個字元，僅包括英數字元和 '-' 虛線字元。 
   訂用帳戶 | _您的訂用帳戶_ |您要用於測試的 Azure 訂用帳戶。 如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。
   資源群組 | _您的資源群組_ | 您可以產生新的資源群組名稱，或使用您訂用帳戶中現有的資源群組名稱。
   位置 | 最接近使用者的區域 | 選擇最接近您的使用者與資料資源的位置。
   基座數目 | 2 | 輸入基座數目。 此選項會影響[定價](https://azure.microsoft.com/pricing/details/machine-learning/)。 前兩個基座是免費的。 基於本快速入門的目的，我們使用兩個基座。 您稍後可以視需要在 Azure 入口網站中更新基座數目。
   儲存體帳戶 | _唯一的名稱_ | 選擇 [建立新的] 並提供名稱以建立新的 Azure 儲存體帳戶，或選擇 [使用現有的] 並從下拉式清單中選取現有的儲存體帳戶。 需要儲存體帳戶，並且是用來儲存專案構件並執行歷程記錄資料。 
   測試帳戶的工作區 | _唯一的名稱_ | 提供新工作區的名稱。 名稱長度必須介於 2 到 32 個字元，僅包括英數字元和 '-' 虛線字元。
   指派工作區的擁有者 | _您的帳戶_ | 選取您自己的帳戶作為工作區擁有者。
   建立模型管理帳戶 | *檢查* | 作為測試帳戶建立體驗的一部分，您也可以選擇建立 Machine Learning 模型管理帳戶。 在您在準備好要將您的模型部署和管理為即時 Web 服務時，會使用此資源。 建議您在與測試帳戶的同時建立模型管理帳戶。
   帳戶名稱 | _唯一的名稱_ | 選擇可識別您模型管理員帳戶的唯一名稱。 您可以使用您自己的名稱，或最能識別測試的部門或專案名稱。 名稱長度必須介於 2 到 32 個字元，僅包括英數字元和 '-' 虛線字元。 
   模型管理定價層 | **DEVTEST** | 按一下 [未選取任何定價層] 來指定新模型管理帳戶的定價層。 為了節省成本，請選取 **DEVTEST** 定價層 (如果在您的訂用帳戶上可用，限量提供)，否則請選取 S1 定價層以節省成本。 按一下 [選取] 以儲存定價層選取項目。 
   釘選到儀表板 | _檢查_ | 選取 [釘選到儀表板] 選項，在 Azure 入口網站的前儀表板頁面上輕鬆追蹤 Machine Learning 測試帳戶。

5. 按一下 [建立] 來開始建立程序。

6. 在 Azure 入口網站工具列的右上角，按一下 [通知] (鈴鐺圖示) 來監視部署程序。 

   通知會顯示「部署進行中...」。 一旦完成，狀態會變更為「部署已成功」。 Machine Learning 測試帳戶頁面會在成功時開啟。
   
   ![Azure 入口網站通知](media/quickstart-installation/portal-notification.png)

現在，根據您在本機電腦上使用的作業系統，請遵循下面兩個區段中的一個，在您的電腦上安裝 Azure Machine Learning Workbench。 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>在 Windows 上安裝 Azure Machine Learning Workbench
在執行 Windows 10、Windows Server 2016 或更新版本的電腦上安裝 Azure Machine Learning Workbench。

1. 下載最新的 Azure Machine Learning Workbench 安裝程式 **[AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi)**。

2. 從檔案總管按兩下所下載的安裝程式 _AmlWorkbenchSetup.msi_。

   >[!IMPORTANT]
   >在磁碟上完整下載安裝程式，然後從該處啟動它。 不要直接在瀏覽器的下載小工具中啟動它。

3. 遵循螢幕上的指示完成安裝。

   安裝程式會下載所有必要的相依元件，例如 Python、Miniconda 及其他相關程式庫。 安裝可能需要約半小時才能完成所有元件。 

4. Azure Machine Learning Workbench 現在已安裝在下列目錄：
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>在 macOS 上安裝 Azure Machine Learning Workbench
在執行 macOS Sierra 的電腦上安裝 Azure Machine Learning Workbench。

1. 使用[Homebrew](http://brew.sh) 安裝 openssl 程式庫。 如需詳細資訊，請參閱 [Mac 上的 .NET Core 的必要條件](https://docs.microsoft.com/dotnet/core/macos-prerequisites)。
   ```
   # install Homebrew first if you don't have it already
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

   # install latest openssl needed for .NET Core 1.x
   brew update
   brew install openssl
   mkdir -p /usr/local/lib
   ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
   ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
   ```

2. 下載最新的 Azure Machine Learning Workbench 安裝程式 **[AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg)**。

   >[!IMPORTANT]
   >在磁碟上完整下載安裝程式，然後從該處啟動它。 不要直接在瀏覽器的下載小工具中啟動它。

3. 從 Finder 按兩下所下載的安裝程式 _AmlWorkbench.dmg_。

4. 遵循螢幕上的指示完成安裝。

   安裝程式會下載所有必要的相依元件，例如 Python、Miniconda 及其他相關程式庫。 安裝可能需要約半小時才能完成所有元件。 

5. Azure Machine Learning Workbench 現在已安裝在下列目錄： 

   `/Applications/AmlWorkbench.app`

## <a name="run-azure-machine-learning-workbench-to-log-in-the-first-time"></a>執行 Azure Machine Learning Workbench 以第一次登入
1. 一旦安裝程序完成，請按一下安裝程式的最後一個畫面上的 [啟動 Workbench] 按鈕。 如果您已關閉安裝程式，請在桌面和 [開始] 功能表上尋找名為 **Azure Machine Learning Workbench** 的 Machine Learning Workbench 捷徑，來啟動應用程式。

2. 使用您稍早用來佈建您的 Azure 資源的相同帳戶登入 Workbench。 

3. 登入程序成功時，Workbench 會嘗試尋找您稍早建立的 Machine Learning 測試帳戶。 它會搜尋您的認證具有存取權的所有 Azure 訂用帳戶。 找到至少一個測試帳戶時，即會使用該帳戶開啟 Workbench。 然後它會列出在該帳戶中找到的工作區和專案。 

   >[!TIP]
   > 如果您有一個以上的測試帳戶的存取權，可以按一下 Workbench 應用程式左下角的圖像圖示，切換為另一個帳戶。

請參閱[部署環境設定](deployment-setup-configuration.md)，以建立用於部署 Web 服務的環境。

## <a name="create-a-new-project"></a>建立新專案
1. 啟動 Azure ML Workbench 應用程式並登入。 

2. 按一下 [檔案] --> [新專案] (或是按一下 **+** 登入 [專案] 窗格)。 

3. 填寫 [專案名稱] 和 [專案目錄] 欄位。 [專案描述] 為選擇性，但是很有幫助。 目前將 **Visualstudio.com GIT 儲存機制 URL** 欄位保持空白。 選擇工作區，然後選取 [分類鳶尾花] 作為專案範本。

   >[!TIP]
   >(選擇性) 您可以使用 [VSTS (Visual Studio Team Service)](https://www.visualstudio.com) 專案中裝載的 Git 儲存機制的 URL 來填入 Git 儲存機制文字欄位。 這個 Git 儲存機制必須已經存在，且必須是空的且沒有主要分支。 而且，您必須具有其寫入權限。 現在新增 Git 儲存機制，可讓您稍後啟用漫遊及共用案例。 [閱讀更多資訊](using-git-ml-project.md)。

4. 按一下 [建立] 按鈕以建立專案。 隨即為您建立並開啟新的專案。 此時，您可以瀏覽專案首頁、資料來源、筆記本、原始程式檔。 

    >[!TIP]
    >您也可以僅僅藉由設定 IDE (整合式開發環境) 連結，在 VS Code 或其他編輯器中開啟專案，然後再開啟其中的專案目錄。 [閱讀更多資訊](how-to-configure-your-IDE.md)。 

## <a name="run-a-python-script"></a>執行 Python 指令碼
讓我們在本機電腦上執行指令碼。 

1. 每個專案會開啟到自己的 [專案儀表板] 頁面。 從接近應用程式最上方的命令列 [執行] 按鈕的左邊選取 `local` 作為執行目標，以及 `iris_sklearn.py` 作為要執行的指令碼。  範例中包含一些其他檔案，可供您稍後簽出。 

   ![影像](media/quickstart-installation/run_control.png)

2. 在 [引數] 文字欄位中輸入 `0.01`。 這個數字用於程式碼中，以設定正則化速率，這是一個值，用來設定線性迴歸模型定型的方式。 

3. 按一下 [執行] 按鈕，在您的電腦上開始執行 `iris_sklearn.py`。 

   此程式碼使用來自受歡迎的 Python [scikit-learn](http://scikit-learn.org/stable/index.html) 程式庫的[羅吉斯迴歸](https://en.wikipedia.org/wiki/logistic_regression)演算法來建置模型。

4. [作業] 面板會從右邊滑出 (如果尚不可見)，並且 `iris_sklearn` 作業會在面板中新增。 隨著作業開始執行，其狀態會從**提交中**轉換為**執行中**，然後在幾秒後變成**已完成**。 

5. 恭喜！ 您已成功在 Azure ML Workbench 中執行 Python 指令碼。

6. 重複步驟 2-4 數次。 每次都使用不同的引數值，範圍從 `10` 至 `0.001`。

## <a name="view-run-history"></a>檢視執行歷程記錄
1. 瀏覽至 [執行] 檢視，然後按一下執行清單中的 **iris_sklearn.py**。 `iris_sklearn.py` 的執行歷程記錄儀表板隨即開啟。 它會顯示在 `iris_sklearn.py` 上的每次執行。 

   ![影像](media/quickstart-installation/run_view.png)

2. 執行歷程記錄儀表板也會顯示最上層度量、一組預設圖形和每個執行度量的清單。 您可以藉由按一下設定圖示或篩選圖示來排序、篩選和調整設定，即可自訂這個檢視。

   ![影像](media/quickstart-installation/run_dashboard.png)

3. 按一下已完成的執行，您可以看到該特定執行的詳細檢視，包括其他度量、它所產生的檔案和其他可能有用的記錄檔。

## <a name="next-steps"></a>後續步驟
您現在已成功建立 Azure Machine Learning 測試帳戶和 Azure Machine Learning 模型管理帳戶。 您已安裝 Azure Machine Learning Workbench 桌面應用程式和命令列介面。 您已透過執行指令碼建立新的專案、建立模型，並探索指令碼的執行歷程記錄。

如需此工作流程的更深入體驗，包括如何將鳶尾花模型部署為 Web 服務，請遵循包含[資料準備](tutorial-classifying-iris-part-1.md)、[測試](tutorial-classifying-iris-part-2.md)和[模型管理](tutorial-classifying-iris-part-3.md)詳細步驟的完整分類鳶尾花教學課程。 

> [!div class="nextstepaction"]
> [分類鳶尾花教學課程](tutorial-classifying-iris-part-1.md)
