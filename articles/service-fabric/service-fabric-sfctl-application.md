---
title: Azure Service Fabric CLI- sfctl application| Microsoft Docs
description: "描述 Service Fabric CLI sfctl application 命令。"
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
ms.openlocfilehash: 82d2024f567768e784d9d8697784d06b56bc08ed
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="sfctl-application"></a>sfctl application
建立、刪除與管理應用程式和應用程式類型。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
| create       | 使用指定的描述來建立 Service Fabric 應用程式。|
| delete       | 刪除現有 Service Fabric 應用程式。|
| deployed     | 取得 Service Fabric 節點上所部署應用程式的相關資訊。|
| deployed-health | 取得 Service Fabric 節點上所部署應用程式健康情況的相關資訊。|
| deployed-list| 取得 Service Fabric 節點上所部署的應用程式清單。|
| health       | 取得 Service Fabric 應用程式的健康情況。|
| info         | 取得 Service Fabric 應用程式的相關資訊。|
| list         | 取得 Service Fabric 叢集中所建立且符合指定為參數之篩選的應用程式清單。|
| load | 取得 Service Fabric 應用程式的載入資訊。 |
| manifest     | 取得可描述應用程式類型的資訊清單。|
| provision    | 向叢集佈建或註冊 Service Fabric 應用程式類型。|
| report-health| 傳送 Service Fabric 應用程式的健康情況報告。|
| 類型         | 取得 Service Fabric 叢集中完全符合指定名稱的應用程式類型清單。|
| type-list    | 取得 Service Fabric 叢集中的應用程式類型清單。|
| unprovision  | 從叢集移除或取消註冊 Service Fabric 應用程式類型。|
| 升級      | 開始在 Service Fabric 叢集中升級應用程式。|
| upgrade-resume  | 繼續在 Service Fabric 叢集中升級應用程式。|
| upgrade-rollback| 開始復原目前正在 Service Fabric 叢集中進行的應用程式升級。|
| upgrade-status  | 取得在這個應用程式上執行之最新升級的詳細資料。|
| upload       | 將 Service Fabric 應用程式套件複製到映像存放區。|

## <a name="sfctl-application-create"></a>sfctl application create
使用指定的描述來建立 Service Fabric 應用程式。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-name    [必要]| 應用程式名稱，包括 'fabric:' URI 配置。|
| --app-type    [必要]| 在應用程式資訊清單中找到的應用程式類型名稱。|
| --app-version [必要]| 應用程式資訊清單中所定義的應用程式類型版本。|
| --max-node-count     | Service Fabric 保留這個應用程式容量的節點數目上限。 這不表示會將此應用程式的服務放在所有這些節點上。|
| --metrics            | 應用程式容量計量描述的 JSON 編碼清單。 計量定義為名稱，並與應用程式所在之每一個節點的一組功能相關聯。|
| --min-node-count     | Service Fabric 保留這個應用程式容量的節點數目下限。 這不表示會將此應用程式的服務放在所有這些節點上。|
| --parameters         | 要在建立應用程式時套用之應用程式參數覆寫的 JSON 編碼清單。|
| --timeout -t         | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug              | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h            | 顯示此說明訊息並結束。|
| --output -o          | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query              | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose            | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-delete"></a>sfctl application delete
刪除現有 Service Fabric 應用程式。

刪除現有 Service Fabric 應用程式。 必須先建立應用程式，才能將它刪除。 刪除應用程式會刪除為該應用程式一部分的所有服務。 Service Fabric 預設會嘗試以正常方式關閉服務複本，然後刪除該服務。 不過，如果服務在正常關閉複本時發生問題，則刪除作業可能需要很長的時間或停滯。 使用選擇性 ForceRemove 旗標來略過正常關閉順序，並強制刪除應用程式和其所有服務。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-id [必要]| 應用程式的身分識別。 這通常是沒有 'fabric:' URI 配置之應用程式的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果應用程式名稱是 "fabric://myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp~app1"，而在舊版中會是 "myapp/app1"。|
| --force-remove          | 強制移除 Service Fabric 應用程式或服務，而不需要經過正常關機順序。 當服務程式碼中有無法正常關閉複本的問題而導致刪除逾時時，此參數可用來強制刪除應用程式或服務。|
| --timeout -t            | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                 | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h               | 顯示此說明訊息並結束。|
| --output -o             | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query                 | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose               | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
取得 Service Fabric 節點上所部署應用程式的相關資訊。
     
### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-id [必要]| 應用程式的身分識別。 這通常是沒有 'fabric:' URI 配置之應用程式的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果應用程式名稱是 "fabric://myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp~app1"，而在舊版中會是 "myapp/app1"。|
| --node-name      [必要]| 節點的名稱。|
| --timeout -t            | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                 | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h               | 顯示此說明訊息並結束。|
| --output -o             | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query                 | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose               | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-health"></a>sfctl application health
取得 Service Fabric 應用程式的健康情況。

傳回 Service Fabric 應用程式的健康情況狀態。 回應會報告 Ok、Error 或 Warning 健康情況狀態。 如果在健康狀態資料存放區中找不到實體，則會傳回 Error。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-id                 [必要]| 應用程式的身分識別。 這通常是沒有 'fabric:' URI 配置之應用程式的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果應用程式名稱是 "fabric://myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp~app1"，而在舊版中會是 "myapp/app1"。|
| --deployed-applications-health-state-filter| 可根據健康情況狀態來篩選應用程式健康情況查詢結果中所傳回的已部署應用程式健康情況狀態物件。 此參數的可能值包括下列其中一個健康情況狀態的整數值。 只會傳回符合篩選條件的已部署應用程式。 所有已部署應用程式都會用來評估彙總的健康情況狀態。 如果未指定，則會傳回所有項目。 狀態值是以旗標為基礎的列舉，因此值可以是使用位元 'OR' 運算子取得的這些值的組合。 例如，如果提供的值為 6，則會傳回 HealthState 值為 OK (2) 和 Warning (4) 的已部署應用程式健康情況狀態。 - Default - 預設值。 符合任何 HealthState。 值為零。 - None - 不符合任何 HealthState 值的篩選條件。 用來在指定狀態集合沒有任何結果時傳回。 值為 1。 - Ok - 符合輸入含 HealthState 值 Ok 的篩選條件。 值為 2。 - Warning - 符合輸入含 HealthState 值 Warning 的篩選條件。 值為 4。 - Error - 符合輸入含 HealthState 值 Error 的篩選條件。 值為 8。 - All - 符合輸入含任何 HealthState 值的篩選條件。 值為 65535。|
| --events-health-state-filter            | 可根據健康情況狀態來篩選所傳回的 HealthEvent 物件集合。 此參數的可能值包括下列其中一個健康情況狀態的整數值。 只會傳回符合篩選條件的事件。 所有事件都會用來評估彙總的健康情況狀態。 如果未指定，則會傳回所有項目。 狀態值是以旗標為基礎的列舉，因此值可以是使用位元 'OR' 運算子取得的這些值的組合。 例如，如果提供的值為 6，則會傳回 HealthState 值為 OK (2) 和 Warning (4) 的所有事件。 - Default - 預設值。 符合任何 HealthState。 值為零。 - None - 不符合任何 HealthState 值的篩選條件。 用來在指定狀態集合沒有任何結果時傳回。 值為 1。 - Ok - 符合輸入含 HealthState 值 Ok 的篩選條件。 值為 2。 - Warning - 符合輸入含 HealthState 值 Warning 的篩選條件。 值為 4。 - Error - 符合輸入含 HealthState 值 Error 的篩選條件。 值為 8。 - All - 符合輸入含任何 HealthState 值的篩選條件。 值為 65535。|
| --exclude-health-statistics | 表示健康情況統計資料是否傳回為查詢結果的一部分。 預設為 False。 統計資料顯示健康情況狀態為 Ok、Warning 和 Error 的子項目數目。|
| --services-health-state-filter          | 可根據健康情況狀態來篩選服務健康情況查詢結果中所傳回的服務健康情況狀態物件。 此參數的可能值包括下列其中一個健康情況狀態的整數值。 只會傳回符合篩選條件的服務。 所有服務都會用來評估彙總的健康情況狀態。 如果未指定，則會傳回所有項目。 狀態值是以旗標為基礎的列舉，因此值可以是使用位元 'OR' 運算子取得的這些值的組合。 例如，如果提供的值為 6，則會傳回 HealthState 值為 OK (2) 和 Warning (4) 的服務健康情況狀態。 - Default - 預設值。 符合任何 HealthState。 值為零。 - None - 不符合任何 HealthState 值的篩選條件。 用來在指定狀態集合沒有任何結果時傳回。 值為 1。 - Ok - 符合輸入含 HealthState 值 Ok 的篩選條件。 值為 2。 - Warning - 符合輸入含 HealthState 值 Warning 的篩選條件。 值為 4。 - Error - 符合輸入含 HealthState 值 Error 的篩選條件。 值為 8。 - All - 符合輸入含任何 HealthState 值的篩選條件。 值為 65535。|
| --timeout -t                            | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                                 | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                               | 顯示此說明訊息並結束。|
| --output -o                             | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query                                 | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                               | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-info"></a>sfctl application info
取得 Service Fabric 應用程式的相關資訊。

傳回 Service Fabric 叢集中已建立或正在建立且名稱符合指定為參數之名稱的應用程式相關資訊。 回應包括名稱、類型、狀態、參數，以及應用程式的其他詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-id      [必要]| 應用程式的身分識別。 這通常是沒有 'fabric:' URI 配置之應用程式的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果應用程式名稱是 "fabric://myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp~app1"，而在舊版中會是 "myapp/app1"。|
| --exclude-application-parameters| 指定是否從結果排除應用程式參數的旗標。|
| --timeout -t                 | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                      | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                    | 顯示此說明訊息並結束。|
| --output -o                  | 輸出格式。  允許的值：json、jsonc、table、tsv。             預設值：json。|
| --query                      | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                    | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-list"></a>sfctl application list
取得 Service Fabric 叢集中所建立且符合指定為參數之篩選的應用程式清單。

取得 Service Fabric 叢集中已建立或正在建立且符合指定為參數之篩選的應用程式相關資訊。 回應包括名稱、類型、狀態、參數，以及應用程式的其他詳細資料。 如果應用程式無法放入一頁，則會傳回一頁結果，以及可用來取得下一頁的接續權杖。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
|--application-definition-kind-filter| 用來篩選 ApplicationDefinitionKind 上的應用程式查詢作業。 - Default - 預設值。 符合具有任何 ApplicationDefinitionKind 值之輸入的篩選條件。 值為 0。 - All - 符合具有任何 ApplicationDefinitionKind 值之輸入的篩選條件。 值為 65535。 - ServiceFabricApplicationDescription - 符合具有 ApplicationDefinitionKind 值 ServiceFabricApplicationDescription 之輸入的篩選條件。 值為 1。 - Compose - 符合具有 ApplicationDefinitionKind 值 Compose 之輸入的篩選條件。 值為 2。 預設值：65535。|
| --application-type-name      | 用來篩選要查詢之應用程式的應用程式類型名稱。 此值不得包含應用程式類型版本。|
| --continuation-token         | 接續權杖參數可用來取得下一組結果。 具有非空白值的接續權杖會在來自系統的結果無法放入單一回應中時，隨附在 API 的回應中。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。|
| --exclude-application-parameters| 指定是否從結果排除應用程式參數的旗標。|
| --timeout -t                 | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                      | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                    | 顯示此說明訊息並結束。|
| --output -o                  | 輸出格式。  允許的值：json、jsonc、table、tsv。             預設值：json。|
| --query                      | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                    | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-load"></a>sfctl application load
取得 Service Fabric 應用程式的載入資訊。

傳回 Service Fabric 叢集中已建立或正在建立且名稱符合指定為參數之名稱的應用程式相關負載資訊。 回應包括名稱、最小節點數、最大節點數、應用程式目前佔用的節點數，以及應用程式的應用程式負載計量資訊。

### <a name="arguments"></a>引數
|引數|說明|
| --- | --- |
|--application-id [必要]| 應用程式的身分識別。 這通常是沒有 'fabric:' URI 配置之應用程式的完整名稱。 從 6.0 版開始，階層的名稱會以 "~" 字元分隔。 例如，如果應用程式名稱是 "fabric://myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp~app1"，而在舊版中會是 "myapp/app1"。 |
| --timeout -t               | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數
|引數|說明|
| --- | --- |
|--debug                    | 增加記錄詳細程度以顯示所有偵錯記錄。|
    --help -h                  | 顯示此說明訊息並結束。|
    --output -o                | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
    --query                    | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
    --verbose                  | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
取得可描述應用程式類型的資訊清單。
        
取得可描述應用程式類型的資訊清單。 回應會將應用程式資訊清單 XML 包含為字串。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-type-name    [必要]| 應用程式類型的名稱。|
| --application-type-version [必要]| 應用程式類型的版本。|
| --timeout -t                      | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                           | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                         | 顯示此說明訊息並結束。|
| --output -o                       | 輸出格式。  允許的值：json、jsonc、table、tsv。                  預設值：json。|
| --query                           | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                         | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-provision"></a>sfctl application provision
向叢集佈建或註冊 Service Fabric 應用程式類型。
        
向叢集佈建或註冊 Service Fabric 應用程式類型。 需要有這個項目，才能具現化任何新的應用程式。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-type-build-path [必要]| 應用程式套件的相對映像存放區路徑。|
| --timeout -t                         | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                              | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                            | 顯示此說明訊息並結束。|
| --output -o                          | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query                              | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                            | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-type"></a>sfctl application type

取得 Service Fabric 叢集中完全符合指定名稱的應用程式類型清單。

傳回 Service Fabric 叢集中已佈建或正在佈建之應用程式類型的相關資訊。 這些結果會是名稱完全符合指定為參數之名稱且符合所指定查詢參數的應用程式類型。 會傳回符合應用程式類型名稱之應用程式類型的所有版本，而且每一個版本都會傳回為一個應用程式類型。 回應包括名稱、版本、狀態，以及應用程式類型的其他詳細資料。 這是分頁式查詢，表示如果並非所有應用程式類型都可以放入一頁，則會傳回一頁結果，以及可用來取得下一頁的接續權杖。 例如，如果有 10 個應用程式類型，但一個頁面只能放入前 3 個應用程式類型，或最大結果設為 3，則會傳回 3 個。 若要存取其餘的結果，請在下一個查詢中使用所傳回的接續權杖來取出後續頁面。 如果沒有後續頁面，則會傳回空的接續權杖。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-type-name [必要]| 應用程式類型的名稱。|
| --continuation-token           | 接續權杖參數可用來取得下一組結果。 具有非空白值的接續權杖會在來自系統的結果無法放入單一回應中時，隨附在 API 的回應中。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。|
| --exclude-application-parameters  | 指定是否從結果排除應用程式參數的旗標。|
| --max-results                  | 分頁式查詢時傳回的最大結果數目。 此參數定義傳回結果數目的上限。 根據設定中所定義的訊息大小限制上限，如果所傳回的結果無法放入訊息中，則它們可以小於指定的結果上限。 如果此參數為零或未指定，分頁式查詢會在傳回訊息中盡可能包含越多結果。|
| --timeout -t                   | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                        | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                      | 顯示此說明訊息並結束。|
| --output -o                    | 輸出格式。  允許的值：json、jsonc、table、tsv。               預設值：json。|
| --query                        | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                      | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
從叢集移除或取消註冊 Service Fabric 應用程式類型。

從叢集移除或取消註冊 Service Fabric 應用程式類型。 只有在已刪除應用程式類型的所有應用程式執行個體時，才能執行這項作業。 取消註冊應用程式類型之後，就無法為此特定應用程式類型建立新的應用程式執行個體。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-type-name    [必要]| 應用程式類型的名稱。|
| --application-type-version [必要]| 應用程式類型版本。|
| --timeout -t                      | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                           | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                         | 顯示此說明訊息並結束。|
| --output -o                       | 輸出格式。  允許的值：json、jsonc、table、tsv。                  預設值：json。|
| --query                           | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                         | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
開始在 Service Fabric 叢集中升級應用程式。

驗證提供的應用程式升級參數，並在參數有效時開始升級應用程式。 請注意，升級描述會取代現有應用程式描述。 這表示，如果未指定參數，則會將應用程式上的現有參數覆寫為空的參數清單。 這會導致應用程式使用應用程式資訊清單中參數的預設值。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-id             [必要]| 應用程式的身分識別。 這通常是沒有 'fabric:' URI 配置之應用程式的完整名稱。 從 6.0 版開始，階層的名稱會以 '~' 字元分隔。 例如，如果應用程式名稱是 'fabric://myapp/app1'，則應用程式識別在 6.0+ 中會是 'myapp~app1'，而在舊版中會是 'myapp/app1'。|
| --app-version        [必要]| 目標應用程式版本。|
| --parameters         [必要]| 要在升級應用程式時套用之應用程式參數覆寫的 JSON 編碼清單。|
| --default-service-health-policy| 預設用來評估服務類型健康狀況之健康狀況原則的 JSON 編碼規格。|
| --failure-action            | 受監視的升級遇到監視原則或健康情況原則違規時執行的動作。|
| --force-restart             | 即使在程式碼版本尚未變更時，還是在升級期間強制重新啟動處理序。|
| --health-check-retry-timeout| 執行失敗動作之前，應用程式或叢集狀況不良時重試健康情況評估的時間量。 測量單位：秒。  預設值：PT0H10M0S。|
| --health-check-stable-duration | 升級繼續進入下一個升級網域之前，應用程式或叢集必須維持狀況良好的時間量。            測量單位：秒。  預設值：PT0H2M0S。|
| --health-check-wait-duration| 套用健康情況原則之前，完成升級網域之後等待的時間量。 測量單位：秒。            預設值︰0。|
| --max-unhealthy-apps        | 最大允許的狀況不良已部署應用程式百分比。 以介於 0 到 100 之間的數字來表示。|
| --mode                      | 在輪流升級期間用來監視健康情況的模式。            預設值：UnmonitoredAuto。|
| --replica-set-check-timeout | 發生非預期問題時，封鎖處理升級網域並防止遺失可用性的時間量上限。 測量單位：秒。|
| --service-health-policy     | 每個服務類型名稱之服務類型健康狀況原則的 JSON 編碼對應。 對應預設是空的。|
| --timeout -t                | 伺服器逾時 (秒)。  預設值：60。|
| --upgrade-domain-timeout    | 執行 FailureAction 之前，每個升級網域必須完成的時間量。 測量單位：秒。  預設值：P10675199DT02H48M05.4775807S。|
| --upgrade-timeout           | 執行 FailureAction 之前，整體升級必須完成的時間量。 測量單位：秒。  預設值：P10675199DT02H48M05.4775807S。|
| --warning-as-error          | 將具有相同嚴重性的健康情況評估警告視為錯誤。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                     | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                   | 顯示此說明訊息並結束。|
| --output -o                 | 輸出格式。  允許的值：json、jsonc、table、tsv。            預設值：json。|
| --query                     | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                   | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-application-upload"></a>sfctl application upload
將 Service Fabric 應用程式套件複製到映像存放區。

選擇性地顯示套件中每個檔案的上傳進度。 上傳進度會傳送至 `stderr`。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --path [必要]| 本機應用程式套件的路徑。|
|--imagestore-string| 將應用程式套件上傳至其中的目的地映像存放區。  預設值：fabric:ImageStore。|
| --show-progress  | 顯示大型套件的檔案上傳進度。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug       | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h     | 顯示此說明訊息並結束。|
| --output -o   | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query       | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose     | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。