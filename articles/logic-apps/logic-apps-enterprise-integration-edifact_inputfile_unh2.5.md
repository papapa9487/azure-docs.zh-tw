---
title: "Logic Apps B2B EDIFACT 解碼解析 UNH2.5 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps B2B EDIFACT 解碼解析 UNH2.5"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 62ad8183cc6e9f56255b2729a04ee7710d00a21a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>如何處理具有 UNH2.5 區段的 EDIFACT 文件
EDIFACT 文件中有 UNH2.5 時，它會用於結構描述查詢。 

範例︰UNH 欄位在 EDIFACT 訊息中是 **EAN008**  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

要處理訊息所需遵循的步驟 
1. 更新結構描述
2. 檢查合約設定  

## <a name="update-the-schema"></a>更新結構描述
若要處理訊息，您需要部署具有 UNH2.5 根節點名稱的結構描述。  假設範例，結構描述根名稱就是 **EFACT_D03B_ORDERS_EAN008**  

針對每個具有不同 UNH2.5 區段的 D03B_ORDERS，您就必須部署個別的結構描述。  

## <a name="add-schema-to-the-edifact-agreement"></a>將結構描述新增至 EDIFACT 合約
### <a name="edifact-decode"></a>EDIFACT 解碼
若要將內送郵件解碼，請設定 EDIFACT 合約接收設定中的結構描述
1. 將結構描述新增至整合帳戶    
2. 設定 EDIFACT 合約接收設定中的結構描述。 
3. 選取 EDIFACT 合約，然後按一下 [編輯為 JSON]。  在接收合約中新增 UNH2.5 值 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT 編碼
若要將內送郵件編碼，請設定 EDIFACT 合約傳送設定中的結構描述
1. 將結構描述新增至整合帳戶    
2. 設定 EDIFACT 合約傳送設定中的結構描述。 
3. 選取 EDIFACT 合約，然後按一下 [編輯為 JSON]。  在傳送合約中新增 UNH2.5 值 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>後續步驟
* [深入了解整合帳戶合約](../logic-apps/logic-apps-enterprise-integration-agreements.md "深入了解企業整合合約")  