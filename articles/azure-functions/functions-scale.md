---
title: "Azure Functions 主控方案比較 | Microsoft Docs"
description: "了解如何在 Azure Functions 取用方案與 App Service 方案之間做出選擇。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函式, 取用方案, app service 方案, 事件處理, webhooks, 動態計算, 無伺服器架構"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/12/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09bb662e30a97e2741303e2e4630582625954909
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="azure-functions-hosting-plans-comparison"></a>Azure Functions 主控方案比較

Azure Functions 的執行模式有兩種︰取用方案和 Azure App Service 方案。 取用方案會在您的程式碼執行時自動配置計算能力、視需要相應放大來處理負載，然後在程式碼未執行時相應減少。 因此，您不必支付閒置 VM 的費用，或必須預先保留容量。 本文的重點是取用方案 ([無伺服器](https://azure.microsoft.com/overview/serverless-computing/)應用程式模型)。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

>[!NOTE]  
> Linux 裝載目前僅適用於 App Service 方案。

如果您還不熟悉 Azure Functions，請參閱 [Azure Functions 概觀](functions-overview.md)。

當您建立函數應用程式時，您必須針對應用程式中包含的函式設定主控方案。 不管用哪一種，都是以 Azure Functions 主機的執行個體執行函式。 方案的類型控制：

* 主控件執行個體如何向外延展。
* 每個主控件可用的資源。

目前，您必須在函式應用程式建立期間選擇主控方案類型。 之後便無法更改。 

在 App Service 方案中，您可以在各層之間調整，以配置不同的資源量。 在取用方案中，Azure Functions 會自動處理所有的資源配置。

## <a name="consumption-plan"></a>取用方案

當使用取用方案時，會根據傳入事件的數目，動態新增和移除 Azure Functions 主機。 這個方案會自動調整，您只需支付您的函式執行時使用的計算資源。 在取用方案中，函式最多可執行 10 分鐘。 

> [!NOTE]
> 在取用方案中，函式的預設逾時為 5 分鐘。 變更 [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) 中的 `functionTimeout` 屬性，您可以將函數應用程式的該值增加為 10 分鐘。

帳單是根據執行數目、執行時間以及使用的記憶體。 帳單會跨函式應用程式內的所有函式進行彙總。 如需詳細資訊，請參閱 [Azure Functions 價格頁面]。

取用方案是預設主控方案，具有下列優點︰
- 只有當您的函式執行時才需付費。
- 自動調整規模，即使在高負載期間也會。

## <a name="app-service-plan"></a>App Service 方案

在 App Service 方案中，您的函式應用程式是依據基本、標準、進階和隔離 SKU 在專用的 VM 上執行，就像 Web 應用程式、API Apps 和行動應用程式一樣。 會配置專用的 VM 給您的 App Service 應用程式，這表示函式主機執行不間斷。 App Service 方案支援 Linux。

在下列情況中請考慮使用 App Service 方案︰
- 您有現有的、使用量過低的 VM 已在執行其他 App Service 執行個體。
- 您期望您的函數應用程式連續執行或接近連續執行。 在此情況下，App Service 方案可以更符合成本效益。
- 您需要的 CPU 或記憶體選項比取用方案所提供的更多。
- 您的執行時間超過取用方案允許的執行時間上限 (10 分鐘)。
- 您需要 App Service 方案才有提供的功能，例如 App Service 環境、VNET/VPN 連線和較大 VM 大小的支援。 
- 您想要在 Linux 上執行函數應用程式，或想要在其上提供自訂映像以執行函數。

VM 可以減少執行數目、執行時間以及使用記憶體的成本。 如此一來，您不會支付超過您配置的 VM 執行個體的成本。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

使用 App Service 方案時，您可以透過手動新增更多 VM 執行個體來相應放大，或者您可以啟用自動規模調整。 如需詳細資訊，請參閱[手動或自動調整執行個體計數規模](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)。 您也可以透過選擇不同的 App Service 方案來相應增加。 如需詳細資訊，請參閱[在 Azure 中為應用程式進行相應增加](../app-service/web-sites-scale.md)。 

若計畫在 App Service 方案上執行 JavaScript 函式，您應該選擇 vCPU 數目較少的方案。 如需詳細資訊，請參閱[選擇單一核心 Azure Service 方案](functions-reference-node.md#considerations-for-javascript-functions)。  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
###永遠開啟

如果您執行 App Service 方案，應該啟用 [永遠開啟] 設定，以讓您的函數應用程式能夠正確執行。 在 App Service 方案中，函式的執行階段會在無作用幾分鐘後進入閒置狀態，因此只有 HTTP 觸發程序會「喚醒」您的函式。 這類似於 WebJobs 必須啟用「永遠開啟」的方式。 

只有 App Service 方案具備「永遠開啟」選項。 在取用方案中，平台會自動啟動函數應用程式。

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

不論是取用方案或 App Service 方案，函式應用程式都需要有支援 Azure Blob、佇列、檔案、表格儲存體的一般 Azure 儲存體帳戶。 Azure Functions 會在內部使用「Azure 儲存體」來進行作業，例如管理觸發程序和記錄函數執行。 有些儲存體帳戶並不支援佇列和表格，例如僅限 Blob 的儲存體帳戶 (包括進階儲存體) 和搭配區域備援儲存體複寫的一般用途儲存體帳戶。 建立函數應用程式時，[儲存體帳戶] 刀鋒視窗中會過濾掉這些帳戶。

若要深入了解儲存體帳戶類型，請參閱 [Azure 儲存體服務簡介](../storage/common/storage-introduction.md#introducing-the-azure-storage-services)。

## <a name="how-the-consumption-plan-works"></a>取用方案的運作方式

在取用方案中，調整控制器會根據事件函式被觸發的事件數目，新增額外的 Functions 主機執行個體，藉此自動調整 CPU 和記憶體資源。 Functions 主機的每個執行個體僅限於 1.5 GB 記憶體。

使用取用主控方案時，函式程式碼檔案會儲存在函式之主要儲存體帳戶的 Azure 檔案共用上。 當您刪除函式應用程式的主要儲存體帳戶時，函式程式碼檔案會被刪除，且無法復原。

> [!NOTE]
> 在執行取用方案中使用 blob 觸發程序時，如果函數應用程式已進入閒置狀態，則處理新 blob 時最多會有 10 分鐘的延遲。 在函數應用程式開始執行之後，會立即處理 blob。 為了避免發生此初始延遲，請考慮下列做法︰
> - 在 App Service 方案上主控函式應用程式，並啟用「永遠開啟」。
> - 使用其他機制來觸發 blob 處理，像是包含 blob 名稱的佇列訊息。 如需範例，請參閱 [Blob 輸入和輸出繫結的 C# 指令碼和 JavaScript 範例](functions-bindings-storage-blob.md#input--output---example)。

### <a name="runtime-scaling"></a>執行階段調整

Azure Functions 使用名為「縮放控制器」的元件來監視事件的速率，並判斷是否相應放大或相應縮小。 縮放控制器會在每種觸發程序類型使用啟發學習法。 例如，當使用 Azure 佇列儲存體觸發程序時，會根據佇列長度和最舊佇列訊息的壽命調整規模。

調整的單位是函數應用程式。 當函式應用程式相應放大時，會配置額外資源來執行 Azure Functions 主機的多個執行個體。 反之，當計算需求降低時，縮放控制器會移除 Functions 主機的執行個體。 執行個體的數目最終會在函數應用程式中沒有任何函式執行時相應減少至零個。

![縮放控制器能監視事件及建立執行個體](./media/functions-scale/central-listener.png)

### <a name="billing-model"></a>計費模式

[Azure Functions 價格頁面]會詳細說明取用方案的計費方式。 使用量是在函式應用程式層級彙總，且只會計算函式程式碼執行的時間。 計費單位如下︰ 
* **以十億位元組-秒 (GB-s) 為單位的資源取用量**。 會計算為在函數應用程式中執行之所有函式的記憶體大小和執行時間組合。 
* **執行**。 每次函式為回應事件觸發程序而執行，就算一次。

[Azure Functions 價格頁面]: https://azure.microsoft.com/pricing/details/functions
