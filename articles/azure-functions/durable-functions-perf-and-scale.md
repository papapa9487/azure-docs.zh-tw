---
title: "Durable Functions 中的效能和級別 - Azure"
description: "Azure Functions 的 Durable Functions 擴充簡介。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 10ce74097388a0283797e4692126c5039e8d4dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的效能和級別

若要最佳化效能和延展性，務必了解 [Durable Functions](durable-functions-overview.md) 獨特的調整規模特性。

若要了解調整規模行為，您必須了解 Durable Functions 所使用的基礎 Azure 儲存體提供者的一些詳細資料。

## <a name="history-table"></a>記錄資料表

記錄資料表是 Azure 儲存體資料表，含有所有協調流程執行個體的記錄事件。 隨著執行個體執行，此資料表中會新增資料列。 此資料表的資料分割索引鍵衍生自協調流程的執行個體識別碼。 在大部分情況下，這些都是隨機值，可確保 Azure 儲存體中的內部資料分割有最佳的分佈。

## <a name="internal-queue-triggers"></a>內部佇列觸發程序

協調器函式和活動函式都是由函式應用程式的預設儲存體帳戶中的內部佇列所觸發。 Durable Functions 中有兩種佇列：**控制佇列**和**工作項目佇列**。

### <a name="the-work-item-queue"></a>工作項目佇列

在 Durable Functions 中，每個工作中樞各有一個工作項目佇列。 這是基本佇列，運作方式類似於 Azure Functions 中的其他任何 `queueTrigger` 佇列。 此佇列用來觸發無狀態「活動函式」。 當 Durable Functions 應用程式向外延展至多個虛擬機器時，這些虛擬機器全部會從工作項目佇列中爭奪工作。

### <a name="control-queues"></a>控制佇列

「控制佇列」比簡單的工作項目佇列更複雜。 可用來觸發具狀態協調器函式。 因為協調器函式執行個體是具狀態單次個體，所以無法使用競爭取用者模型在虛擬機器之間分散負載。 相反地，協調器訊息會分散至多個控制佇列來平衡負載。 後續各節對此有更詳細的說明。

控制佇列包含各種不同的協調流程生命週期訊息類型。 例如，[協調器控制訊息](durable-functions-instance-management.md)、活動函式「回應」訊息，以及計時器訊息。

## <a name="orchestrator-scale-out"></a>協調器向外延展

活動函式是無狀態，可經由新增虛擬機器而自動向外延展。 相反地，協調器函式會「分割」至一或多個控制佇列。 控制佇列數目固定，開始建立載入之後就無法變更。

向外延展至多個函式主機執行個體時 (通常在不同的虛擬機器上)，每個執行個體取得其中一個控制佇列的鎖定。 此鎖定可確保協調流程執行個體一次只在單一虛擬機器上執行。 這表示，如果工作中樞已設定三個控制佇列，則協調流程執行個體最多可以跨三個虛擬機器來平衡負載。 您可以新增更多虛擬機器，以增加容量來執行活動函式。  但增加的資源不會用來執行協調器函式。

下圖說明在向外延展環境中，Azure Functions 主機與儲存體實體之間的互動方式。

![級別圖表](media/durable-functions-perf-and-scale/scale-diagram.png)

如您所見，所有虛擬機器會爭奪工作項目佇列上的訊息。 不過，只有三個虛擬機器可以從控制佇列中取得訊息，每個虛擬機器會鎖定單一控制佇列。

根據協調流程的執行個體識別碼來執行內部雜湊函式，協調流程執行個體會分散於控制佇列執行個體之間。 執行個體識別碼是自動產生，而且依預設是隨機產生，可確保執行個體均勻分散至所有可用的控制佇列。 目前支援的控制佇列資料分割預設數目是 **4** 個。

> [!NOTE]
> 目前無法在 Azure Functions 中設定資料分割數目。 [正在設法支援此設定選項](https://github.com/Azure/azure-functions-durable-extension/issues/73)。

一般而言，協調器函式是輕巧的設計，並不需要大量運算能力。 因此，不需要建立大量的控制佇列分割區來獲得很高的輸送量。 相反地，大部分繁重的工作都在無狀態活動函式中進行，這還可以無限制地向外延展。

## <a name="auto-scale"></a>自動調整規模

由於所有 Azure Functions 都在使用情況方案中執行，Durable Functions 支援透過 [Azure Functions 級別控制器](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling)來自動調整規模。 「級別控制器」會監視工作項目佇列的長度和每個控制佇列，然後據以新增或移除虛擬機器資源。 如果控制佇列長度隨著時間而增加，級別控制器會持續新增執行個體，直到達到控制佇列資料分割計數為止。 如果工作項目佇列長度隨著時間而增加，級別控制器會持續新增虛擬機器資源，直到符合負載為止，不會考慮控制佇列資料分割計數。

## <a name="thread-usage"></a>執行緒使用方式

協調器函式會在單一執行緒上執行。 為了確保協調器函式執行有確定性，這是必要的。 因此，絕對不要讓協調器函式執行緒不必要地忙於處理某些工作，例如 I/O (基於各種理由而禁止)、封鎖或微調作業。 任何可能需要 I/O、封鎖或多個執行緒的工作，都應該移入活動函式中。

活動函式和一般佇列觸發的函式有完全相同的行為。 這表示它們可以安全地執行 I/O、執行需要大量 CPU 的作業，以及使用多個執行緒。 因為活動觸發程序是無狀態，所以能夠自由地向外延展至不限數量的虛擬機器。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [安裝 Durable Functions 擴充和範例](durable-functions-install.md)
