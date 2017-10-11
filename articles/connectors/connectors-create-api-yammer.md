---
title: "在 Azure Logic Apps 中新增 Yammer 連接器 | Microsoft Docs"
description: "搭配 REST API 參數來使用 Yammer 連接器的概觀"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: c7a213343b4fb2b5a89a5052a459061b404a431c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-yammer-connector"></a>開始使用 Yammer 連接器
連線到 Yammer 來存取您企業網路中的交談。 您可以利用 Yammer 來：

* 根據您從 Yammer 所取得的資料，來建置您的商務流程。 
* 在群組或您追蹤的摘要中有新訊息時使用觸發程序。
* 使用動作來張貼訊息、取得所有訊息等等。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當新訊息出現時，您可以利用 Office 365 來傳送電子郵件。

立即開始建立邏輯應用程式，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-yammer"></a>建立至 Yammer 的連線
若要使用 Yammer 連接器，您必須先建立**連接**，然後提供下列屬性的詳細資料： 

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 Yammer 的認證 |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/yammer/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。