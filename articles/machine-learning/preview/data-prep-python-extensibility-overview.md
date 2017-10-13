---
title: "搭配 Azure Machine Learning 資料準備使用 Python 擴充性 | Microsoft Docs"
description: "本文件提供如何使用 Python 程式碼來擴充資料準備功能的概觀和一些詳細範例"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 4e1935a7830b8174796ac12792fbbc0ed110d081
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="data-prep-python-extensions"></a>資料準備 Python 擴充功能
資料準備可用來作為填補內建功能之間功能落差的方法，其中包含多個層級的擴充性。 在本文件中，我們將透過 Python 指令碼概述擴充性。 

## <a name="custom-code-steps"></a>自訂程式碼步驟 
資料準備包含下列自訂步驟，使用者可在這些步驟中撰寫程式碼： 
1. 檔案讀取器*
2. 寫入器*
3. 新增資料行
4. 進階篩選
5. 轉換資料流程
6. 轉換分割區

*Spark 執行中目前不支援這些步驟。 

## <a name="code-block-types"></a>程式碼區塊類型 
針對這其中每個步驟，我們支援兩種程式碼區塊類型。 首先，我們支援依原樣執行的未包裝 Python 運算式。 其次，支援 Python 模組，我們會在您提供的程式碼內使用已知簽章來呼叫特定函式。

例如，您可以新增資料行，以下列兩種方式來計算另一個資料行的記錄：運算式： 

```python    
    math.log(row["Score"])
```

模組： 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


模組模式中的「新增資料行」轉換預期會找到名為 `newvalue` 的函式，其會接受資料列變數，並傳回資料行的值。 此模組可以包含任何數量的 Python 程式碼以及其他函式、匯入等。 

下列各節將討論每個擴充點的詳細資料： 

## <a name="imports"></a>匯入 
如果您使用「運算式」區塊類型，仍然可以將匯入陳述式新增至程式碼，但它們必須全部群組於程式碼的前幾行中。 正確： 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Error:  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
如果您使用「模組」區塊類型，則可遵循使用 'import' 陳述式的所有一般 Python 規則。 

## <a name="default-imports"></a>預設的匯入
下列匯入一律包含於您的程式碼中且可供使用。 您不需要重新匯入它們。 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="installing-new-packages"></a>安裝新的封裝
若要使用預設不會安裝的封裝，您需要先將它安裝到資料準備使用的環境。 您需要在本機電腦上和您想要執行的任何計算目標上完成此安裝。

若要在計算目標中安裝您的封裝，您必須修改位於專案根目錄下方之 aml_config 資料夾中的 conda_dependencies.yml 檔案。

### <a name="windows"></a>Windows 
在 Windows 上尋找位置的方式是，尋找應用程式專屬的 Python 安裝及其指令碼目錄，預設值是：  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

然後執行下列任一個命令： 

`conda install <libraryname>` 

或 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
若要在 Mac 上尋找位置，請尋找應用程式專屬的 Python 安裝及其指令碼目錄，預設位置是： 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

然後執行下列任一個命令： 

`./conda install <libraryname>`

或 

`./pip install <libraryname>`

## <a name="column-data"></a>資料行資料 
您可以使用點標記法或使用索引鍵/值標記法，從資料列存取資料行資料。 您無法使用點標記法，來存取包含空格或特殊字元的資料行名稱。 `row` 變數應一律定義於這兩種模式的 Python 擴充功能 (模組和運算式) 中。 

範例： 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>檔案讀取器 
### <a name="purpose"></a>目的 
這個擴充點可讓您完全掌控將檔案讀入資料流程的程序。 系統會呼叫您的程式碼，然後傳入您應該處理的檔案清單，而您的程式碼需要建立並傳回 Pandas 資料框架。 

>[!NOTE]
>這個擴充點無法在 Spark 中運作。 


### <a name="how-to-use"></a>使用方式 
您可以從 [開啟資料來源] 精靈存取這個擴充點。 選擇第一個頁面上的 [檔案]，然後選擇檔案位置。 在 [選擇檔案參數] 頁面上，下拉 [檔案類型]，然後選擇 [自訂檔案 (指令碼)]。 

系統會為您的程式碼指定名為 'df' 的 Pandas 資料框架，其中包含您需要讀取之檔案的相關資訊。 如果您選擇開啟包含多個檔案的目錄，資料框架就會包含一個以上的資料列。  

這個資料框架具有下列資料行： 
- Path：要讀取的檔案。
- PathHint：告訴您檔案所在位置。 值：‘Local’、‘AzureBlobStorage’、‘AzureDataLakeStorage’
- AuthenticationType：用來存取檔案的驗證類型。 值：‘None’、‘SasToken’、‘OAuthToken’
- AuthenticationValue：包含 None 或要使用的語彙基元。

### <a name="syntax"></a>語法 
運算式： 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


模組：  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>寫入器 
### <a name="purpose"></a>目的 
這個寫入器可讓您完全掌控從資料流程寫入資料的程序。 系統會呼叫您的程式碼、傳入資料框架，而您的程式碼可在需要時使用資料框架來寫入資料。 

>[!NOTE]
>這個寫入器擴充點無法在 Spark 中運作。 


### <a name="how-to-use"></a>使用方式 
您可以使用 [寫入資料流程 (指令碼)] 區塊來新增這個擴充點。 您可以在最上層的 [轉換] 功能表上取得它。 

### <a name="syntax"></a>語法 
運算式： 

```python
    df.to_csv('c:\\temp\\output.csv')
```

模組：

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
這個自訂寫入區塊只能存在於步驟清單中間，所以，如果您使用模組，則您的寫入函式必須傳回的資料框架是所遵循步驟的輸入。 

## <a name="add-column"></a>新增資料行 
### <a name="purpose"></a>目的
這個擴充點可讓您撰寫 Python 來計算新的資料行。 您所撰寫的程式碼可以存取完整的資料列。 它必須傳回每個資料列的新資料行值。 

### <a name="how-to-use"></a>使用方式
您可以使用 [新增資料行 (指令碼)] 區塊來新增這個擴充點。 您可以在最上層的 [轉換] 功能表上以及資料行內容功能表上取得它。 

### <a name="syntax"></a>語法
運算式： 

```python
    math.log(row["Score"])
```

模組： 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>進階篩選
### <a name="purpose"></a>目的 
這個擴充點可讓您撰寫自訂篩選。 您可以存取整個資料列，而您的程式碼必須傳回 True (包含資料列) 或 False (不含資料列)。 

### <a name="how-to-use"></a>使用方式
您可以使用 [進階篩選 (指令碼)] 區塊來新增這個擴充點。 您可以在最上層的 [轉換] 功能表上取得它。 

### <a name="syntax"></a>語法

運算式： 

```python
    row["Score"] > 95
```

模組：  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>轉換資料流程
### <a name="purpose"></a>目的 
這個擴充點可讓您完全轉換資料流程。 您可以存取包含您正在處理之所有資料行和資料列的 Pandas 資料框架，而您的程式碼必須傳回含有新資料的 Pandas 資料框架。 

>[!NOTE]
>在 Python 中，如果使用此擴充功能，即會將所有資料載入 Pandas 資料框架中的記憶體。 

在 Spark 中，會在單一背景工作節點上收集所有資料。 如果資料非常大，這可能導致背景工作執行記憶體不足。 請小心使用。

### <a name="how-to-use"></a>使用方式 
您可以使用 [轉換資料流程 (指令碼)] 區塊來新增這個擴充點。 您可以在最上層的 [轉換] 功能表上取得它。 
### <a name="syntax"></a>語法 

運算式： 

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

模組： 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>轉換分割區  
### <a name="purpose"></a>目的 
這個擴充點可讓您轉換資料流程的分割區。 您可以存取包含該分割區適用之所有資料行和資料列的 Pandas 資料框架，而您的程式碼必須傳回含有新資料的 Pandas 資料框架。 

>[!NOTE]
>在 Python 中，視您的資料大小而定，您最終可能會產生單一分割區或多個分割區。 在 Spark 中，您正在使用的資料框架會在指定的背景工作節點上保存分割區適用的資料。 在這兩種情況下，您無法假設您具有整個資料集的存取權限。 


### <a name="how-to-use"></a>使用方式
您可以使用 [轉換分割區 (指令碼)] 區塊來新增這個擴充點。 您可以在最上層的 [轉換] 功能表上取得它。 

### <a name="syntax"></a>語法 

運算式： 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

模組： 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>錯誤值  
資料準備中提供了錯誤值的概念。 此處說明建立它們及其存在的原因 <link to error values doc>。 

您可能會在自訂 Python 程式碼中遇到錯誤值。 它們是名為 `DataPrepError` 之 Python 類別的執行個體。 這個類別會包裝 Python 例外狀況並具有數種屬性，其中包含處理原始值時遇到的錯誤以及原始值的相關資訊。 


### <a name="datapreperror-class-definition"></a>DataPrepError 類別定義
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
在資料準備的 Python 架構中建立 DataPrepError，通常看起來像這樣： 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>使用方式 
對於在擴充點上執行的 Python，可能會使用先前的建立方式來產生 DataPrepErrors 作為傳回值。 DataPrepErrors 將在擴充點上處理資料時遇到的可能性更大。 此時，自訂 Python 程式碼需要處理 DataPrepError 作為有效的資料類型。 

#### <a name="syntax"></a>語法 
運算式：  
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
模組：  
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
