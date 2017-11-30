---
title: Azure Service Fabric CLI - sfctl replica | Microsoft Docs
description: "描述 Service Fabric CLI sfctl replica 命令。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: bd16dd889cbe0f05d7e60f444c6c5fa2e65f64a4
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2017
---
# <a name="sfctl-replica"></a>sfctl replica
管理屬於服務分割區的複本。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
|    deployed  | 取得在 Service Fabric 節點上部署的複本詳細資料。|
|    deployed-list| 取得在 Service Fabric 節點上部署的複本清單。|
|    health    | 取得 Service Fabric 具狀態服務複本或無狀態服務執行個體的健康情況。|
|    info      | 取得 Service Fabric 分割區複本的相關資訊。|
|    list      | 取得 Service Fabric 服務分割區複本的相關資訊。|
|    remove    | 移除在節點上執行的服務複本。|
|    report-health| 傳送 Service Fabric 複本的健康情況報告。|
|    restart   | 重新啟動節點上執行之持續性服務的服務複本。|


## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
取得在 Service Fabric 節點上部署的複本詳細資料。

取得在 Service Fabric 節點上部署的複本詳細資料。 此資訊包含服務類型、服務名稱、目前的服務作業、目前的服務作業開始日期時間、分割區識別碼、複本/執行個體識別碼、報告的負載和其他資訊。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-name [必要]| 節點的名稱。|
| --partition-id [必要]| 分割區的識別。|
| --replica-id [必要]| 複本的識別碼。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-replica-health"></a>sfctl replica health
取得 Service Fabric 具狀態服務複本或無狀態服務執行個體的健康情況。

取得 Service Fabric 複本的健康情況。 使用 EventsHealthStateFilter 可根據健康情況狀態，篩選針對複本所報告的健康情況事件集合。 .

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --partition-id [必要]| 分割區的識別。|
| --replica-id [必要]| 複本的識別碼。|
| --events-health-state-filter| 可根據健康情況狀態篩選傳回的 HealthEvent 物件集合。 此參數的可能值包括下列其中一個健康情況狀態的整數值。 只會傳回符合篩選條件的事件。 所有事件都會用來評估彙總的健康情況狀態。 如果未指定，則會傳回所有項目。 狀態值是以旗標為基礎的列舉，因此值可以是使用位元 'OR' 運算子取得的這些值的組合。 例如，如果提供的值為 6，則會傳回 HealthState 值為 OK (2) 和 Warning (4) 的所有事件。 - Default - 預設值。 符合任何 HealthState。 值為零。 - None - 不符合任何 HealthState 值的篩選條件。 用來在指定狀態集合沒有任何結果時傳回。 值為 1。 - Ok - 符合輸入含 HealthState 值 Ok 的篩選條件。 值為 2。 - Warning - 符合輸入含 HealthState 值 Warning 的篩選條件。 值為 4。 - Error - 符合輸入含 HealthState 值 Error 的篩選條件。 值為 8。 - All - 符合輸入含任何 HealthState 值的篩選條件。 值為 65535。|
| --timeout -t             | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                  | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                | 顯示此說明訊息並結束。|
| --output -o              | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query                  | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-replica-info"></a>sfctl replica info
取得 Service Fabric 分割區複本的相關資訊。

回應包括識別碼、角色、狀態、健康情況、節點名稱、執行時間，以及與複本有關的其他詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --partition-id [必要]| 分割區的識別。|
| --replica-id [必要]| 複本的識別碼。|
| --continuation-token  | 接續權杖參數可用來取得下一組結果。 具有非空白值的接續權杖會在來自系統的結果無法放入單一回應中時，隨附在 API 的回應中。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-replica-list"></a>sfctl replica list
取得 Service Fabric 服務分割區複本的相關資訊。

GetReplicas 端點會傳回指定分割區複本的相關資訊。
回應包括識別碼、角色、狀態、健康情況、節點名稱、執行時間，以及與複本有關的其他詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --partition-id [必要]| 分割區的識別。|
| --continuation-token  | 接續權杖參數可用來取得下一組結果。 具有非空白值的接續權杖會在來自系統的結果無法放入單一回應中時，隨附在 API 的回應中。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-replica-remove"></a>sfctl replica remove
移除在節點上執行的服務複本。

此 API 會從 Service Fabric 叢集移除複本，來模擬 Service Fabric 複本失敗。 移除作業會關閉複本，將複本轉換成 None 角色，然後從叢集移除複本所有的狀態資訊。 此 API 會測試複本狀態移除路徑，並透過用戶端 API 模擬報告錯誤永久路徑。 警告：使用此 API 時，不會執行任何安全性檢查。 不正確使用此 API 可能會導致具狀態服務的資料遺失。此外，forceRemove 旗標會影響裝載於相同處理序的所有其他複本。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-name [必要]| 節點的名稱。|
| --partition-id [必要]| 分割區的識別。|
| --replica-id [必要]| 複本的識別碼。|
| --force-remove        | 強制移除 Service Fabric 應用程式或服務，而不需要經過正常關機順序。 當服務程式碼中有無法正常關閉複本的問題而導致刪除逾時時，此參數可用來強制刪除應用程式或服務。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-replica-restart"></a>sfctl replica restart
重新啟動節點上執行之持續性服務的服務複本。

重新啟動節點上執行之持續性服務的服務複本。 警告：使用此 API 時，不會執行任何安全性檢查。 不正確使用此 API 可能會導致具狀態服務無法使用。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-name [必要]| 節點的名稱。|
| --partition-id [必要]| 分割區的識別。|
| --replica-id [必要]| 複本的識別碼。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。
