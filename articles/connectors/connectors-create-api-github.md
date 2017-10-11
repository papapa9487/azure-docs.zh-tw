---
title: "Azure Logic Apps 中的 GitHub 連接器 | Microsoft Docs"
description: "使用 Azure App Service 建立邏輯應用程式。 GitHub 是 Web 架構的 Git 儲存機制裝載服務。 它提供所有 Git 分散式修訂控制項和來源程式碼管理 (SCM) 功能，也加入自己的功能。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: d4614b0ceff0ec0d36dbb1a136551f985f2fc1a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-github-connector"></a>開始使用 GitHub 連接器
GitHub 是 Web 架構的 Git 儲存機制裝載服務。 它提供所有 Git 分散式修訂控制項和來源程式碼管理 (SCM) 功能，也加入自己的功能。

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-github"></a>建立 GitHub 的連線
若要使用 GitHub 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰ 

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 GitHub 認證 |

建立連線後，您就可以用它執行動作，並接聽本文所述的觸發程序。 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/github/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。