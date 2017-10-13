---
title: "升級至 Azure 搜尋服務 REST API 版本 2016-09-01 | Microsoft Docs"
description: "升級至 Azure 搜尋服務 REST API 版本 2016-09-01"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>升級至 Azure 搜尋服務 REST API 版本 2016-09-01
如果您在使用版本 2015-02-28 or 2015-02-28-Preview 的 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)，本文章將協助您升級應用程式，以使用下一個正式推出的 API 版本 2016-09-01。

REST API 的版本 2016-09-01 包含一些較舊版本的變更。 這些是大部分具有回溯相容性，因此變更程式碼應該只需要最少的工作 (視您之前使用的版本而定)。 請參閱[升級步驟](#UpgradeSteps)以取得如何變更您的程式碼以使用新的 API 版本的指示。

> [!NOTE]
> 您的「Azure 搜尋服務」執行個體支援數個 REST API 版本，包括最新版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>版本 2016-09-01 的新功能
版本 2016-09-01 是 Azure 搜尋服務 REST API 的第二個正式推出版本。 這個 API 版本的新功能包括︰

* [自訂分析器](https://aka.ms/customanalyzers)，可讓您充分控制將文字轉換成可檢索和可搜尋語彙基元的程序。
* [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)和 [Azure 表格儲存體](search-howto-indexing-azure-tables.md)索引子，可讓您根據排程或視需要將資料從 Azure 儲存體輕鬆地匯入至 Azure 搜尋服務。
* [欄位對應](search-indexer-field-mappings.md)，可讓您自訂索引子如何將資料匯入至 Azure 搜尋服務。
* Etag 可讓您透過安全並行方式來更新索引、索引子和資料來源的定義。 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟
如果您從版本 2015-02-28 升級，則除了變更版本號碼之外，可能不需要對您的程式碼進行任何變更。 您可能需要變更程式碼的唯一情況是︰

* 您的程式碼在 API 回應中傳回無法辨認的屬性時失敗。 您的應用程式預設應該會略過不了解的屬性。
* 您的程式碼仍然存在 API 要求，並嘗試將它們重新傳送給新的 API 版本。 例如，發生原因可能是您的應用程式持續保存搜尋服務 API 所傳回的接續 Token (如需詳細資訊，請在[搜尋服務 API 參考](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)) 中查找 `@search.nextPageParameters`。

如果下列其中一種情況適用您的情況，您可能需要據此變更程式碼。 否則，除非您想要開始使用版本 2016-09-01 的[新功能](#WhatsNew)，否則應該不需要進行任何變更。

如果您要從版本 2015-02-28-Preview 升級，則也會套用上述作業，但也必須知道版本 2016-09-01 中未提供某些預覽功能︰

* Azure Blob 儲存體索引子支援包含 JSON 陣列中的 CSV 檔案和 Blob。
* 同義字
* "More like this" 查詢

如果您的程式碼使用這些功能，則升級至 2016-09-01 時需要移除它們的使用。

> [!IMPORTANT]
> 請記住，預覽 API 是針對測試與評估，不應該用於生產環境。
> 
> 

## <a name="conclusion"></a>結論
如果您需要使用 Azure 搜尋服務 REST API 的更多詳細資料，請參閱 MSDN 上最近更新的 [API 參考](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

歡迎您提供 Azure 搜尋服務的意見反應。 如果您遇到問題，歡迎在 [Azure 搜尋服務 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) 或 [StackOverflow](http://stackoverflow.com/)上尋求協助。 如果您要在 StackOverflow 上詢問 Azure 搜尋服務問題，請一定要將它標上 `azure-search`。

感謝您使用 Azure 搜尋服務！

