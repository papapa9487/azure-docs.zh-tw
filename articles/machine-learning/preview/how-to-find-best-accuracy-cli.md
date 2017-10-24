---
title: "如何在 Azure Machine Learning Workbench 中找出精確度最佳且持續時間最短的執行 | Microsoft Docs"
description: "一個使用 Azure Machine Learning Workbench 透過 CLI 找出最佳精確度的端對端案例"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 9e5c2cc0b9ec17154c5280850d971308abfc2eb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-runs-with-the-best-accuracy-and-lowest-duration"></a>如何找出精確度最佳且持續時間最短的執行
當有多個執行時，其中一個使用案例是找出精確度最佳的執行。 其中一個方法是使用命令列介面 (CLI) 搭配 [JMESPath](http://jmespath.org/) 查詢。 如需有關在 Azure CLI 中使用 JMESPath 的詳細資訊，請參閱[這篇文章](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)。 在以下範例中，會建立 4 個執行，精確度值分別為 0、0.98、1 及 1。 執行如果符合範圍 `[MaxAccuracy-Threshold, MaxAccuracy]` (其中 `Threshold = .03`)，就會被篩選出來。

## <a name="sample-data"></a>範例資料
如果您沒有具有 `Accuracy` 值的現有執行，下列步驟將會產生可供查詢的執行：

首先，在工作台中建立一個 Python 檔案，將其命名為 `log_accuracy.py`，然後貼上下列程式碼：
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

接著，建立 `run.py` 檔案，然後貼上下列程式碼︰
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

最後，透過工作台開啟 CLI，然後執行 `python run.py` 命令來提交 4 個實驗。 在指令碼執行完成後，您應該會在 [`Run History`] \(執行歷程記錄\) 中看到多出 4 個執行。

## <a name="querying-the-run-history"></a>查詢執行歷程記錄
第一個命令會找出最大精確度值。
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

在使用 `1` 這個最大精確度值和臨界值 `0.03` 的情況下，第二個命令將會使用 `Accuracy` 來篩選執行，然後依據 `duration` 以遞增順序排序執行。
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> 如果您想要進行嚴格的上限檢查，則查詢格式會是 ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

如果您使用 Powershell，則以下程式碼將會使用區域變數來儲存臨界值和最大精確度。
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>後續步驟
- 如需有關記錄的詳細資訊，請參閱[如何在 Azure Machine Learning Workbench 中使用執行歷程記錄和模型計量](how-to-use-run-history-model-metrics.md)。    
