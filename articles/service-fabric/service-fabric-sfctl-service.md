---
title: Azure Service Fabric CLI- sfctl service | Microsoft Docs
description: "描述 Service Fabric CLI sfctl service 命令。"
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
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 66649bb6ae317eb227dcdf45aa084905967c117f
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---
# <a name="sfctl-service"></a>sfctl service
建立、刪除與管理服務、服務類型和服務套件。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
|    app-name       | 取得服務的 Service Fabric 應用程式名稱。|
|    code-package-list | 取得 Service Fabric 節點上所部署的程式碼套件清單。|
|    create         | 從描述建立指定的 Service Fabric 服務。|
|    delete         | 刪除現有 Service Fabric 服務。|
|    deployed-type  | 取得 Service Fabric 叢集節點上所部署應用程式之指定服務類型的相關資訊。|
|    deployed-type-list| 取得一份清單，內含 Service Fabric 叢集節點上所部署應用程式之服務類型的相關資訊。|
|    說明    | 取得現有 Service Fabric 服務的描述。|
|    health         | 取得所指定 Service Fabric 服務的健康情況。|
|    info           | 取得屬於 Service Fabric 應用程式之特定服務的相關資訊。|
|    list           | 取得屬於應用程式識別碼所指定應用程式之所有服務的相關資訊。|
|    manifest       | 取得可描述服務類型的資訊清單。|
|    package-deploy | 將與所指定服務資訊清單相關聯的套件下載至所指定節點上的映像快取。|
|    package-health | 取得針對 Service Fabric 節點和應用程式所部署之特定應用程式的服務套件健康情況相關資訊。|
|    package-info   | 取得 Service Fabric 節點上所部署且完全符合指定名稱的服務套件清單。|
|    package-list   | 取得 Service Fabric 節點上所部署的服務套件清單。|
|    recover        | 表示 Service Fabric 叢集應該嘗試復原目前停留在仲裁遺失的指定服務。|
|    report-health  | 傳送 Service Fabric 服務的健康情況報告。|
|    resolve        | 解析 Service Fabric 資料分割。|
|    type-list      | 取得一份清單，內含 Service Fabric 叢集中已佈建應用程式類型所支援服務類型的相關資訊。|
|    update         | 使用指定的更新描述來更新指定的服務。|


## <a name="sfctl-service-create"></a>sfctl service create
從描述建立指定的 Service Fabric 服務。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-id       [必要]| 父應用程式的身分識別。 這通常是沒有 'fabric:' URI 配置之應用程式的完整識別碼。 從 6.0 版開始，階層的名稱會以 '~' 字元分隔。 例如，如果應用程式名稱是 'fabric://myapp/app1'，則應用程式識別在 6.0+ 中會是 'myapp~app1'，而在舊版中會是 'myapp/app1'。|
| --name         [必要]| 服務的名稱。 這應該是應用程式識別碼的子系。           這是包含 `fabric:` URI 的完整名稱。 例如，服務 `fabric:/A/B` 是應用程式 `fabric:/A` 的子項目。|
| --service-type [必要]| 服務類型的名稱。|
| --activation-mode     | 服務套件的啟用模式。|
| --constraints         | 以字串表示的放置限制式。 放置限制式是節點屬性上的布林運算式，並允許根據服務需求將服務限制為特定節點。 例如，若要在 NodeType 為 blue 的節點上放置服務，請指定下列項目："NodeColor == blue"。|
| --correlated-service  | 要與之建立相互關聯的目標服務名稱。|
| --correlation         | 使用對齊親和性，相互關聯服務與現有服務。|
| --dns-name            | 要建立之服務的 DNS 名稱。 必須啟用這個設定的 Service Fabric DNS 系統服務。|
| --instance-count      | 執行個體計數。 這只適用於無狀態服務。|
| --int-scheme          | 指出應該在某範圍的不帶正負號整數之間一致地分割服務。|
| --int-scheme-count    | 整數索引鍵範圍內要建立的資料分割數目 (適用於統一整數資料分割配置)。|
| --int-scheme-high     | 使用統一整數資料分割配置時的索引鍵整數範圍結尾。|
| --int-scheme-low      | 使用統一整數資料分割配置時的索引鍵整數範圍開頭。|
| --load-metrics        | 在節點之間負載平衡服務時所使用計量的 JSON 編碼清單。|
| --min-replica-set-size| 以數字表示的最小複本集大小。 這只適用於具狀態服務。|
| --move-cost           | 指定服務的移動成本。 可能的值為：'Zero'、'Low'、'Medium'、'High'。|
| --named-scheme        | 指出服務應該有多個具名資料分割。|
| --named-scheme-list   | 使用具名資料分割配置時，在其間分割服務之名稱的 JSON 編碼清單。|
| --no-persisted-state  | 如果為 true，這表示服務未在本機磁碟上儲存持續性狀態，或只會將狀態儲存至記憶體中。|
| --placement-policy-list  | 服務放置原則的 JSON 編碼清單，以及任何相關聯的網域名稱。 原則可以是下列其中一個或多個：`NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution`。|
| --quorum-loss-wait    | 允許處於仲裁遺失狀態之資料分割的最大持續時間 (以秒為單位)。 這只適用於具狀態服務。|
| --replica-restart-wait| 關閉複本時與建立新複本時之間的持續時間 (秒)。 這只適用於具狀態服務。|
| --singleton-scheme    | 指出服務應該有單一資料分割，或為非分割服務。|
| --stand-by-replica-keep  | StandBy 複本在移除之前的最長維護持續時間 (秒)。 這只適用於具狀態服務。|
| --stateful            | 指出服務是具狀態服務。|
| --stateless           | 指出服務是具狀態服務。|
| --target-replica-set-size| 以數字表示的目標複本集大小。 這只適用於具狀態服務。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-service-delete"></a>sfctl service delete
刪除現有 Service Fabric 服務。

刪除現有 Service Fabric 服務。 必須先建立服務，才能將它刪除。 Service Fabric 預設會嘗試以正常方式關閉服務複本，然後刪除該服務。 不過，如果服務在正常關閉複本時發生問題，則刪除作業可能需要很長的時間或停滯。 使用選擇性 ForceRemove 旗標來略過正常關閉順序，並強制刪除服務。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --service-id [必要]| 服務的身分識別。 這通常是沒有 'fabric:' URI 配置之服務的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果服務名稱是 "fabric://myapp/app1/svc1"，則服務識別在 6.0+ 中會是 "myapp~app1~svc1"，而在舊版中會是 "myapp/app1/svc1"。|
| --force-remove      | 強制移除 Service Fabric 應用程式或服務，而不需要經過正常關機順序。 這個參數可以用來強制刪除應用程式或服務，而其刪除因無法正常關閉複本之服務程式碼的問題而逾時。|
| --timeout -t        | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug             | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h           | 顯示此說明訊息並結束。|
| --output -o         | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query             | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose           | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-service-description"></a>sfctl service description
取得現有 Service Fabric 服務的描述。

取得現有 Service Fabric 服務的描述。 必須先建立服務，才能取得其描述。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --service-id [必要]| 服務的身分識別。 這通常是沒有 'fabric:' URI 配置之服務的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果服務名稱是 "fabric://myapp/app1/svc1"，則服務識別在 6.0+ 中會是 "myapp~app1~svc1"，而在舊版中會是 "myapp/app1/svc1"。|
| --timeout -t        | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug             | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h           | 顯示此說明訊息並結束。|
| --output -o         | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query             | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose           | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-service-health"></a>sfctl service health
取得所指定 Service Fabric 服務的健康情況。

取得所指定服務的健康情況資訊。 使用 EventsHealthStateFilter 可根據健康情況狀態，篩選針對服務所報告的健康情況事件集合。 使用 PartitionsHealthStateFilter 來篩選所傳回資料分割的集合。 如果您指定的服務不在健康狀態資料存放區中，此 Cmdlet 會傳回錯誤。 .

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --service-id          [必要]| 服務的身分識別。 這通常是沒有 'fabric:' URI 配置之服務的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果服務名稱是 "fabric://myapp/app1/svc1"，則服務識別在 6.0+ 中會是 "myapp~app1~svc1"，而在舊版中會是 "myapp/app1/svc1"。|
| --events-health-state-filter | 可根據健康情況狀態來篩選所傳回的 HealthEvent 物件集合。 此參數的可能值包括下列其中一個健康情況狀態的整數值。 只會傳回符合篩選條件的事件。 所有事件都會用來評估彙總的健康情況狀態。 如果未指定，則會傳回所有項目。 狀態值是以旗標為基礎的列舉，因此值可以是使用位元 'OR' 運算子取得的這些值的組合。 例如，如果提供的值為 6，則會傳回 HealthState 值為 OK (2) 和 Warning (4) 的所有事件。 -                  Default - 預設值。 符合任何 HealthState。 值為零。 - None - 不符合任何 HealthState 值的篩選條件。 用來在指定狀態集合沒有任何結果時傳回。 值為 1。 - Ok - 符合輸入含 HealthState 值 Ok 的篩選條件。 值為 2。 -                  Warning - 符合輸入含 HealthState 值 Warning 的篩選條件。 值為 4。 - Error - 符合輸入含 HealthState 值 Error 的篩選條件。 值為 8。 - All - 符合輸入含任何 HealthState 值的篩選條件。 值為 65535。|
|--exclude-health-statistics     | 表示健康情況統計資料是否傳回為查詢結果的一部分。 預設為 False。 統計資料顯示健康情況狀態為 Ok、Warning 和 Error 的子項目數目。|
| --partitions-health-state-filter| 可根據健康情況狀態來篩選服務健康情況查詢結果中所傳回的資料分割健康情況狀態物件。 此參數的可能值包括下列其中一個健康情況狀態的整數值。 只會傳回符合篩選條件的資料分割。 所有資料分割都會用來評估彙總的健康情況狀態。 如果未指定，則會傳回所有項目。 狀態值是以旗標為基礎的列舉，因此值可以是使用位元 'OR' 運算子取得的這些值的組合。 例如，如果提供的值為 "6"，則會傳回 HealthState 值為 OK (2) 和 Warning (4) 的資料分割健康情況狀態。 - Default - 預設值。 符合任何 HealthState。                  值為零。 - None - 不符合任何 HealthState 值的篩選條件。 用來在指定狀態集合沒有任何結果時傳回。 值為 1。 - Ok - 符合輸入含 HealthState 值 Ok 的篩選條件。 值為 2。 -                  Warning - 符合輸入含 HealthState 值 Warning 的篩選條件。 值為 4。 - Error - 符合輸入含 HealthState 值 Error 的篩選條件。 值為 8。 - All - 符合輸入含任何 HealthState 值的篩選條件。 值為 65535。|
| --timeout -t                 | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                      | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                    | 顯示此說明訊息並結束。|
| --output -o                  | 輸出格式。  允許的值：json、jsonc、table、tsv。                  預設值：json。|
| --query                      | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                    | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-service-info"></a>sfctl service info
取得屬於 Service Fabric 應用程式之特定服務的相關資訊。

傳回屬於所指定 Service Fabric 應用程式之指定服務的相關資訊。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-id [必要]| 應用程式的身分識別。 這通常是沒有 'fabric:' URI 配置之應用程式的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果應用程式名稱是 "fabric://myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp~app1"，而在舊版中會是 "myapp/app1"。|
| --service-id     [必要]| 服務的身分識別。 這通常是沒有 'fabric:' URI 配置之服務的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果服務名稱是 "fabric://myapp/app1/svc1"，則服務識別在 6.0+ 中會是 "myapp~app1~svc1"，而在舊版中會是 "myapp/app1/svc1"。|
| --timeout -t            | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                 | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h               | 顯示此說明訊息並結束。|
| --output -o             | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query                 | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose               | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-service-list"></a>sfctl service list
取得屬於應用程式識別碼所指定應用程式之所有服務的相關資訊。

傳回屬於應用程式識別碼所指定應用程式之所有服務的相關資訊。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-id [必要]| 應用程式的身分識別。 這通常是沒有 'fabric:' URI 配置之應用程式的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果應用程式名稱是 "fabric://myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp~app1"，而在舊版中會是 "myapp/app1"。|
| --continuation-token    | 接續權杖參數可用來取得下一組結果。 具有非空白值的接續權杖會在來自系統的結果無法放入單一回應中時，隨附在 API 的回應中。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。|
| --service-type-name     | 用來篩選要查詢之服務的服務類型名稱。|
| --timeout -t            | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                 | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h               | 顯示此說明訊息並結束。|
| --output -o             | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query                 | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose               | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-service-manifest"></a>sfctl service manifest
取得可描述服務類型的資訊清單。

取得可描述服務類型的資訊清單。 回應會將服務資訊清單 XML 包含為字串。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-type-name    [必要]| 應用程式類型的名稱。|
| --application-type-version [必要]| 應用程式類型的版本。|
| --service-manifest-name    [必要]| 註冊為 Service Fabric 叢集中應用程式類型一部分的服務資訊清單名稱。|
| --timeout -t                      | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                           | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                         | 顯示此說明訊息並結束。|
| --output -o                       | 輸出格式。  允許的值：json、jsonc、table、tsv。                       預設值：json。|
| --query                           | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                         | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-service-recover"></a>sfctl service recover
表示 Service Fabric 叢集應該嘗試復原目前停留在仲裁遺失的指定服務。

表示 Service Fabric 叢集應該嘗試復原目前停留在仲裁遺失的指定服務。 只有無法復原已關閉的複本時，才能執行這項作業。 不正確使用這個 API，可能會造成資料遺失。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --service-id [必要]| 服務的身分識別。 這通常是沒有 'fabric:' URI 配置之服務的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果服務名稱是 "fabric://myapp/app1/svc1"，則服務識別在 6.0+ 中會是 "myapp~app1~svc1"，而在舊版中會是 "myapp/app1/svc1"。|
| --timeout -t        | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug             | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h           | 顯示此說明訊息並結束。|
| --output -o         | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query             | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose           | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-service-resolve"></a>sfctl service resolve
解析 Service Fabric 資料分割。

解析 Service Fabric 服務資料分割，以取得服務複本的端點。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --service-id [必要]| 服務的身分識別。 這通常是沒有 'fabric:' URI 配置之服務的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果服務名稱是 "fabric://myapp/app1/svc1"，則服務識別在 6.0+ 中會是 "myapp~app1~svc1"，而在舊版中會是 "myapp/app1/svc1"。|
| --partition-key-type| 資料分割的索引鍵類型。 如果服務的資料分割配置是 Int64Range 或 Named，則需要此參數。 可能的值如下。 - None (1) - 指出未指定 PartitionKeyValue 參數。 這適用於資料分割配置為 Singleton 的資料分割。 這是預設值。 值為 1。 - Int64Range (2) - 指出 PartitionKeyValue 參數是 int64 資料分割索引鍵。 這適用於資料分割配置為 Int64Range 的資料分割。 值為 2。 - Named (3) -         指出 PartitionKeyValue 參數是資料分割名稱。 這適用於資料分割配置為 Named 的資料分割。 值為 3。|
| --partition-key-value  | 資料分割索引鍵。 如果服務的資料分割配置是 Int64Range 或 Named，則需要此項目。|
| --previous-rsp-version | 先前收到之回應的 [版本] 欄位中的值。 如果使用者知道先前取得的結果已過時，則這是必要的。|
| --timeout -t        | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug             | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h           | 顯示此說明訊息並結束。|
| --output -o         | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query             | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose           | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-service-update"></a>sfctl service update
使用指定的更新描述來更新指定的服務。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --service-id   [必要]| 要更新的目標服務。 這通常是沒有 'fabric:' URI 配置之服務的完整識別碼。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果服務名稱是 'fabric://myapp/app1/svc1'，則服務識別在 6.0+ 中會是 'myapp~app1~svc1'，而在舊版中會是 'myapp/app1/svc1'。|
| --constraints         | 以字串表示的放置限制式。 放置限制式是節點屬性上的布林運算式，並允許根據服務需求將服務限制為特定節點。 例如，若要在 NodeType 為 blue 的節點上放置服務，請指定下列項目："NodeColor == blue"。|
| --correlated-service  | 要與之建立相互關聯的目標服務名稱。|
| --correlation         | 使用對齊親和性，相互關聯服務與現有服務。|
| --instance-count      | 執行個體計數。 這只適用於無狀態服務。|
| --load-metrics        | 在節點之間進行負載平衡時所使用計量的 JSON 編碼清單。|
| --min-replica-set-size| 以數字表示的最小複本集大小。 這只適用於具狀態服務。|
| --move-cost           | 指定服務的移動成本。 可能的值為：'Zero'、'Low'、'Medium'、'High'。|
| --placement-policy-list  | 服務放置原則的 JSON 編碼清單，以及任何相關聯的網域名稱。 原則可以是下列其中一個或多個：`NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution`。|
| --quorum-loss-wait    | 允許處於仲裁遺失狀態之資料分割的最大持續時間 (以秒為單位)。 這只適用於具狀態服務。|
| --replica-restart-wait| 關閉複本時與建立新複本時之間的持續時間 (秒)。 這只適用於具狀態服務。|
| --stand-by-replica-keep  | StandBy 複本在移除之前的最長維護持續時間 (秒)。 這只適用於具狀態服務。|
| --stateful            | 指出目標服務是具狀態服務。|
| --stateless           | 指出目標服務是無狀態服務。|
| --target-replica-set-size| 以數字表示的目標複本集大小。 這只適用於具狀態服務。|
| --timeout -t          | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug               | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h             | 顯示此說明訊息並結束。|
| --output -o           | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query               | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose             | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。
