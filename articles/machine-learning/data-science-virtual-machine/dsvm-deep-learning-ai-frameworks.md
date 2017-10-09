---
title: "深度學習和 AI 架構 - Azure | Microsoft Docs"
description: "深度學習和 AI 架構"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 46662c15374f781a6527bb1435d883ae060c63f4
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="deep-learning-and-ai-frameworks"></a>深度學習和 AI 架構
[資料科學虛擬機器](http://aka.ms/dsvm) \(英文\) (DSVM) 和[深度學習 VM](http://aka.ms/dsvm/deeplearning) \(英文\) 支援數種深度學習架構，可協助建置具備預測性分析及能了解影像及語言之辨識能力的人工智慧 (AI) 應用程式。 

以下是 DSVM 上所提供之所有深度學習架構的詳細資料。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft 辨識工具組

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 深度學習架構      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | Microsoft 辨識工具組 (CNTK) 是安裝在 Python 2.7 的 _root_ 環境中，以及 Python 3.5 的 _py35_ 環境中。   |
| 範例的連結      | 已包含範例 Jupyter 筆記本。     |
| DSVM 上的相關工具      | Keras      |
| 如何使用/執行它？    | 開啟 Jupyter，然後尋找 CNTK 資料夾  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 深度學習架構      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | 在 Linux 上，TensorFlow 是安裝在 Python 2.7 (_root_) 及 Python 3.5 (_py35_) 環境中。 在 Windows 上，Tensorflow 是安裝在 Python 3.5(_py35_) 環境中。  |
| 範例的連結      | 已包含範例 Jupyter 筆記本。     |
| DSVM 上的相關工具      | Keras      |
| 如何使用/執行它？    | 開啟 Jupyter，然後尋找 TensorFlow 資料夾。  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 深度學習架構      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | Keras 是安裝在 Python 2.7 (_root_) 及 Python 3.5 (_py35_) 環境中。   |
| 範例的連結      | https://github.com/fchollet/keras/tree/master/examples      |
| DSVM 上的相關工具      | Microsoft 辨識工具組、TensorLlow、Theano      |
| 如何使用/執行它？    | 從 Github 位置下載範例，將範例複製到 ~/notebooks 底下的目錄，然後在 Jupyter 中開啟它   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 深度學習架構      |
| 支援的 DSVM 版本      | Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | Caffe 是安裝在 `/opt/caffe` 中。    |
| 範例的連結      | 範例會包含在 `/opt/caffe/examples`中。      |
| DSVM 上的相關工具      | Caffe2      |
### <a name="how-to-use--run-it"></a>如何使用/執行它？  

使用 X2Go 登入您的 VM，然後啟動新的終端機，並輸入

```
cd /opt/caffe/examples
jupyter notebook
```

具有範例筆記本的新瀏覽器視窗將會開啟。

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 深度學習架構      |
| 支援的 DSVM 版本      | Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | Caffe2 是安裝在 `/opt/caffe2` 中。 它也針對 Python 2.7(_root_) Conda 環境提供。     |
| 範例的連結      | 已包含範例 Jupyter 筆記本     |
| DSVM 上的相關工具      | Caffe      |
| 如何使用/執行它？    | 開啟 Jupyter，然後瀏覽至 Caffe2 目錄以尋找範例筆記本。 某些筆記本會要求以 Python 程式碼設定 Caffe2 根，請輸入 /opt/caffe2。   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 深度學習架構      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | Chainer 是安裝在 Python 2.7 (_root_) 及 Python 3.5 (_py35_) 環境中。 也會安裝 ChainerRL 和 ChainerCV。   |
| 範例的連結      | 已包含範例 Jupyter 筆記本。      |
| DSVM 上的相關工具      | Caffe      |

### <a name="how-to-use--run-it"></a>如何使用/執行它？  

在終端機中，啟動所需的 Python 版本 (_root_或 _py35_)，執行 _python_，然後匯入 Chainer。 在 Jupyter 中，選取 [Python 2.7] 或 [Python 3.5] 核心，然後匯入 Chainer。


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 適用於 H2O 的深度學習架構      |
| 支援的 DSVM 版本      | Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | Deep Water 是安裝在 `/dsvm/tools/deep_water` 中。   |
| 範例的連結      | 範例可透過 Deep Water 伺服器取得。      |
| DSVM 上的相關工具      | H2o、Sparkling Water      |

### <a name="how-to-use--run-it"></a>如何使用/執行它？  

使用 X2Go 連線至 VM。 在終端機中，啟動 Deep Water 伺服器：

    java -jar /dsvm/tools/deep_water/h2o.jar

然後開啟瀏覽器，並連線至 `http://localhost:54321`。



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 深度學習架構      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | MXNet 是安裝在 Windows 上的 `C:\dsvm\tools\mxnet` 中，以及 Linux 上的 `/dsvm/tools/mxnet` 中。 Python 繫結是安裝在 Python 2.7 (_root_) 及 Python 3.5 (_py35_) 環境中。 也會安裝 R 繫結。   |
| 範例的連結      | 已包含範例 Jupyter 筆記本。    |
| DSVM 上的相關工具      | Keras      |
| 如何使用/執行它？    | 開啟 Jupyter，然後尋找 mxnet 資料夾  |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 來自 NVIDIA 的深度學習系統，可用來快速訓練深度學習模型      |
| 支援的 DSVM 版本      | Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | DIGITS 是安裝在 `/dsvm/tools/DIGITS` 中，並以稱為 _digits_ 之服務的形式提供。   |
### <a name="how-to-use--run-it"></a>如何使用/執行它？  

使用 X2Go 登入 VM。 在終端機中，啟動該服務：

    sudo systemctl start digits

服務需要約一分鐘才能啟動。 開啟網頁瀏覽器，並瀏覽至 `http://localhost:5000`。



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 用來查詢 GPU 活動的 NVIDIA 工具      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | _nvidia-smi_ 可於系統路徑上取得。   |
| 如何使用/執行它？ | 啟動命令提示字元 (在 Windows 上) 或終端機 (在 Linux 上)，然後執行 _nvidia-smi_。



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 深度學習架構      |
| 支援的 DSVM 版本      | Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | Theano 是安裝在 Python 2.7 (_root_) 及 Python 3.5 (_py35_) 環境中。   |
| DSVM 上的相關工具      | Keras      |
| 如何使用/執行它？    | 在終端機中，啟動所需的 Python 版本 (root 或 py35)，執行 Python，然後匯入 Theano。 在 Jupyter 中，選取 [Python 2.7] 或 [Python 3.5] 核心，然後匯入 Theano。  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 深度學習架構      |
| 支援的 DSVM 版本      | Linux     |
| 它是如何在 DSVM 上設定/安裝的？  | Torch 是安裝在 `/dsvm/tools/torch` 中。 PyTorch 是安裝在 Python 2.7 (_root_) 及 Python 3.5 (_py35_) 環境中。   |
| 範例的連結      | Torch 範例位於 `/dsvm/samples/torch`。 PyTorch 範例位於 `/dsvm/samples/pytorch`。      |


