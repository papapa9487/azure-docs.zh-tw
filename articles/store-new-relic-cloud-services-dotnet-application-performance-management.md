---
title: "將 New Relic 與 Azure 搭配使用 | Microsoft Docs"
description: "了解如何使用 New Relic 服務來管理與監控 Azure 應用程式。"
services: 
documentationcenter: .net
author: nickfloyd
manager: timlt
editor: 
ms.assetid: b01011be-c344-4e33-987d-c93dac1971fb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2016
ms.author: nickfloyd@newrelic.com
ms.openlocfilehash: f4f13c909a6ff640d403f5264004176c087925dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="new-relic-application-performance-management-on-azure"></a>Azure 上的 New Relic 應用程式效能管理
您可以將 New Relic 的世界級效能監視新增至 Azure 託管的應用程式。 除了獲得對 Azure 應用程式進行監視、疑難排解及微調的全面功能外，您還有資格使用 Azure 來享有 New Relic 產品的折扣價格。

## <a name="what-is-new-relic"></a>什麼是 New Relic？
使用 [New Relic 產品](https://newrelic.com/products)，您就可以解決應用程式錯誤、早一步處理潛在問題，以及了解整個環境的效能。 主要是讓您在識別和診斷效能問題時可節省時間，並隨手取得解決這些問題所需的資訊。

New Relic 會追蹤來自伺服器和使用者瀏覽器的 Web 交易載入時間和輸送量。 它會顯示您在資料庫中花費多少時間、分析較慢的查詢和 Web 要求、提供運作時間監視和警示、追蹤應用程式例外狀況，還有許多其他功能。 

## <a name="special-pricing"></a>優惠價格
New Relic Standard 供 Azure 使用者免費使用。 New Relic Pro 是依據 Azure 雲端服務的執行個體大小提供。 如需定價資訊，請參閱 Azure Marketplace 中的 [New Relic 頁面](https://azure.microsoft.com/marketplace/partners/newrelic/newrelic/) \(英文\) 或連絡 New Relic (sales@newrelic.com) 以了解企業層級的價格。

Azure 客戶部署 New Relic 代理程式時享有 New Relic Pro 試用訂閱兩週。

## <a name="sign-up-for-new-relic-using-the-azure-store"></a>使用 Azure 市集註冊 New Relic
New Relic 與 Azure Web 角色和背景工作角色緊密整合。 您可以直接從 Azure 市集輕鬆快速地註冊 New Relic。 如需指示，請參閱 New Relic 提供的 [Azure 市集註冊指示](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-cloud-services#signup) 。

## <a name="view-your-data"></a>檢視資料
註冊好後，您可以利用 New Relic 令人讚嘆的應用程式監視和資料驅動分析。 若要在 New Relic 中檢查您的應用程式效能：

1. 從 Azure 入口網站選取 [管理]。
2. 以您的 New Relic 帳戶電子郵件和密碼登入。
3. 從應用程式索引選取您的應用程式以在 [APM 概觀頁面](https://docs.newrelic.com/docs/apm/applications-menu/monitoring/apm-overview-page)中檢視您所有應用程式的資料。

## <a name="using-new-relic-with-azure"></a>將 New Relic 與 Azure 搭配使用
如需使用 New Relic 與 Azure 的詳細資訊，請參閱 [New Relic 的文件網站](https://docs.newrelic.com/docs/agents/net-agent/azure-installation)，包括︰ 

* [適用於 .NET 的 New Relic](https://docs.newrelic.com/docs/agents/net-agent/getting-started/new-relic-net)
* [在 Azure 雲端服務上檢測 .NET 背景工作角色](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/instrument-net-worker-role-azure-cloud-service)
* [適用於 Microsoft Azure 雲端平台的 New Relic](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-cloud-services)
* [適用於 Microsoft Azure App Service 的 New Relic](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-portal)
* [New Relic/Azure 疑難排解](https://docs.newrelic.com/docs/agents/net-agent/azure-troubleshooting)

