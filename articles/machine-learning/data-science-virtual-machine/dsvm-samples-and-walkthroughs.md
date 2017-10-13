---
title: "資料科學虛擬機器的範例和逐步介紹 - Azure | Microsoft Docs"
description: "資料科學虛擬機器的範例和逐步介紹。"
keywords: "資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 89bb5d255db9ab266d04169a3101e2b694236029
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>資料科學虛擬機器 (DSVM) 上的範本

DSVM 包含 Jupyter Notebook 形式的完整解決的範本，以及並非以 Jupyter 為基礎的範本。 您可以按一下桌面或應用程式功能表上的 `Jupyter` 圖示存取 Jupyter。  
> [!NOTE]
> 請參閱[存取 Jupyter](#access-jupyter) 一節在 DSVM 上啟用 Jupyter Notebook。

## <a name="quick-reference-of-samples"></a>範例的快速參考
| 範例類別 | 說明 | 位置 |
| ------------- | ------------- | ------------- |
| **R** 語言  | **R** 中的範例說明的情況包括連接 Azure 雲端資料庫存放區、比較開放原始碼 R 與 Microsoft R，以及在 Microsoft R Server 或 SQL Server 上運用模型。 <br/> [螢幕擷取畫面](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** 語言  | **Python** 中的範例說明的情況包括連接 Azure 雲端資料存放區，以及使用 **Azure Machine Learning**。  <br/> [螢幕擷取畫面](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** 語言  | **Julia** 中的範例詳細說明 Julia 中的繪圖、Julia 中的深入學習、從 Julia 呼叫 C 和 Python 等等。 <br/> [螢幕擷取畫面](#julia-language) |<br/> **Windows**：<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**：<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Microsoft 辨識工具組)  | Microsoft 的辨識工具組小組發佈的深入學習範例。  <br/> [螢幕擷取畫面](#cntk) | <br/>**Windows**：<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**：<br/> `~notebooks/CNTK`<br/> <br/>|
| **MXnet** Notebooks  | 運用 **MXnet** 型神經網路的深入學習範例。 有從初學者到進階案例的各種不同 Notebook。  <br/> [螢幕擷取畫面](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Azure Machine Learning** AzureML  | 與 **Azure Machine Learning** Studio 互動，對於雲端型評分工作流程，從本機定型的模型建立 Web 服務端點。 <br/> [螢幕擷取畫面](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | 運用 **caffe2** 型神經網路的深入學習範例。 有一些 Notebook 的設計是讓使用者熟悉 caffe2 以及如何有效使用它，包括映像預先處理、資料集建立、迴歸和使用預先定型的模型之類的範例。 <br/> [螢幕擷取畫面](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | 使用 **H2O** 的 Python 架構範例處理許多真實案例問題。 <br/> [螢幕擷取畫面](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** 語言  | 在 **Apache Spark 2.0** 上透過 **pySpark 2.0** 利用 Spark 的 **MLlib** 工具組本身功用和功能的範例。  <br/> [螢幕擷取畫面](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| **MMLSpark** 語言  | 利用 **MMLSpark-Microsoft 的 Apache Spark 的機器學習** (對於 **Apache Spark** 提供許多深入學習和資料科學工具的架構) 的各種範例。 <br/> [螢幕擷取畫面](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | 使用 **TensorFlow** 架構實作的多個不同神經網路範例和技術。 <br/> [螢幕擷取畫面](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | **XGBoost** 中對於分類、迴歸等案例的標準機器學習範例。 <br/> [螢幕擷取畫面](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>存取 Jupyter 

移至 Windows 上的 **`https://localhost:9999`** 或 Ubuntu 上的 **`https://localhost:8000`**，造訪 Jupyter 首頁。


### <a name="enabling-jupyter-access-from-browser"></a>從瀏覽器啟用 Jupyter 存取

**Windows DSVM**

從桌面捷徑執行**`Jupyter SetPassword`**，然後遵循提示設定/重設 Jupyter 的密碼，並開始 Jupyter 程序。 
<br/>![啟用 Jupyter 例外狀況](./media/jupyter-setpassword.png)<br/>
使用瀏覽器造訪 **`https://localhost:9999`**，如此 Jupyter 程序在 VM 上成功開始之後，即可存取 Jupyter 首頁。 查看螢幕擷取畫面以新增例外狀況，並透過瀏覽器啟用 Jupyter 存取
<br/>![啟用 Jupyter 例外狀況](./media/windows-jupyter-exception.png)<br/>
使用剛設定的新密碼登入。
<br/>
**Linux DSVM**

您可以使用瀏覽器上造訪 **`https://localhost:8000`**，以便在 VM 上存取 Jupyter 首頁。 查看螢幕擷取畫面以新增例外狀況，並透過瀏覽器啟用 Jupyter 存取。
<br/>![啟用 Jupyter 例外狀況](./media/ubuntu-jupyter-exception.png)<br/>
使用登入 DSVM 所用的同一組密碼登入。
<br/>

**Jupyter 首頁**
<br/>![Jupyter 首頁](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 語言 
<br/>![R 範例](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 語言
<br/>![Python 範例](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 語言 
<br/>![Julia 範例](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![CNTK 範例](./media/cntk-samples2.png)<br/>
<br/>![CNTK 範例](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXnet
<br/>![MXnet 範例](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML 範例](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>caffe2 
<br/>![caffe2 範例](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O 範例](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 範例](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow 範例](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 範例](./media/xgboost-samples.png)<br/>

