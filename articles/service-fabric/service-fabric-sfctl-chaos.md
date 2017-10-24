---
title: Azure Service Fabric CLI - sfctl choas | Microsoft Docs
description: "描述 Service Fabric CLI sfctl choas 命令。"
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
ms.openlocfilehash: 336e74d8f69cb04e6bd0e85fc68ba38b218fabae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-chaos"></a>sfctl chaos
啟動、停止及報告 chaos 測試服務。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
|    report| 根據傳入接續權杖或傳入的時間範圍取得 Chaos 報告的下個區段。|
|    start | 如果 Chaos 尚未在叢集中執行，請使用指定的 Chaos 參數開始執行 Chaos。|
|    stop  | 停止叢集中的 Chaos (如果正在執行)，否則不會執行任何動作。|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
根據傳入接續權杖或傳入的時間範圍取得 Chaos 報告的下個區段。

您可以指定 ContinuationToken 取得混亂報告的下個區段，或者您可以透過 StartTimeUtc 與 EndTimeUtc 指定時間範圍，但您無法在同一個呼叫中同時指定 ContinuationToken 與時間範圍。 當混亂事件超過 100 個時，系統會分區段傳回混亂報告，每個區段中包含的混亂事件不超過 100 個。 

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --continuation-token| 接續權杖參數可用來取得下一組結果。 具有非空白值的接續權杖會在來自系統的結果無法放入單一回應中時，隨附在 API 的回應中。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。|
| --end-time-utc   | 表示要產生 Chaos 報告之時間範圍的結束時間刻度數目。 如需刻度的詳細資料，請參閱 [DateTime.Ticks 屬性](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29)。|
| --start-time-utc | 表示要產生 Chaos 報告之時間範圍的開始時間刻度數目。 如需刻度的詳細資料，請參閱 [DateTime.Ticks 屬性](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29)。|
| --timeout -t     | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug          | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h        | 顯示此說明訊息並結束。|
| --output -o      | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query          | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose        | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
如果 Chaos 尚未在叢集中執行，請使用指定的 Chaos 參數開始執行 Chaos。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-type-health-policy-map  | 對於特定應用程式類型，有最大健康情況不良應用程式百分比的 JSON 編碼清單。 每個項目都會指定應用程式類型名稱做為一個索引鍵，並指定一個整數的值，表示用來評估指定應用程式類型之應用程式的 MaxPercentUnhealthyApplications 百分比。|
| --disable-move-replica-faults | 停用移動主要錯誤和移動次要錯誤。|
| --max-cluster-stabilization| 等待所有叢集實體變成穩定且健康情況良好的最大時間量。  預設值：60。|
| --max-concurrent-faults    | 每個反覆運算引發的最大並行錯誤數。           預設值：1。|
| --max-percent-unhealthy-apps  | 在 Chaos 期間評估叢集健康情況時，在報告錯誤之前，允許健康情況不良應用程式的最大百分比。|
| --max-percent-unhealthy-nodes | 在 Chaos 期間評估叢集健康情況時，在報告錯誤之前，允許健康情況不良節點的最大百分比。|
| --time-to-run              | 在自動停止之前，Chaos 將執行的總時間 (秒)。 最大允許的值為 4,294,967,295 (System.UInt32.MaxValue)。  預設值：4294967295。|
| --timeout -t               | 伺服器逾時 (秒)。  預設值：60。|
| --wait-time-between-faults | 單一反覆運算中連續錯誤之間的等候時間 (秒)。  預設值：20。|
| --wait-time-between-iterations| Chaos 的兩次連續反覆運算之間的時間區隔 (秒)。  預設值︰30。|
| --warning-as-error         | 在 Chaos 期間評估叢集健康情況時，將有相同嚴重性的警告視為錯誤。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                    | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                  | 顯示此說明訊息並結束。|
| --output -o                | 輸出格式。  允許的值：json、jsonc、table、tsv。           預設值：json。|
| --query                    | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                  | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
停止叢集中的 Chaos (如果正在執行)，否則不會執行任何動作。

阻止 Chaos 排程進一步的錯誤；但不會影響執行中的錯誤。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --timeout -t| 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug  | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h| 顯示此說明訊息並結束。|
| --output -o | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query  | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose| 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。