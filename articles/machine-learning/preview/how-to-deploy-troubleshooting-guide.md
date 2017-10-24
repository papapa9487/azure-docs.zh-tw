---
title: "Azure Machine Learning 部署疑難排解指南 | Microsoft Docs"
description: "部署和服務建立的疑難排解指南"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 066a6a223692055c7855abc63667e345ee8dc2d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-troubleshooting"></a>模型管理疑難排解
下列資訊可協助判斷在部署期間或呼叫 web 服務時的錯誤原因。
 
## <a name="1-service-logs"></a>1.服務記錄
服務 CLI 的 `logs` 選項會提供來自 Docker 和 Kubernetes 的記錄資料。

```
az ml service logs realtime -i <web service id>
```

如需其他記錄設定，請使用 `--help` (或 `-h`) 選項。

```
az ml service logs realtime -h
```

## <a name="2-debug-and-verbose-options"></a>2.偵錯和 Verbose 選項
在部署服務時，使用 `--debug` 旗標來顯示偵錯記錄。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

在部署服務時，使用 `--verbose` 旗標來查看其他詳細資料。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

## <a name="3-app-insights"></a>3.應用程式情資
建立 web 服務來啟用要求層級記錄時，將 `-l` 旗標設定為 true。 要求記錄會寫入您在 Azure 中的 App Insights 執行個體環境。 使用您在使用 `az ml env setup` 命令時所用的環境名稱來搜尋此執行個體。

- 建立服務時，將 `-l` 設定為 true。
- 在 Azure 入口網站中開啟 App Insights。 使用您的環境名稱來尋找 App Insights 執行個體。
- 在 App Insights 後，於上方功能表中按一下搜尋來檢視結果。
- 或移至 `Analytics` > `Exceptions` > `exceptions take | 10`。


## <a name="4-error-handling-in-script"></a>4.指令碼中的錯誤處理
使用 `scoring.py` 指令碼的 **run** 函式中的例外狀況處理來傳回錯誤訊息，作為您 web 服務輸出的一部分。

Python 範例：
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="5-other-common-problems"></a>5.其他常見問題
- 如果 `env setup` 命令失敗，請確定您的訂用帳戶中有足夠的可用核心。
- 請勿在 Web 服務名稱中使用底線 ( _ ) (如 my_webservice)。
- 如果您在呼叫 Web 服務時收到 **502 錯誤的閘道**錯誤，請重試。 這通常表示容器尚未部署到叢集中。
- 如果您在建立服務時收到 **CrashLoopBackOff** 錯誤，請檢查您的記錄。 這通常是因為在 **init** 函式中缺少相依性。