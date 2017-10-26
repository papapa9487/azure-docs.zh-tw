---
title: Azure Service Fabric CLI - sfctl partition | Microsoft Docs
description: "描述 Service Fabric CLI sfctl partition 命令。"
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
ms.openlocfilehash: 99756378f2106707b4f6d634a1183d5c32243ee2
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="sfctl-partition"></a>sfctl partition
查詢和管理任何服務的資料分割。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
|    data-loss      | 此 API 會導致指定分割區的資料遺失。|
|    data-loss-status  | 取得使用 StartDataLoss API 啟動的分割區資料遺失作業的進度。|
|    health         | 取得指定 Service Fabric 分割區的健康情況。|
|    info           | 取得 Service Fabric 分割區的相關資訊。|
|    list           | 取得 Service Fabric 服務的分割區清單。|
|    load           | 取得指定 Service Fabric 分割區的負載。|
|    load-reset     | 重設 Service Fabric 分割區目前的負載。|
|    quorum-loss    | 導致指定具狀態服務分割區的仲裁遺失。|
|    quorum-loss-status| 取得分割區上使用 StartQuorumLoss API 啟動的仲裁遺失作業的進度。|
|    recover        | 表示 Service Fabric 叢集應該嘗試復原目前停留在仲裁遺失的特定分割區。|
|    recover-all    | 表示 Service Fabric 叢集應該嘗試復原目前停留在仲裁遺失的任何服務 (包括系統服務)。|
|    report-health  | 傳送 Service Fabric 分割區的健康情況報告。|
|    restart        | 此 API 會重新啟動指定分割區的部分或所有複本或執行個體。|
|    restart-status | 取得使用 StartPartitionRestart 啟動的 PartitionRestart 作業的進度。|
|    svc-name       | 取得分割區的 Service Fabric 服務名稱。|


## <a name="sfctl-partition-health"></a>sfctl partition health
取得指定 Service Fabric 分割區的健康情況。

取得指定分割區的健康情況資訊。 使用 EventsHealthStateFilter 可根據健康情況狀態，篩選針對服務所報告的健康情況事件集合。
使用 ReplicasHealthStateFilter 可篩選分割區上 ReplicaHealthState 物件的集合。 如果您指定的分割區不在健康狀態資料存放區中，此 Cmdlet 會傳回錯誤。 .

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --partition-id [必要]| 分割區的識別。|
| --events-health-state-filter  | 可根據健康情況狀態篩選傳回的 HealthEvent 物件集合。 此參數的可能值包括下列其中一個健康情況狀態的整數值。                只會傳回符合篩選條件的事件。 所有事件都會用來評估彙總的健康情況狀態。 如果未指定，則會傳回所有項目。 狀態值是以旗標為基礎的列舉，因此值可以是使用位元 'OR' 運算子取得的這些值的組合。 例如，如果提供的值為 6，則會傳回 HealthState 值為 OK (2) 和 Warning (4) 的所有事件。 - Default - 預設值。 符合任何 HealthState。 值為零。 - None - 不符合任何 HealthState 值的篩選條件。 用來在指定狀態集合沒有任何結果時傳回。 值為 1。 - Ok - 符合輸入含 HealthState 值 Ok 的篩選條件。 值為 2。 - Warning - 符合輸入含 HealthState 值 Warning 的篩選條件。 值為 4。 - Error - 符合輸入含 HealthState 值 Error 的篩選條件。 值為 8。                - All - 符合輸入含任何 HealthState 值的篩選條件。                值為 65535。|
|--exclude-health-statistics   | 表示健康情況統計資料是否傳回為查詢結果的一部分。 預設為 False。 統計資料顯示健康情況狀態為 Ok、Warning 和 Error 的子項目數目。|
| --replicas-health-state-filter| 可篩選分割區上 ReplicaHealthState 物件的集合。 值可以從 HealthStateFilter 的成員或對成員的位元運算取得。 只會傳回符合篩選條件的複本。 所有複本都會用來評估彙總的健康情況狀態。 如果未指定，則會傳回所有項目。 狀態值是以旗標為基礎的列舉，因此值可以是使用位元 'OR' 運算子取得的這些值的組合。 例如，如果提供的值為 6，則會傳回 HealthState 值為 OK (2) 和 Warning (4) 的所有事件。 此參數的可能值包括下列其中一個健康情況狀態的整數值。 - Default - 預設值。 符合任何 HealthState。 值為零。 - None - 不符合任何 HealthState 值的篩選條件。 用來在指定狀態集合沒有任何結果時傳回。 值為 1。 - Ok - 符合輸入含 HealthState 值 Ok 的篩選條件。 值為 2。 - Warning - 符合輸入含 HealthState 值 Warning 的篩選條件。 值為 4。 - Error - 符合輸入含 HealthState 值 Error 的篩選條件。 值為 8。 - All - 符合輸入含任何 HealthState 值的篩選條件。 值為 65535。|
| --timeout -t               | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                    | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                  | 顯示此說明訊息並結束。|
| --output -o                | 輸出格式。  允許的值：json、jsonc、table、tsv。                預設值：json。|
| --query                    | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。 |
| --verbose                  | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-partition-info"></a>sfctl partition info
取得 Service Fabric 分割區的相關資訊。

Partitions 端點會傳回指定分割區的相關資訊。 回應包括分割區識別碼、資料分割配置資訊、分割區支援的索引鍵、狀態、健康情況，以及與分割區有關的其他詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --partition-id [必要]| 分割區的識別。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-partition-list"></a>sfctl partition list
取得 Service Fabric 服務的分割區清單。

取得 Service Fabric 服務的分割區清單。 分割區識別碼、資料分割配置資訊、分割區支援的索引鍵、狀態、健康情況，以及與分割區有關的其他詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --service-id [必要]| 服務的身分識別。 這通常是沒有 'fabric:' URI 配置之服務的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果服務名稱是"fabric://myapp/app1/svc1"，服務識別在 6.0+ 中會是 "myapp~app1~svc1"，在舊版中會是 "myapp/app1/svc1"。|
| --continuation-token| 接續權杖參數可用來取得下一組結果。         具有非空白值的接續權杖會在來自系統的結果無法放入單一回應中時，隨附在 API 的回應中。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。|
| --timeout -t        | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug             | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h           | 顯示此說明訊息並結束。|
| --output -o         | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query             | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose           | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-partition-load"></a>sfctl partition load
取得指定 Service Fabric 分割區的負載。

傳回指定分割區的相關資訊。 回應包括負載資訊清單。 每個資訊都包括負載計量名稱、值和上次報告的時間 (UTC)。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --partition-id [必要]| 分割區的識別。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-partition-recover"></a>sfctl partition recover
表示 Service Fabric 叢集應該嘗試復原目前停留在仲裁遺失的特定分割區。

表示 Service Fabric 叢集應該嘗試復原目前停留在仲裁遺失的特定分割區。 只有當已知無法復原已關閉的複本時，才能執行這項作業。 不正確使用這個 API，可能會造成資料遺失。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --partition-id [必要]| 分割區的識別。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-partition-restart"></a>sfctl partition restart
此 API 會重新啟動指定分割區的部分或所有複本或執行個體。

此 API 在測試容錯移轉時很有用。 如果用來針對無狀態服務的分割區，RestartPartitionMode 必須是 AllReplicasOrInstances。 使用相同的 OperationId 呼叫 GetPartitionRestartProgress API 可取得進度。 .

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --operation-id [必要]| 識別此 API 呼叫的 GUID。  這會傳入對應的 GetProgress API。|
| --partition-id [必要]| 分割區的識別。|
| --restart-partition-mode [必要]| - Invalid - 保留。  請勿傳入 API。 - AllReplicasOrInstances - 分割區中的所有複本或執行個體都會立即重新啟動。 - OnlyActiveSecondaries - 只有次要複本會重新啟動。 .|
| --service-id [必要]| 服務的識別。 這通常是服務沒有 'fabric:' URI 配置的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果服務名稱是"fabric://myapp/app1/svc1"，服務識別在 6.0+ 中會是 "myapp~app1~svc1"，在舊版中會是 "myapp/app1/svc1"。|
| --timeout -t                    | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                         | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                       | 顯示此說明訊息並結束。|
| --output -o                     | 輸出格式。  允許的值：json、jsonc、table、tsv。                     預設值：json。|
| --query                         | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                       | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。
