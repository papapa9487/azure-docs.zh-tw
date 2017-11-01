---
title: "sprint 0 2017 年 10 月的 Azure ML Workbench 版本資訊"
description: "本文件詳述 Azure ML 的 sprint 0 版本更新"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="sprint-0---october-2017"></a>Sprint 0 - 2017 年 10 月 

**版本號碼：0.1.1710.04013**

歡迎使用我們在 Microsoft Ignite 2017 會議首次公開預覽 Microsoft Azure Machine Learning Workbench 後的第一次更新。 此版本的主要更新為可靠性和穩定化修正。  我們解決的一些重大問題包括：

## <a name="new-features"></a>新功能
- 目前已支援 macOS High Sierra

## <a name="bug-fixes"></a>錯誤修正
### <a name="workbench-experience"></a>Workbench 體驗
- 將檔案拖放至 Workbench 會導致 Workbench 損毀。
- 在 VS 程式碼中設定為 IDE for Workbench 的終端機視窗無法辨識 _az ml_ 命令。

### <a name="workbench-authentication"></a>Workbench 驗證
我們做一些更新來改善回報的各種登入及驗證問題。
- 特別是當網際網路連線不穩定時，驗證視窗會持續快顯的問題。
- 改善驗證權杖過期的可靠性。
- 在某些情況下，驗證視窗會出現兩次。
- 當訊息程序實際上已完成並和已關閉快顯對話方塊時，Workbench 主視窗仍會顯示「驗證中」訊息。
- 如果沒有網際網路連線，驗證對話方塊會出現空白畫面。

### <a name="data-preparation"></a>資料準備 
- 篩選特定值後，也會篩選掉錯誤和遺漏值。
- 變更取樣策略可移除後續現有的加入作業。
- 取代遺漏值轉換不會考量 NaN。
- 遇到 null 值時，日期型別推斷會擲回例外狀況。

### <a name="job-execution"></a>作業執行
- 當作業執行因其超過大小限制而無法上傳專案資料夾時，沒有任何明確的錯誤訊息。
- 如果使用者的 Python 指令碼變更工作目錄，不會追蹤寫入至輸出資料夾的檔案。 
- 如果作用中的 Azure 訂用帳戶不同於目前的專案所屬的訂用帳戶，則提交作業會產生 403 錯誤。
- 當 Docker 不存在時，如果使用者嘗試將 Docker 做為執行目標，不傳回任何明確的錯誤訊息。
- 當使用者按一下 [執行] 按鈕時不會自動儲存 .runconfig 檔案。

### <a name="jupyter-notebook"></a>Jupyter Notebook
- 如果使用者使用特定的登入類型，則無法啟動 Notebook 伺服器。
- Notebook 伺服器錯誤訊息不會出現在使用者可看見的記錄檔中。

### <a name="azure-portal"></a>Azure 入口網站
- 選取深色的 Azure 入口網站佈景主題會將 ML 模型管理刀鋒視窗顯示為黑色方塊。

### <a name="operationalization"></a>運算化
- 重複使用要更新 web 服務的資訊清單，會使用隨機的名稱建立新的 Docker 映像。
- 無法從 Kubernetes 叢集中擷取 web 服務記錄檔。
- 當使用者嘗試建立模型管理帳戶或 ML 計算帳戶而發生權限問題時，可能造成誤導的錯誤訊息。
