---
title: "在 Logic Apps 中使用 SharePoint Server 連接器 | Microsoft Docs"
description: "開始在 Logic Apps 中使用 SharePoint Server 連接器"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0f3274816e279a1aa57febaa2f8294914900799a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-sharepoint-connector"></a>開始使用 SharePoint 連接器
SharePoint 連接器提供一種方式，讓您能夠使用 SharePoint 上的清單。

從建立邏輯應用程式開始，請參閱[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-sharepoint"></a>建立至 SharePoint 的連線
如要使用 SharePoint 連接器，您必須先建立 **連線** ，然後提供下列屬性的詳細資料： 

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 SharePoint 的認證 |

若要連接到 **SharePoint** ，請向 SharePoint 提供您的身分識別 (使用者名稱和密碼、智慧卡認證等等)。 通過驗證之後，您就可以在邏輯應用程式中使用 SharePoint 連接器。 

在邏輯應用程式的設計工具中，請依照下列步驟來登入 SharePoint，以便建立 **連接** 供您的邏輯應用程式使用：

1. 在搜尋方塊中輸入 SharePoint，等候搜尋傳回名稱中有 SharePoint 的所有項目：   
   ![設定 SharePoint][1]  
2. 選取 [SharePoint - 當檔案建立時]   
3. 選取 [登入 SharePoint]：   
   ![設定 SharePoint][2]    
4. 提供您的 SharePoint 認證來登入向 SharePoint 進行驗證    
   ![設定 SharePoint][3]     
5. 驗證完成後，您會被重新導向至邏輯應用程式，設定 SharePoint 的 [當檔案建立時]  對話方塊後即可完成。          
   ![設定 SharePoint][4]  
6. 接著，您可以新增所需的其他觸發和動作來完成邏輯應用程式。   
7. 選取上方功能表列的 [儲存]  來儲存您的工作。  

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/sharepoint/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
