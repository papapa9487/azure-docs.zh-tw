---
title: "Azure 搜尋服務的 API 版本 | Microsoft Docs"
description: "Azure 搜尋服務 REST API 與 .NET SDK 中用戶端程式庫的版本原則。"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 0458053a-164e-4682-a802-00097ecde981
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
ms.openlocfilehash: 4b4cb2d168676d52a90942a116905e6669b16639
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="api-versions-in-azure-search"></a>Azure 搜尋服務中的 API 版本
「Azure 搜尋服務」會定期推出功能更新。 有時候 (但並不一定)，這些更新會需要我們發佈新版本的 API，以保留回溯相容性。 發行新版本可讓您控制在程式碼中整合搜尋服務更新的時機與方式。

因此，我們嘗試只在必要時發佈新版本，因為這牽涉到需要花費一些心力升級您的程式碼以使用新的 API 版本。 只有在我們需要以破壞回溯相容性的方式變更 API 的某些層面時，才會發佈新版本。 在修正現有的功能，或新功能會變更現有 API 的介面區時，我們就會這樣做。

對於 SDK 更新，我們遵循相同的規則。 Azure 搜尋服務 SDK 會遵循 [semantic versioning (語意化版本控制系統)](http://semver.org/) 規則，這代表其版本具有三個部分：主要、次要和組建編號 (例如 1.1.0)。 只有在變更會破壞回溯相容性時，我們才會發行新的主要版本 SDK。 如果是非重大功能更新，我們將會遞增次要版本，而針對錯誤修正，我們只會遞增組建版本。

> [!NOTE]
> 您的「Azure 搜尋服務」執行個體支援數個 REST API 版本，包括最新版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

## <a name="snapshot-of-current-versions"></a>目前版本的快照
以下是 Azure 搜尋服務所有程式設計介面目前版本的快照。

| 介面 | 最新主要版本 | 狀態 |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |3.0 |正式推出，2016 年 11 月發行 |
| [.NET SDK 預覽版](https://aka.ms/search-sdk-preview) |4.0.1-preview |預覽版，2017 年 5 月發行 |
| [服務 REST API](https://docs.microsoft.com/rest/api/searchservice/) |2016-09-01 |正式推出 |
| [服務 REST API (預覽)](search-api-2016-09-01-preview.md) |2016-09-01-Preview |預覽 |
| [.NET 管理 SDK](https://aka.ms/search-mgmt-sdk) |2015-08-19 |正式推出 |
| [管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |正式推出 |

針對 REST API，必須在每個呼叫中納入 `api-version` 。 這會使將特定版本 (例如預覽 API) 做為目標較為容易。 下列範例說明如何指定 `api-version` 參數：

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2016-09-01

> [!NOTE]
> 雖然每個要求都具有 `api-version`，我們仍建議您針對所有的 API 要求使用相同的版本。 特別是當新的 API 版本引入舊版本無法辨識的屬性或作業時更建議您這樣做。 混合的 API 版本可能會有非預期的結果，應該予以避免。
>
> 服務 REST API 和管理 REST API 的版本是彼此獨立的。 版本號碼如有類似，純屬巧合。

正式推出 (或 GA) 的 API 可以用於實際執行，並且受到 Azure 服務等級協定所約束。 預覽版本具有實驗性功能，那些功能並不會都移轉到 GA 版本上。 **我們強烈建議您避免在實際執行的應用程式中使用預覽 API。**

## <a name="about-preview-and-generally-available-versions"></a>關於預覽與正式推出版本
Azure 搜尋服務一律會先透過 REST API 預先推出實驗性功能，然後再透過 .NET SDK 的發行前版本推出。

預覽功能不保證會移轉到 GA 版本。 相對於 GA 版本中的功能被視為穩定功能，且除了小型的回溯相容性修正與增強之外不太會變更，預覽功能是供用於測試和實驗，以期收集功能設計與實作的意見反應。

不過，因為預覽功能均有可能變更，所以我們建議避免撰寫依存於預覽版本的實際程式碼。 如果您正在使用舊版預覽版本，建議您移轉到正式推出的 (GA) 版本。

如需程式碼移轉的 .NET SDK：指南，請參閱 [升級 .NET SDK](search-dotnet-sdk-migration.md)。

正式推出意指 Azure 搜尋服務已受到服務等級協定 (SLA) 約束。 SLA 可以在 [Azure 搜尋服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)中找到。
