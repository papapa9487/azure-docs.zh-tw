---
title: "Sprint 2 2017 年 12 月的 Azure ML Workbench 版本資訊"
description: "本文件詳述 Azure ML 的 Sprint 2 版本更新"
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: f3579942624de282b01d74c4b8c449c56a66e7b7
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="sprint-2---december-2017"></a>Sprint 2 - 2017 年 12 月 

#### <a name="version-number-01171115263"></a>版本號碼：0.1.1711.15263

>此處提供如何[尋找版本號碼](https://docs.microsoft.com/en-us/azure/machine-learning/preview/known-issues-and-troubleshooting-guide)的說明。

歡迎使用 Azure Machine Learning Workbench 的第三次更新。 此更新包括 Workbench 應用程式、命令列介面 (CLI) 和後端服務的改進。 非常感謝您傳送的正反面意見。 以下的更新有許多是直接來自您的意見反應。 

## <a name="notable-new-features"></a>重要新功能
- [支援 SQL Server 和 Azure SQL DB 作為資料來源](https://docs.microsoft.com/en-us/azure/machine-learning/preview/data-prep-appendix2-supported-data-sources#types) 
- [使用 MMLSpark 在具備 GPU 支援的 Spark 上進行深度學習](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md) \(英文\)
- [所有 AML 容器在部署時都與 Azure IoT Edge 裝置相容 (不需要額外步驟)](http://aka.ms/aml-iot-edge-blog) \(英文\)
- Azure 入口網站提供註冊的模型清單和詳細資料檢視
- 除了透過使用者名稱/密碼存取計算目標之外，也可以使用以 SSH 金鑰為基礎的驗證進行存取。 
- 資料準備體驗中全新的模式頻率偵測器。 

## <a name="detailed-updates"></a>詳細的更新
以下是 Azure Machine Learning 在此 Sprint 中各元件區域的詳細更新清單。

### <a name="installer"></a>安裝程式
- 安裝程式可以自行更新，以在使用者不需重新安裝的情況下修正錯誤並支援新功能

### <a name="workbench-authentication"></a>Workbench 驗證
- 對驗證系統進行多項修正。 請讓我們知道您在登入時是否仍會發生問題。
- 可讓您更容易找到 Proxy 管理員設定的 UI 變更。

### <a name="workbench"></a>Workbench
- 唯讀檔案檢視現在具有淡藍色背景
- 將 [編輯] 按鈕移動到右邊，讓您更容易找到。
- "dsource"、"dprep" 和 "ipynb" 檔案格式現在可以以原始文字格式轉譯
- Workbench 現在擁有全新編輯體驗，可引導使用者使用外部 IDE 來編輯指令碼，並只使用 Workbench 來編輯可提供豐富編輯體驗的檔案類型 (例如 Notebook、資料來源、資料準備套件)
- 現在可以更快速地載入使用者擁有存取權之工作區和專案的清單

### <a name="data-preparation"></a>資料準備 
- 可檢視資料行中字串模式的模式頻率偵測器。 您也可以使用這些模式來篩選資料。 這會顯示和值計數偵測器類似的檢視。 差別在於樣式頻率會顯示資料的唯一模式計數，而非唯一資料計數。 您也可以篩選出或篩選掉符合特定模式的所有資料列。

![產品編號上模式頻率偵測器的影像](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- 效能提升，同時建議可在「衍生資料行實例化」轉換中檢閱的邊緣案例

- [支援 SQL Server 和 Azure SQL DB 作為資料來源](https://docs.microsoft.com/en-us/azure/machine-learning/preview/data-prep-appendix2-supported-data-sources#types) 

![建立新 SQL 伺服器資料來源的影像](media/release-notes-sprint-2/sql-server-data-source.png)

- 已啟用資料列和資料行計數的「概覽」檢視

![概覽之資料列資料行計數的影像](media/release-notes-sprint-2/row-col-count.png)

- 已在所有計算內容中啟用資料準備
- 已在所有計算內容中啟用使用 SQL Server 資料庫的資料來源
- 可以依據資料類型篩選資料準備資料格資料行
- 已修正將多個資料行轉換成日期的問題
- 已修正如果使用者在進階模式中變更輸出資料行名稱，便可以在「衍生資料行實例化」中選取輸出資料行作為來源的問題。

### <a name="job-execution"></a>作業執行
您現在可以依照下列步驟，使用以 SSH 金鑰為基礎的驗證建立及存取 Remotedocker 或叢集類型計算目標：
- 在 CLI 中使用下列命令來附加計算目標

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] 命令中的 -k (或 --use-azureml-ssh-key) 選項會指定產生及使用 SSH 金鑰。

- Azure ML Workbench 將會產生公開金鑰，並在您的主控台中輸出。 使用相同的使用者名稱登入計算目標，並附加具有此公開金鑰的 ~/.ssh/authorized_keys 檔案。

- 您可以準備此計算目標並將它用於執行，Azure ML Workbench 將會使用此金鑰進行驗證。  

如需建立計算目標的詳細資訊，請參閱[設定 Azure Machine Learning 測試服務](https://docs.microsoft.com/en-us/azure/machine-learning/preview/experimentation-service-configuration)

### <a name="visual-studio-tools-for-ai"></a>適用於 AI 的 Visual Studio Tools
- 已新增對[適用於 AI 的 Visual Studio Tools](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017) \(英文\) 的支援。 

### <a name="command-line-interface-cli"></a>命令列介面 (CLI)
- 已新增 `az ml datasource create` 命令來允許從命令列建立資料來源檔案

### <a name="model-management-and-operationalization"></a>模型管理與作業
- [所有 AML 容器在作業時都與 Azure IoT Edge 裝置相容 (不需要額外步驟)](http://aka.ms/aml-iot-edge-blog) \(英文\) 
- o16n CLI 中錯誤訊息的改進
- 模型管理入口網站 UX 中的錯誤修正  
- 詳細資料頁面中模型管理屬性採用一致的字母大小寫
- 即時計分呼叫逾時設定為 60 秒
- Azure 入口網站提供註冊的模型清單和詳細資料檢視

![入口網站中的模型詳細資料](media/release-notes-sprint-2/model-list.jpg)

![入口網站中的模型概觀](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- 在具備 [GPU 支援](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md) \(英文\) 的 Spark 上進行深度學習
- 支援資源管理員範本，以便於進行資源部署
- 支援 SparklyR 生態系統
- [AZTK 整合](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark) \(英文\)

### <a name="sample-projects"></a>範例專案
- [鳶尾花](https://github.com/Azure/MachineLearningSamples-Iris) \(英文\) 和 [MMLSpark](https://github.com/Azure/mmlspark) \(英文\) 範例已更新為新的 Azure ML SDK 版本

## <a name="breaking-changes"></a>重大變更
- 將 `az ml computetarget attach` 中的 `--type` 參數升級為子命令。 

    - `az ml computetarget attach --type remotedocker` 現在為 `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` 現在為 `az ml computetarget attach cluster`
