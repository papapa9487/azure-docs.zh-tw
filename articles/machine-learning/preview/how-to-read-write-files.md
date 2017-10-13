---
title: "如何讀取和寫入大型資料檔 | Microsoft Docs"
description: "如何在 Azure ML 實驗中讀取和寫入大型檔案。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: fb3158ef786ad73440a59c07b38476a98ced0768
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="persisting-changes-and-dealing-with-large-files"></a>保存變更和處理大型檔案

## <a name="execution-isolation-portability-and-reproducibility"></a>執行隔離、可攜性和重現性
Azure ML 實驗服務可讓您設定不同的執行目標，有些是本機 (例如，本機電腦或本機電腦上的 Docker 容器)，有些則是遠端 (例如，遠端電腦上的Docker 容器或 HDInsight 叢集)。 請參考[組態實驗執行](experiment-execution-configuration.md)文章，以取得更多詳細資料。 先將專案資料夾複製到該計算目標，之後才能發生任何執行。 即使是基於此目的而使用本機暫存資料夾的本機執行，也符合此情況。 

此設計的目的是確保執行隔離、重現性和可攜性。 如果您執行同一個指令碼兩次，則不論是在相同的計算目標或不同的計算目標中，您一定會收到相同的結果。 第一個執行所做的變更不應影響第二個執行。 透過此設計，我們可將計算目標視為無狀態的計算資源，與在它們完成之後執行的作業之間沒有任何同質性。

## <a name="challenges"></a>挑戰
透過可攜性和重複性的優點，此設計也帶來一些獨特的挑戰：
### <a name="persisting-state-changes"></a>保存狀態變更
如果您的指令碼會修改計算內容上的狀態，則不會為您的下一個執行保存變更，也不會自動將它們傳播回用戶端電腦。 

更具體而言，如果您的指令碼會建立子資料夾或寫出檔案，您將無法在專案目錄後置執行中找到這類資料夾或檔案。 它們會保留在其發生所在之計算目標環境上的暫存資料夾中。 它們可能會用於偵錯用途，但您絕對不能依賴它們的存在。

### <a name="dealing-with-large-files-in-project-folder"></a>處理專案資料夾中的大型檔案

如果您的專案資料夾包含任何大型檔案，當每個專案一開始將專案資料夾複製到目標計算環境時，即會造成延遲。 即使執行會在本機發生，但它仍是應該避免的不必要磁碟流量。 這就是為什麼我們目前將最大專案大小限制在 25 MB 的原因。

## <a name="option-1-use-the-outputs-folder"></a>選項 1：使用輸出資料夾
如果您的指令碼會產生檔案，而您希望這些檔案隨著每個實驗執行變更，而且您想要保留這些檔案的歷程記錄，則這是慣用的選項。 一般使用案例如下：如果您定型模型或建立資料集，或將圖形繪製為影像檔以作為模型定型執行的一部分。 而您想要跨執行比較這些輸出，並且想要返回並選取上一個執行所產生的輸出檔 (例如模型)，然後將它用於後續作業 (例如計分)。

基本上，您可以將檔案寫入名為 `outputs` 的資料夾 (相對於根目錄)。 這是特殊的資料夾，會受到實驗服務的特殊處理。 您的指令碼在執行期間於該處建立的任何項目 (例如，模型檔、資料檔或繪製的影像檔 (合稱為「成品」))，均會在完成執行之後，複製到與您實驗帳戶相關聯的 Azure Blob 儲存體帳戶。 它們會成為您執行歷程記錄記錄的一部分。

以下提供在 `outputs` 資料夾中儲存模型的快速範例：
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
您可以下載任何「成品」，方法是瀏覽至 Azure ML Workbench 中特殊執行之執行詳細資料頁面的 [輸出檔案] 區段、選取它，然後按一下 [下載] 按鈕。 或者，您可以從 CLI 視窗使用 `az ml asset download` 命令。

若要查看更完整的範例，請參閱 Classifying Iris (將蝴蝶花分類) 範例專案中的 `iris_sklearn.py` Python 指令碼。

## <a name="option-2-use-the-shared-folder"></a>選項 2：使用共用資料夾
使用可跨獨立執行存取的共用資料夾，對下列案例而言是一個絕佳選項，但前提是您不需要針對每個執行保留這些檔案的歷程記錄： 
- 您的指令碼需要從本機檔案 (例如 csv 檔案)，或是文字檔或影像檔的目錄載入資料，以作為定型或測試資料。 
- 您的指令碼會處理未經處理的資料，並寫出中繼結果，例如凸顯文字/影像檔的定型資料，以便在後續定型執行中使用。 
- 您的指令碼會產生一個模型，而您後續的計分指令碼需要挑選該模型並用它來進行評估。 

有個重要的注意事項是，共用資料夾會本機存留於您選擇的相同計算目標上。 因此，這只有在您所有參考此共用資料夾的指令碼執行都在相同的計算目標上執行，而且計算目標不會在執行之間循環時才會運作。

共用資料夾功能可讓您讀取或寫入透過環境變數 `AZUREML_NATIVE_SHARE_DIRECTORY` 所識別的特定資料夾。 

### <a name="example"></a>範例
以下提供一些範例 Python 程式碼，以使用此共用資料夾來讀取和寫入文字檔：
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

如需更完整的範例，請參閱 Classifying Iris (將蝴蝶花分類) 範例專案中的 `iris_sklearn_shared_folder.py` 檔案。

在您可以使用此功能之前，必須先在代表 `aml_config` 資料夾內之目標執行內容的 `.compute` 檔案中設定一些簡單組態。 此資料夾的實際路徑可以根據您選擇的計算目標和您設定的值而有所不同。

### <a name="configure-local-compute-context"></a>設定本機計算內容

若要在本機計算內容上啟用此功能，只要將下一行新增至代表「本機」環境的 `.compute` 檔案中 (通常命名為 `local.compute`) 即可。
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

`~/.azureml/share` 是預設的基底資料夾路徑。 您可以將它變更為指令碼執行可存取的任何本機絕對路徑。 實驗帳戶名稱、工作區名稱和專案名稱均會自動附加至基底目錄，以組成共用目錄的完整路徑。 例如，如果您使用先前的預設值，即可將檔案寫入下列路徑 (以及從中擷取)：

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-docker-compute-context-local-or-remote"></a>設定 Docker 計算內容 (本機或遠端)
若要在 Docker 計算內容上啟用此功能，您需要將下列兩行新增至您的本機或遠端 Docker _.compute_ 檔案。

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>當您使用 `AZUREML_NATIVE_SHARE_DIRECTORY` 環境變數來存取共用資料夾時，必須將 `sharedVolume` 設為 `true`，否則執行將會失敗。

在 Docker 容器中執行的程式碼一律會以 `/azureml-share/` 形式看見此共用資料夾。 Docker 容器所看見的此資料夾路徑是不可設定的。 而您不應該在程式碼中採用此資料夾名稱上的相依性。 相反地，一律使用環境變數名稱 `AZUREML_NATIVE_SHARE_DIRECTORY` 來處理此資料夾。 它會對應到 Docker 主機電腦/計算內容上的本機資料夾。 這個本機資料夾的基底目錄是 `.compute` 檔案中 `nativeSharedDirectory` 設定的可設定值。 主機電腦上共用資料夾的本機路徑 (如果您使用上述預設值) 如下所示：
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!TIP]
>請注意，本機磁碟上共用資料夾的路徑，在本機計算內容與本機 Docker 計算內容之間是一樣的。 這表示，您甚至可以在本機執行和本機 Docker 執行之間共用檔案。

您可以直接在這些資料夾中放置輸入資料，並預期您在該電腦上的本機或 Docker 執行可以挑選它們。 您也可以從本機或 Docker 執行將檔案寫入此資料夾，並預期檔案會保存於該資料夾中，存留於執行生命週期中。

如需 Azure ML 執行服務中組態檔的詳細資訊，請參閱這篇文章：[執行組態檔](experiment-execution-configuration-reference.md)。

>[!NOTE]
>HDInsight 計算內容中不支援 `AZUREML_NATIVE_SHARE_DIRECTORY` 環境變數。 不過，您可以藉由明確地使用絕對 WASB 路徑來讀取或寫入連接的 Blob 儲存體，輕鬆達到相同的結果。

## <a name="option-3-use-external-durable-storage"></a>選項 3：使用外部永久性儲存體

您當然可以隨意使用外部永久性存放區來保存執行期間的狀態。 這適用於下列情況：
- 您的輸入資料已經儲存於可從目標計算環境存取的永久性儲存體中。
- 這些檔案不需是執行歷程記錄的一部分。
- 這些檔案必須由跨不同運算環境的執行共用。
- 這些檔案需要存留計算內容本身。

這其中一個範例是從您的 Python/PySpark 程式碼使用 Azure Blob 儲存體。

以下提供從 Python 程式碼使用 Azure Blob 儲存體的快速範例：
```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

以下也會提供將任何任意的 Azure Blob 儲存體連接至 HDI Spark 執行階段的快速範例：
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>結論
由於 Azure ML 會藉由將整個專案資料夾複製到目標計算內容來執行指令碼，因此，必須特別注意大型輸入、輸出和中繼檔案。 您可以使用特殊的 `outputs` 資料夾、可透過 `AZUREML_NATIVE_SHARE_DIRECTORY` 環境變數存取的共用資料夾，或適用於大型檔案交易的外部永久性儲存體。 

## <a name="next-steps"></a>後續步驟
- 檢閱[設定實驗執行](experiment-execution-configuration-reference.md)。
- 請參閱 [Classifying Iris](tutorial-classifying-iris-part-1.md) (將蝴蝶花分類) 教學課程專案如何使用 `outputs` 資料夾來保存定型的模型。