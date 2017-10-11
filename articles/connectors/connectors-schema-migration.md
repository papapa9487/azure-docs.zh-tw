---
title: "如何將邏輯應用程式移轉至結構描述版本 2015-08-01-preview | Microsoft Docs"
description: "您可以輕鬆地將邏輯應用程式移轉至最新的結構描述版本。 請直接遵循下列步驟。"
services: logic-apps
documentationcenter: 
author: MSFTMAN
manager: erikre
editor: 
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
ms.openlocfilehash: a5a73a9f124e5339b61dbc49021444a208a471f0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>如何將邏輯應用程式移轉至結構描述版本 2015-08-01-preview
若要將現有的邏輯應用程式移至新的結構描述，請執行下列作業︰  

1. 在 Azure 入口網站中開啟邏輯應用程式  
2. 按一下 [更新結構描述]︰
   
   ![API 圖示][step1]   
   [更新結構描述] 頁面便會出現，並提供會提供新結構描述增強功能詳細資訊之文件的連結︰![API 圖示][step2]

> [!NOTE]
> 當您選取 [更新結構描述] 時，我們會自動執行移轉步驟，並為您提供程式碼輸出。 您可以使用此輸出更新您的定義，不過，請確定您有遵循良好的程式碼撰寫方式，例如下面的**最佳作法**一節中所說的方式。
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>將邏輯應用程式移轉至最新結構描述版本時的最佳作法︰
* 將已移轉的指令碼複製到新的邏輯應用程式 - 在完成測試並確認移轉的應用程式已如預期般運作後再覆寫舊的邏輯應用程式。
* 放入生產環境 **之前** 先測試邏輯應用程式
* 移轉完成後，開始更新邏輯應用程式以盡可能使用 [Managed API](apis-list.md)。 例如，您可以在使用 DropBox v1 的地方開始使用 Dropbox v2。

## <a name="whats-next"></a>後續步驟
* [了解如何手動移轉邏輯應用程式](../logic-apps/logic-apps-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






