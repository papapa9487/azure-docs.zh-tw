---
title: "在 Azure Logic Apps 中使用 Slack 連接器 | Microsoft Docs"
description: "連接至 Logic Apps 中的 Slack"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: fc5fc128efe01bd0727e3ff30d8938918e89ac3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-slack-connector"></a>開始使用 Slack 連接器
Slack 是團隊通訊工具，能把您團隊的通訊都集中在一個地方，讓您不但在何處都可使用，還能搜尋各項記錄。 

立即開始建立邏輯應用程式，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-slack"></a>建立至 Slack 的連線
如要使用 Slack 連接器，您必須先建立 **連線** ，然後提供下列屬性的詳細資料： 

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 Slack 的認證 |

請遵循下列步驟登入 Slack，並在邏輯應用程式中完成 Slack **連線** 設定：

1. 選取 [週期] 
2. 選取 [頻率] 並輸入 [間隔]
3. 選取 [新增動作]  
   ![設定 Slack][1]  
4. 在搜尋方塊中輸入 Slack，並等候搜尋傳回所有名稱中有 Slack 的項目
5. 選取 [Slack - 張貼訊息] 
6. 選取 [登入 Slack]：  
   ![設定 Slack][2]
7. 提供您的 Slack 認證來登入並授權應用程式    
   ![設定 Slack][3]  
8. 您將會重新導向至組織的登入頁面。 **授權** Slack 與邏輯應用程式互動：      
   ![設定 Slack][5] 
9. 驗證完成後，您會被重新導向至邏輯應用程式，設定 [Slack - 取得所有訊息]  區段後，即可完成動作。 加入其他所需的觸發和動作。  
   ![設定 Slack][6]
10. 選取上方功能表列的 [儲存]  來儲存您的工作。

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/slack/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
