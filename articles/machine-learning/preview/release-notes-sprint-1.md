---
title: "Sprint 1 2017 年 11 月的 Azure ML Workbench 版本資訊"
description: "本文件詳述 Azure ML 的 Sprint 1 版本更新"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: a4945c77be5763ffeda328184149f712572937c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="sprint-1---november-2017"></a>Sprint 1 - 2017 年 11 月 

**版本號碼：0.1.1710.31013**

歡迎使用 Azure Machine Learning Workbench 的第二次更新。 我們持續改善 Workbench 應用程式、CLI 和後端服務層的安全性、穩定性和可維護性。 非常感謝您傳送的肯定 (笑臉) 與不滿意 (苦臉)。 以下的更新有許多是直接來自您的意見反應。 請繼續提供您的意見！

## <a name="notable-new-features"></a>重要新功能
- 現在有兩個新的 Azure 區域提供 Azure ML：**西歐**和**東南亞**。 它們原先加入的區域是**美國東部 2**、**美國中西部**和**澳洲東部**，現在部署區域總數增加至五個。
- 我們在 Workbench 應用程式中啟用了 Python 程式碼語法醒目提示，讓您更容易閱讀及編輯 Python 原始程式碼。 
- 您現在可以直接從檔案啟動您最愛的 IDE，而不用從整個專案中啟動。  在 Workbench 中開啟檔案，然後按一下 [編輯]，即可啟動目前檔案和專案的 IDE (目前支援 VS Code 和 PyCharm)。  您也可以按一下 [編輯] 按鈕旁邊的箭號，在 Workbench 文字編輯器中編輯檔案。  為防止意外變更，在您按下 [編輯] 之前，檔案都是唯讀的。
- Workbench 應用程式內附熱門的繪圖程式庫 `matplotlib` 2.1.0 版。
- 我們已將資料準備引擎升級至 .NET Core 2.0 版。 這會在 macOS 上安裝應用程式期間移除 brew-install openssl 需求。 這也是在為日後更有趣的資料準備功能鋪路。 
- 我們已啟用特定版本的應用程式首頁，方便您根據目前的應用程式版本取得更接近的版本資訊及更新提示。
- 即使您的本機使用者名稱中有空格，現在也可以成功安裝應用程式。 

## <a name="detailed-updates"></a>詳細的更新
以下是 Azure Machine Learning 此 Sprint 中各元件區域的詳細更新清單。

### <a name="installer"></a>安裝程式
- 應用程式安裝程式現在會清除舊版應用程式建立的安裝目錄。
- 修正導致安裝程式在 macOS High Sierra 中卡在 100% 的 Bug。
- 如果安裝失敗，使用者現在可以直接連結到安裝目錄，檢閱安裝程式記錄檔。
- 使用者名稱中有空格的使用者現在也可以順利安裝。

### <a name="workbench-authentication"></a>Workbench 驗證
- 支援 Proxy 管理員的驗證。
- 位於防火牆後方使用者現在可以順利登入。 
- 如果使用者在多個 Azure 區域有試驗帳戶，而某個區域剛好無法使用，應用程式不會再停止回應。
- 當驗證未完成，而且仍然顯示驗證對話方塊時，應用程式不會再嘗試從本機快取載入工作區。

### <a name="workbench-app"></a>Workbench 應用程式
- 文字編輯器啟用 Python 程式碼語法醒目提示。
- 文字編輯器中的 [編輯] 按鈕可讓您在 IDE (支援 VS Code 和 PyCharm) 或內建文字編輯器中編輯檔案。
- 文字編輯器預設為唯讀模式。 
- [儲存] 按鈕視覺狀態現在在檔案儲存後會變更成停用，因此不會再變更。
- Workbench 會在您起始執行時，儲存_所有_未儲存的檔案。
- Workbench 會記住上次本機電腦使用過的工作區，自動開啟。
- 目前只允許執行單一的 Workbench 執行個體。 以前可以啟動多個執行個體，但會在操作相同專案時發生問題。
- [檔案] 功能表的 [開啟專案] 重新命名為 [Add Existing Folder as Project] (將現有資料夾新增為專案) 
- 索引標籤切換速度變快。
- 說明連結新增到 [Configuring IDE] (設定 IDE) 對話方塊中。
- 意見反應表單現在會記住您上次輸入的電子郵件地址。
- 肯定 (笑臉) 與不滿意 (苦臉) 表單的文字區域現在已加大，所以您可以傳送給我們更多的意見！ 
- 已更正 `az ml workspace create` 的 `--owner` 切換說明文字。
- 新增的 [關於] 對話方塊方便使用者輕鬆檢視及複製應用程式的版本號碼。
- [建議功能] 功能表項目已新增至 [說明] 功能表。
- 應用程式標題列現在會顯示試驗帳戶名稱，位在應用程式名稱 "Azure Machine Learning Workbench" 的前面。
- 現在會根據偵測到的應用程式版本，顯示特定版本的應用程式首頁。

### <a name="data-preparation"></a>資料準備 
- 不再從地圖偵測器載入外部網站，以避免潛在的安全性問題。
- 長條圖和值計數偵測器現在可以選擇顯示對數刻度圖表。
- 現在在進行計算時，資料品質列會顯示不同的色彩，以表示「計算」狀態。
- 資料行計量現在會顯示類別值資料行的統計資料。
- 不再截斷資料來源名稱中的最後一個字元。
- 切換索引標籤、產生顯著的效能提升時，資料準備套件現在會保持開啟狀態。
- 在資料來源中切換資料檢視和計量檢視時，不再變更資料行的順序。
- 開啟無效的 `.dprep` 或 `.dsource` 檔案，不會再造成 Workbench 損毀。
- 資料準備套件現在可以使用相對路徑處理 [寫入至 CSV] 轉換的輸出。
- [保留資料行] 轉換現在可讓使用者在編輯時新增其他資料行。
- [Replace this] (取代此項) 功能表現在會實際啟動 [Replace Value] (取代值) 對話方塊。
- [Replace Value] (取代值) 轉換功能一如預期，不再擲回錯誤。
- 參考專案資料夾外部的資料檔案時，資料準備套件現在會使用絕對路徑，讓您可以使用資料檔案的絕對路徑在本機環境中執行套件。
- 現在使用 Azure BLOb 作為資料來源時，支援作為取樣策略的 [完整檔案]。
- 產生的 Python 程式碼 (來自資料準備套件) 現在會攜帶 CR 和 LF，方便使用 Windows 處理。
- [選擇度量] 下拉式清單現在切換至 [資料] 檢視時會隱藏屬性。
- Workbench 現在即使使用 Python 執行階段，也可以處理 parquet 檔案。 過去只能使用 Spark 處理 parquet 檔案。 
- 篩選出資料行中有_日期_資料類型的值，不會再造成資料準備引擎損毀。
- [計量] 檢視現在會遵守取樣策略更新。
- 遠端取樣作業現在已正確運作。

### <a name="job-execution"></a>作業執行
- 引數現在包含在執行歷程記錄中。
- 使用 CLI 開始的作業現在會自動出現在 [Run history Job] (執行歷程記錄作業) 面板中。
- 作業面板現在會顯示新增至 AAD 租用戶之來賓使用者所建立的工作。
- 作業面板的取消工和刪除動作都更加穩定。
- 如果設定檔的格式不正確，現在按一下 [執行] 按鈕就會觸發錯誤訊息。
- 結束應用程式不會再干擾以 CLI 開始的作業。
- 使用 CLI 開始的作業現在即使執行一小時後，仍會繼續產出標準輸出。
- 當 Python/PySpark 中的資料準備套件執行失敗時，會顯示更清楚的錯誤訊息。
- `az ml experiment clean` 現在也會清除遠端 VM 中的 Docker 映像。
- `az ml experiment clean` 現在能夠在 macOS 上正確處理本機目標。
- 清除本機或遠端 Docker 執行目標時會出現錯誤訊息，且更容易閱讀。
- 附加為執行目標的 HDInsight 叢集前端節點名稱格式不正確時，會顯示更清楚的錯誤訊息。
- 憑證服務中找不到密碼時，會顯示更清楚的錯誤訊息。 
- 升級 MMLSpark 程式庫以支援 Apache Spark 2.2。
- MMLSpark 現在包含醫療文件的主體編碼轉換 (網狀結構編碼)。
- `matplotlib` 2.1.0 版現在直接隨附於 Workbench。

### <a name="jupyter-notebook"></a>Jupyter Notebook
- Notebook 名稱搜尋在 [Notebook] 檢視中可正確運作。
- 您現在可以在 [Notebook] 檢視中刪除 Notebook。
- 新增新的魔術 `%upload_artifact` 將 Notebook 執行環境中產生的檔案上傳到執行歷程記錄資料存放區。
- 核心錯誤現在會顯示在 Notebook 作業狀態中以利偵錯。
- 現在當使用者登出應用程式時，Jupyter 伺服器會正確關閉。

### <a name="azure-portal"></a>Azure 入口網站
- 現在可以在兩個新的 Azure 區域中建立試驗帳戶和模型管理帳戶：西歐和東南亞。
- 現在只有當模型管理帳戶是訂用帳戶中建立的第一個帳戶時，才提供 DevTest 計劃。 
- 更新 Azure 入口網站的 [說明] 連結，指向正確的文件頁面。
- 從 Docker 映像詳細資料頁面中移除 [描述] 欄位，因為它不適用。
- 包括 AppInsights 和自動調整規模設定的詳細資料會新增至 Web 服務詳細資料頁面。
- 現在即使瀏覽器中停用協力廠商 Cookie，也會呈現模型管理頁面。 

### <a name="operationalization"></a>運算化
- 名稱中有 "score" 的 Web 服務不再失敗。
- 使用者現在只要有 Azure 資源群組或參與者存取權或訂用帳戶，就可以建立部署環境。 不再需要整個訂用帳戶的擁有者存取權。
- 作業 CLI 可在 Linux 上使用索引標籤自動完成。
- 映像建構服務現在支援 Azure IoT 服務/裝置的組建映像。

### <a name="sample-projects"></a>範例專案
- [_分類光圈_](./tutorial-classifying-iris-part-1.md)範例專案：
    - `iris_pyspark.py` 已重新命名為 `iris_spark.py`。
    - `iris_score.py` 已重新命名為 `score_iris.py`。
    - `iris.dprep` 和 `iris.dsource` 已更新，以反映最新的資料準備引擎更新。
    - 已修改 `iris.ipynb` Notebook 使適用於 HDInsight 叢集。
    - `iris.ipynb` Notebook 資料格已開啟執行歷程記錄。
- 已修正[_使用自行車共用資料的進階資料準備_](./tutorial-bikeshare-dataprep.md)範例專案「處理錯誤值」步驟。
- [_MMLSpark 成人人口普查資料_](https://github.com/Azure/MachineLearningSamples-mmlspark)範例專案 `docker.runconfig` 格式從 JSON 更新為 YAML。
- [_分散式超參數微調_](./scenario-distributed-tuning-of-hyperparameters.md)範例專案 `docker.runconfig` 格式從 JSON 更新為 YAML。
- 新的範例專案[_使用 CNTK 進行影像分類_](./scenario-image-classification-using-cntk.md)。
