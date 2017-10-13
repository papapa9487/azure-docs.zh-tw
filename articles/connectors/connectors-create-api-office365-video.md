---
title: "在您的邏輯應用程式中新增 Office 365 影片連接器 | Microsoft Docs"
description: "開始在您的 Microsoft Azure App Service Logic Apps 中使用 Office 365 影片連接器"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: f0e3613d4a3fd5478787c0365eb7a0bcde886c81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-office365-video-connector"></a>開始使用 Office 365 影片連接器
連接至 Office 365 影片，以取得 Office 365 影片的相關資訊、影片清單等。 有了 Office 365 影片，您可以：

* 根據您從 Office 365 影片所取得的資料，來建置您的商務流程。 
* 檢查影片入口網站狀態、取得頻道影片清單等動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 例如，您可以使用 Bing 搜尋連接器來搜尋 Office 365 影片，然後使用 Office 365 影片連接器取得該影片的相關資訊。 如果影片符合您的需求，您可以將該影片張貼在 Facebook 上。 

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-office365-video-connector"></a>建立至 Office365 影片連接器的連線
當您將這個連接器新增到邏輯應用程式時，您必須登入您的 Office 365 影片帳戶，並允許邏輯應用程式連線到您的帳戶。

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

當您建立連線之後，請輸入 Office 365 影片的屬性，例租用戶名稱或頻道識別碼。 


## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/office365videoconnector/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。