---
title: "了解 Azure IoT 中樞作業 | Microsoft Docs"
description: "開發人員指南 - 排定在連接至 IoT 中樞的多個裝置上執行作業。 作業可以在多個裝置上更新標籤和所需的屬性，並叫用直接方法。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: juanpere
ms.openlocfilehash: f90ecb70ad12ed05d5d40f8b26a0a4e461c9f835
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2017
---
# <a name="schedule-jobs-on-multiple-devices"></a>排程多個裝置上的作業

「Azure IoT 中樞」啟用了一些建置組塊，例如[裝置對應項屬性和標籤][lnk-twin-devguide]及[直接方法][lnk-dev-methods]。  一般而言，後端應用程式可讓裝置管理員和操作員在排定的時間大量更新 IoT 裝置並與之互動。  作業可在排定時間，針對一組裝置執行裝置對應項更新和直接方法。  例如，操作員會使用後端應用程式來起始和追蹤作業，在不干擾大樓運作的時段，將 43 號大樓 3 樓的一組裝置重新開機。

當您需要排定和追蹤一組裝置的下列任何活動進度時，請考慮使用作業︰

* 更新所需屬性
* 更新標籤
* 叫用直接方法

## <a name="job-lifecycle"></a>作業生命週期
作業由解決方案後端起始，並由 IoT 中樞維護。  您可以透過面向服務的 URI (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) 起始作業，並透過面向服務的 URI (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`) 查詢執行中作業的進度。 在作業起始後，若要重新整理執行中作業的狀態，請執行作業查詢。

> [!NOTE]
> 當您起始作業時，屬性名稱和值只能包含 US-ASCII 可列印英數字元，下列集合中的任何字元除外︰`$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`。

## <a name="jobs-to-execute-direct-methods"></a>用以執行直接方法的作業
以下程式碼片段顯示 HTTPS 1.1 要求詳細資料，適用於使用作業在一組裝置上執行[直接方法][lnk-dev-methods]的情況︰

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

查詢條件也可以針對單一裝置識別碼或針對裝置識別碼清單，如以下範例所示：

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[IoT 中樞查詢語言][lnk-query]涵蓋了IoT 中樞查詢語言的其他詳細資料。

## <a name="jobs-to-update-device-twin-properties"></a>用以更新裝置對應項屬性的作業
以下程式碼片段顯示 HTTPS 1.1 要求詳細資料，適用於使用作業來更新裝置對應項屬性的情況：

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>查詢作業的進度
以下程式碼片段顯示 HTTPS 1.1 要求詳細資料，適用於[查詢作業][lnk-query]的情況：

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

ContinuationToken 會從回應來提供。  

## <a name="jobs-properties"></a>作業屬性
以下清單顯示屬性和對應的描述，可在查詢作業或作業結果時使用。

| 屬性 | 說明 |
| --- | --- |
| **jobId** |應用程式所提供的作業識別碼。 |
| **startTime** |應用程式所提供的作業開始時間 (ISO-8601)。 |
| **endTime** |IoT 中樞所提供的作業完成日期 (ISO-8601)。 在作業到達「已完成」狀態後才有效。 |
| **type** |作業類型： |
| | **scheduledUpdateTwin**︰用來更新一組所需屬性或標籤的作業。 |
| | **scheduledDeviceMethod**︰用來在一組裝置對應項上叫用裝置方法的作業。 |
| **狀態** |作業的目前狀態。 狀態的可能值︰ |
| | **pending**︰已排定並等候作業服務執行。 |
| | **scheduled**︰已排定未來時間。 |
| | **running**︰目前作用中的作業。 |
| | **canceled**：作業已被取消。 |
| | **failed**：作業失敗。 |
| | **completed**作業完成。 |
| **deviceJobStatistics** |作業執行的相關統計資料。 |
| | **deviceJobStatistics** 屬性： |
| | **deviceJobStatistics.deviceCount**：作業中的裝置數目。 |
| | **deviceJobStatistics.failedCount**：作業失敗的裝置數目。 |
| | **deviceJobStatistics.succeededCount**：作業成功的裝置數目。 |
| | **deviceJobStatistics.runningCount**目前正在執行作業的裝置數目。 |
| | **deviceJobStatistics.pendingCount**：等待執行作業的裝置數目。 |

### <a name="additional-reference-material"></a>其他參考資料
IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點][lnk-endpoints]說明每個 IoT 中樞公開給執行階段和管理作業的各種端點。
* [節流和配額][lnk-quotas]說明適用於 IoT 中樞服務的配額，和使用服務時所預期的節流行為。
* [Azure IoT 裝置和服務 SDK][lnk-sdks] 列出各種語言 SDK，可供您在開發與「IoT 中樞」互動的裝置和服務應用程式時使用。
* [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言][lnk-query]說明可用於從 IoT 中樞擷取有關裝置對應項和作業資訊的 IoT 中樞查詢語言。
* [IoT 中樞 MQTT 支援][lnk-devguide-mqtt]針對 MQTT 通訊協定提供 IoT 中樞支援的詳細資訊。

## <a name="next-steps"></a>後續步驟
如果您想要嘗試本文章所述的概念，您可能會對下列 IoT 中樞教學課程感興趣：

* [排程及廣播作業][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
