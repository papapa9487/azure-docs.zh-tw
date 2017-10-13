---
title: "在 Azure Logic Apps 中新增 Twilio 連接器 | Microsoft Docs"
description: "搭配 REST API 參數來使用 Twilio 連接器的概觀"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia; ladocs
ms.openlocfilehash: a790ac51b0fea7e3fa379d20e0e094e7ce0d7696
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-twilio-connector"></a>開始使用 Twilio 連接器
連線到 Twilio 來傳送及接收全域 SMS、多媒體及 IP 訊息。 您可以利用 Twilio 來：

* 根據您從 Twilio 所取得的資料，來建置您的商務流程。 
* 使用會取得訊息、列出訊息等等的動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當您收到新的 Twilio 訊息時，您可以取得此訊息，並在服務匯流排工作流程中使用。 

從建立邏輯應用程式開始，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-twilio"></a>建立至 Twilio 的連線
當您將這個連接器新增到邏輯應用程式時，請輸入下列的 Twilio 值：

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 帳戶識別碼 |是 |輸入您的 Twilio 帳戶識別碼 |
| 存取權杖 |是 |輸入您的 Twilio 存取權杖 |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

如果您沒有 Twilio 存取權杖，請參閱[使用者身分識別和存取權杖](https://www.twilio.com/docs/api/chat/guides/identity)。

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/twilio/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。