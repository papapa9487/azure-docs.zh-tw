---
title: "驗證 XML - Azure Logic Apps | Microsoft Docs"
description: "使用企業整合套件，以 Azure Logic Apps 與 B2B 案例的結構描述來驗證 XML"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 8558efffa354cc4bb93820c837077ee997924c95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="validate-xml-for-enterprise-integration"></a>針對企業整合驗證 XML

通常在 B2B 案例中，協議中的合作夥伴必須先確認他們交換的訊息是有效的，才能開始處理資料。 您可以使用企業整合套件中的 XML 驗證連接器，根據預先定義的結構描述來驗證文件。

## <a name="validate-a-document-with-the-xml-validation-connector"></a>利用 XML 驗證連接器驗證文件

1. 建立邏輯應用程式，並[將應用程式連結到整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md "了解如何將整合帳戶連結到邏輯應用程式")，該應用程式包含用來驗證 XML 資料的結構描述。

2. 將 [要求 - 收到 HTTP 要求時]  觸發程序新增到您的邏輯應用程式。

    ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)

3. 若要新增 [XML 驗證] 動作，選擇 [新增動作]。

4. 若要篩選所有動作直到找到您想要的，請在搜尋方塊中輸入 *xml*。 選擇 [XML 驗證]。

    ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)

5. 若要指定您想要驗證的 XML 內容，請選取 [內容]。

    ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)

6. 選取內文標記做為要驗證的內容。

    ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)

7. 若要指定用來驗證前面 *content* 輸入的結構描述，請選擇 [結構描述名稱]。

    ![](./media/logic-apps-enterprise-integration-xml/xml-4.png)

8. 儲存您的工作   

    ![](./media/logic-apps-enterprise-integration-xml/xml-5.png)

現在您已完成設定驗證連接器。 在實際的應用程式中，您可能要將驗證的資料儲存在 SalesForce 之類的商業 (LOB) 應用程式。 若要將驗證的輸出傳送到 Salesforce，請新增一個動作。

若要測試您的驗證動作，請對 HTTP 端點提出要求。

## <a name="next-steps"></a>後續步驟
[深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md "了解企業整合套件")   

