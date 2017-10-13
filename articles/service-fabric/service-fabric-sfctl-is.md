---
title: Azure Service Fabric CLI - sfctl is | Microsoft Docs
description: "描述 Service Fabric CLI sfctl is 命令。"
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
ms.openlocfilehash: 11c7a10c6448248ffacee2b519f48b6ca7e4e188
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-is"></a>sfctl is
查詢命令，並將其傳送至基礎結構服務。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
|    命令| 叫用指定基礎結構服務執行個體上的系統管理命令。|
|    query  | 叫用指定基礎結構服務執行個體上的唯讀查詢。|


## <a name="sfctl-is-command"></a>sfctl is command
叫用指定基礎結構服務執行個體上的系統管理命令。

對於設定一或多個基礎結構服務執行個體的叢集，此 API 提供將基礎結構特有的命令傳送至基礎結構服務特定執行個體的方法。 可用的命令和其對應的回應格式會根據叢集執行所在的基礎結構而有所不同。 這個 API 支援 Service Fabric 平台；這不表示直接從您的程式碼使用。 .

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --command [必要]| 要叫用的命令文字。 命令的內容與基礎結構有關。  預設值：is command。|
| --service-id     | 基礎結構服務的識別。 這是沒有 'fabric:' URI 配置之基礎結構服務的完整名稱。 當叢集中有一個以上的基礎結構服務執行個體執行時，才需要此參數。|
| --timeout -t     | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug          | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h        | 顯示此說明訊息並結束。|
| --output -o      | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query          | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose        | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。|

## <a name="sfctl-is-query"></a>sfctl is query
叫用指定基礎結構服務執行個體上的唯讀查詢。

對於設定一或多個基礎結構服務執行個體的叢集，此 API 提供將基礎結構特有的查詢傳送至基礎結構服務特定執行個體的方法。 可用的命令和其對應的回應格式會根據叢集執行所在的基礎結構而有所不同。 這個 API 支援 Service Fabric 平台；這不表示直接從您的程式碼使用。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --command [必要]| 要叫用的命令文字。 命令的內容與基礎結構有關。  預設值：is query。|
| --service-id     | 基礎結構服務的識別。 這是沒有 'fabric:' URI 配置之基礎結構服務的完整名稱。 當叢集中有一個以上的基礎結構服務執行個體執行時，才需要此參數。|
| --timeout -t     | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug          | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h        | 顯示此說明訊息並結束。|
| --output -o      | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query          | JMESPath 查詢字串。 如需詳細資訊，請參閱 http://jmespath.org/。|
| --verbose        | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。