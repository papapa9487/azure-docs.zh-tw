---
title: "在您的 Logic Apps 中新增 Facebook 連接器 | Microsoft Docs"
description: "搭配 REST API 參數來使用 Facebook 連接器的概觀"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia; ladocs
ms.openlocfilehash: e10a30ccef3e81cb3d7749696453d82b8958d076
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-facebook-connector"></a>開始使用 Facebook 連接器
連線到 Facebook 並張貼在動態時報上、取得頁面摘要等等。 您可以利用 Facebook 來：

* 根據您從 Facebook 所取得的資料，來建置您的商務流程。 
* 在接收到新貼文時使用觸發程序。
* 使用會張貼到您的動態時報、取得頁面摘要等等的動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當您的動態時報上有新貼文時，您可以取得該貼文，然後把它推送到您的 Twitter 摘要。 

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-facebook"></a>建立至 Facebook 的連線
當您將這個連接器新增到邏輯應用程式時，您必須授權邏輯應用程式，使其能夠連線到您的 Facebook。

1. 登入您的 Facebook 帳戶。
2. 選取 [授權] ，然後允許您的邏輯應用程式連線並使用您的 Facebook。 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/facebook/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。