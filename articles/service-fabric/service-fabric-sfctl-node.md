---
title: Azure Service Fabric CLI - sfctl node | Microsoft Docs
description: "描述 Service Fabric CLI sfctl node 命令。"
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
ms.openlocfilehash: 76037c7b4a2f7ada314a9360e3990245e6fbc06c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-node"></a>sfctl node
管理形成叢集的節點。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
|    disable       | 停用有指定停用意圖的 Service Fabric 叢集節點。|
|    enable        | 啟用目前停用的 Service Fabric 叢集節點。|
|    health        | 取得 Service Fabric 節點的健康情況。|
|    info          | 取得 Service Fabric 叢集中的節點清單。|
|    list          | 取得 Service Fabric 叢集中的節點清單。|
|    load          | 取得 Service Fabric 節點的負載資訊。|
|    remove-state  | 通知 Service Fabric 某個節點上保存的狀態已永久移除或遺失。|
|    report-health | 傳送 Service Fabric 節點的健康情況報告。|
|    restart       | 重新啟動 Service Fabric 叢集節點。|
|    transition    | 啟動或停止叢集節點。|
|    transition-status| 取得使用 StartNodeTransition 啟動的作業進度。|


## <a name="sfctl-node-disable"></a>sfctl node disable
停用有指定停用意圖的 Service Fabric 叢集節點。

停用有指定停用意圖的 Service Fabric 叢集節點。 一旦停用進行中，停用意圖可以增加，但無法降低 (例如，使用 Pause 意圖停用的節點可以進一步使用 Restart 停用，反之則無效)。 節點可在停用之後，隨時使用啟用節點的作業重新啟用。 如果停用未完成，則會取消停用。 節點若在停用時停止又恢復啟動，仍然需要重新啟用，才能將服務放上該節點。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-name [必要]| 節點的名稱。|
| --deactivation-intent | 描述停用節點的意圖或理由。 可能的值如下。 - Pause - 表示節點應該暫停。 值為 1。 - Restart - 表示意圖是要在一小段時間後重新啟動節點。 值為 2。 - RemoveData - 表示意圖是要節點移除資料。 值為 3。 .|
| --timeout -t       | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug            | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h          | 顯示此說明訊息並結束。|
| --output -o        | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query            | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose          | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-node-enable"></a>sfctl node enable
啟用目前停用的 Service Fabric 叢集節點。

啟用目前停用的 Service Fabric 叢集節點。 一旦啟用，節點就會再次成為可行目標來放置新複本，而節點上剩餘的任何已停用複本會重新啟用。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-name [必要]| 節點的名稱。|
| --timeout -t       | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug            | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h          | 顯示此說明訊息並結束。|
| --output -o        | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query            | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose          | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-node-health"></a>sfctl node health
取得 Service Fabric 節點的健康情況。

取得 Service Fabric 節點的健康情況。 使用 EventsHealthStateFilter 可根據健康情況狀態，篩選針對節點所報告的健康情況事件集合。 如果您依名稱指定的節點不在健康狀態資料存放區中，則會傳回錯誤。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-name [必要]| 節點的名稱。|
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

## <a name="sfctl-node-info"></a>sfctl node info
取得 Service Fabric 叢集中的節點清單。

取得 Service Fabric 叢集中特定節點的相關資訊。 回應包括名稱、狀態、識別碼、健康情況、執行時間，以及與節點有關的其他詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-name [必要]| 節點的名稱。|
| --timeout -t       | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug            | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h          | 顯示此說明訊息並結束。|
| --output -o        | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query            | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose          | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-node-list"></a>sfctl node list
取得 Service Fabric 叢集中的節點清單。

Nodes 端點會傳回在 Service Fabric 叢集中節點的相關資訊。 回應包括名稱、狀態、識別碼、健康情況、執行時間，以及與節點有關的其他詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --continuation-token| 接續權杖參數可用來取得下一組結果。 具有非空白值的接續權杖會在來自系統的結果無法放入單一回應中時，隨附在 API 的回應中。      當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。|
| --node-status-filter| 可根據 NodeStatus 篩選節點。 只會傳回符合指定篩選值的節點。 篩選值可以是下列其中一種。 - default - 此篩選值符合除了節點狀態為 Unknown 或 Removed 以外的所有節點。 - all - 此篩選值符合所有節點。 - up - 此篩選值符合狀態為 Up 的節點。 - down - 此篩選值符合狀態為 Down 的節點。 - enabling - 此篩選值符合正在啟用中，狀態為 Enabling 的節點。 - disabling - 此篩選值符合正在停用中，狀態為 Disabling 的節點。 - disabled - 此篩選值符合狀態為 Disabled 的節點。 - unknown - 此篩選值符合其狀態為 Unknown 的節點。 如果 Service Fabric 沒有某個節點的相關授權資訊，該節點就會處於 Unknown 狀態。 如果系統在執行階段知道某個節點，也可能會發生這個狀況。 - removed - 此篩選值符合其狀態為 Removed 的節點。 這些是使用 RemoveNodeState API 從叢集移除的節點。 .      預設值：default。|
| --timeout -t     | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug          | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h        | 顯示此說明訊息並結束。|
| --output -o      | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query          | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose        | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-node-load"></a>sfctl node load
取得 Service Fabric 節點的負載資訊。

取得 Service Fabric 節點的負載資訊。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-name [必要]| 節點的名稱。|
| --timeout -t       | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug            | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h          | 顯示此說明訊息並結束。|
| --output -o        | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query            | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose          | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-node-restart"></a>sfctl node restart
重新啟動 Service Fabric 叢集節點。

重新啟動已啟動的 Service Fabric 叢集節點。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-name [必要]| 節點的名稱。|
| --create-fabric-dump  | 指定 True 以建立網狀架構節點處理程序的傾印。 這區分大小寫。  預設值：False。|
| --node-instance-id | 目標節點的執行個體識別碼。 如果指定執行個體識別碼，只有當識別碼符合節點目前的執行個體時，才會重新啟動節點。 預設值 "0" 會比對任何執行個體識別碼。 您可以使用取得節點查詢來取得執行個體識別碼。  預設值︰0。|
| --timeout -t       | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug            | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h          | 顯示此說明訊息並結束。|
| --output -o        | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query            | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose          | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-node-transition"></a>sfctl node transition
啟動或停止叢集節點。

啟動或停止叢集節點。  叢集節點是一種處理程序，而不是作業系統執行個體本身。
若要啟動節點，請為 NodeTransitionType 參數傳入 "Start"。 若要停止節點，請為 NodeTransitionType 參數傳入 "Stop"。 此 API 會在 API 傳回節點可能尚未完成轉換時啟動作業。 呼叫有相同 OperationId 的 GetNodeTransitionProgress 可取得作業的進度。 .

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --node-instance-id [必要]| 目標節點的節點執行個體識別碼。 這可透過 GetNodeInfo API 決定。|
| --node-name [必要]| 節點的名稱。|
| --node-transition-type     [必要]| 表示要執行的轉換類型。                       NodeTransitionType.Start 會啟動已停止的節點。                       NodeTransitionType.Stop 會停止已啟動的節點。 - Invalid - 保留。  請勿傳入 API。 - Start - 將已停止的節點轉換為啟動。 - Stop - 將啟動的節點轉換為已停止。 .|
| --operation-id [必要]| 識別此 API 呼叫的 GUID。  這會傳入對應的 GetProgress API。|
| --stop-duration-in-seconds [必要]| 讓節點維持停止的持續時間，以秒為單位。  最小值是 600，最大值是 14400。 這段時間之後，節點會自動恢復啟動。|
| --timeout -t                      | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                           | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                         | 顯示此說明訊息並結束。|
| --output -o                       | 輸出格式。  允許的值：json、jsonc、table、tsv。                       預設值：json。|
| --query                           | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                         | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。