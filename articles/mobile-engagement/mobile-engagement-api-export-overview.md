---
title: "Mobile Engagement 匯出 API 概觀"
description: "了解匯出使用者裝置所產生之原始資料，以運用在自己工具中的基本概念。"
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: 
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: 346e0e480ff84ee849f135a7605d27df9e32f966
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="mobile-engagement-export-api-overview"></a>Mobile Engagement 匯出 API 概觀
## <a name="introduction"></a>簡介
在本文件中，您將了解匯出使用者裝置所產生之原始資料，以運用在自己工具中的基本概念。

## <a name="pre-requisites"></a>必要條件
從 Mobile Engagement 匯出原始資料需要︰

* 設定 API 驗證，以便能夠使用 API (請參閱 [驗證手動設定](mobile-engagement-api-authentication-manual.md))、
* 使用 REST API 或 [.net SDK](mobile-engagement-dotnet-sdk-service-api.md)、
* Azure 儲存體帳戶。

> [!NOTE]
> 我們也建議您使用絕佳的 [Microsoft Azure 儲存體總管](http://storageexplorer.com/)，至少在開發階段使用，因為它提供簡單易用的 UI，可以與 Azure 儲存體互動。
> 
> 

## <a name="what-can-be-exported"></a>您可以匯出什麼？
Mobile Engagement 可讓其使用者收集許多類型的資料，因此有幾種類型的匯出適合這些不同的資料類型。
有 2 種基本類型的匯出︰

* 快照集︰通常用來匯出代表狀態且 Mobile Engagement 沒有其歷程記錄的資料。 比方說，這包括標籤 (應用程式資訊)、權杖或推播宣傳活動的意見反應。 因此，這些類型的匯出與日期無關。
* 歷程記錄︰此匯出類型用於會隨一段時間累積的資料，例如事件或活動。

下表完整地描述所有可能的匯出︰

| 匯出類型 | 資料類型 | 說明 |
| --- | --- | --- |
| 快照 |推播 |針對每個裝置識別碼/使用者識別碼，產生推播宣傳活動意見反應的匯出 |
| 快照 |標記 |產生與每個裝置相關聯的標籤 (應用程式資訊) 的匯出 |
| 快照 |裝置 |產生大部分裝置相關資料的匯出，例如技術資料 (機型、地區設定、時區...)、標籤、第一次看到的時間... |
| 快照 |權杖 |產生所有有效權杖的匯出 |
| 歷程記錄 |活動 |產生在指定的時間內，每個裝置的所有活動的匯出 |
| 歷程記錄 |Event |產生在指定的時間內，每個裝置的所有活動的匯出 |
| 歷程記錄 |Job |產生在指定的時間內，每個裝置之所有工作的匯出 |
| 歷程記錄 |錯誤 |產生在指定的時間內，每個裝置的所有錯誤的匯出 |

## <a name="how-does-it-work"></a>運作方式
匯出是長時間執行的工作，可能會產生大型資料檔案。 因此，無法叫用它們來立即傳回要下載的檔案。
若要從 Mobile Engagement 匯出資料，您必須透過 API 建立 **匯出工作** ，在 API 中您一般會指定：

* 匯出的類型 (快照或歷程記錄)、
* 資料類型、
* 匯出結果寫入位置所在的 **Azure 儲存體容器** (包括具有寫入存取權的有效 SAS)。
* 例如︰範例容器 URL 參數會是 https://[StorageAccountName].blob.core.windows.net/[ContainerName]?[SASWritePermissionsToken]  

以下是真實範例。 https://testazmeexport.blob.core.windows.net/test1234azme?sv=2015-12-11&ss=b&srt=sco&sp=rwdlac&se=2016-12-17T04:59:26Z&st=2016-12-16T20:59:26Z&spr=https&sig=KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q%3D

請注意，可能需要幾分鐘，您的工作才會啟動，然後若是小型應用程式，它可能會執行幾秒鐘，若是有許多使用者或活動的應用程式則可能執行數小時。

一旦建立工作之後，就可以檢查其狀態，查看它是否仍在執行，或是否已完成。

工作成功之後，產生的資料檔案會在提供的儲存體容器上。

