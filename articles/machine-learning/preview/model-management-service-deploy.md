---
title: "Azure Machine Learning 模型管理 Web 服務部署 | Microsoft Docs"
description: "本文件說明與使用 Azure Machine Learning 模型管理來部署機器學習服務模型有關的步驟。"
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 0ebf8964bc565e5d4f95fb1ad3edbc16f841851c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>將機器學習服務模型部署為 Web 服務

Azure Machine Learning 模型管理會提供介面供您將模型部署為容器化的 Docker 型 Web 服務。 您可以使用 Spark、Microsoft 辨識工具組 (CNTK)、Keras、Tensorflow 和 Python 等架構來部署您所建立的模型。 

本文件會說明使用 Azure Machine Learning 模型管理命令列介面 (CLI) 來將模型部署為 Web 服務的步驟。

## <a name="deploying-web-services"></a>部署 Web 服務
使用 CLI，您就可以部署會在本機機器或叢集上執行的 Web 服務。

建議您先從本機部署開始。 請先驗證模型和程式碼可正常運作，然後再將 Web 服務部署至叢集以供生產級別的環境使用。 如需設定環境以供部署叢集的詳細資訊，請參閱[模型管理設定](model-management-configuration.md)。 

部署步驟如下：
1. 使用已儲存且經過訓練的機器學習服務模型
2. 為 Web 服務的輸入和輸出資料建立結構描述
3. 建立 Docker 型容器映像
4. 建立及部署 Web 服務

### <a name="1-save-your-model"></a>1.儲存模型
請從已儲存且經過訓練的模型檔案來開始，例如 mymodel.pkl。 副檔名會根據您用來訓練和儲存模型的平台而不同。 

例如，您可以使用 Python 的 Pickle 程式庫將經過訓練的模型儲存至檔案。 以下是使用 Iris 資料集的[範例](http://scikit-learn.org/stable/modules/model_persistence.html)：

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2.建立 schema.json 檔案
此為選用步驟。 

建立結構描述，以自動驗證 Web 服務的輸入和輸出。 CLI 也會使用結構描述來產生 Web 服務的 Swagger 文件。

若要建立結構描述，請匯入下列程式庫：

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
接下來，定義輸入變數，例如 Spark 資料框架、Pandas 資料框架或 NumPy 陣列。 下列範例使用 NumPy 陣列：

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```
下列範例使用 Spark 資料框架：

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

下列範例使用 PANDAS 資料框架：

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3.建立 score.py 檔案
請提供 score.py 檔案，以載入模型並傳回使用模型所做出的預測結果。

此檔案必須包含兩個函式：init 和 run。

在 score.py 檔案的頂端新增下列程式碼來啟用資料收集功能，以協助收集模型的輸入和預測資料

    ```
    from azureml.datacollector import ModelDataCollector
    ```

如需如何使用這項功能的詳細資訊，請查看[模型資料收集](how-to-use-model-data-collection.md)一節。

#### <a name="init-function"></a>Init 函式
您可以使用 init 函式來載入已儲存的模型。

用來載入模型的簡單 init 函式範例如下：

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Run 函式
run 函式會使用模型和輸入資料來傳回預測。

用來處理輸入並傳回預測結果的簡單 run 函式範例如下：

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4.註冊模型
下列命令可用來註冊上面的步驟 1 所建立的模型，

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5.建立資訊清單
下列命令可協助建立模型的資訊清單，

```
az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]
```
您可以在上面所示的命令中使用 `--model-id` 或 `-i` 引數，將先前註冊的模型新增至資訊清單中。 另外搭配 -i 引數就可以指定多個模型。

### <a name="6-create-an-image"></a>6.建立映像 
您可以選擇先建立映像的資訊清單，然後再建立映像。 

```
az ml image create -n [image name] -manifest-id [the manifest ID]
```

或者，您也可以使用單一命令同時建立資訊清單和映像。 

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>如需命令參數的詳細資訊，請在命令結尾輸入 -h，例如 az ml image create -h。


### <a name="7-create-and-deploy-the-web-service"></a>7.建立及部署 Web 服務
請使用下列命令部署服務：

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>您也可以使用單一命令來執行上述的 4 個步驟。 如需詳細資訊，請對 service create 命令使用 -h。

### <a name="8-test-the-service"></a>8.測試服務
使用下列命令來取得如何呼叫服務的資訊：

```
az ml service usage realtime -i <service id>
```

請從命令提示字元使用 run 函式來呼叫服務：

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

下列範例會呼叫 Iris Web 服務範例：

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>後續步驟
現在您已測試過 Web 服務可在本機執行，接下來您可以將服務部署到叢集以便大規模使用。 如需有關設定叢集以便部署 Web 服務的詳細資訊，請參閱[模型管理設定](model-management-configuration.md)。 
