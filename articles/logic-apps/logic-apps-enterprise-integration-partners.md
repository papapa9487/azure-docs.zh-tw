---
title: "建立企業對企業 (B2B) 訊息的夥伴 - Azure Logic Apps | Microsoft Docs"
description: "了解如何新增夥伴至您的整合帳戶，以搭配企業整合套件與 Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 950cb449b53f400f0f0f860caf5415bbb5212269
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>新增或更新工作流程中企業對企業協議中的夥伴

合作夥伴是參與企業對企業 (B2B) 交易及在彼此之間交換訊息的實體。 在您可以建立這些交易中代表您與其他組織的合作夥伴之前，你們雙方必須先共用可識別及驗證彼此所傳送訊息的資訊。 在您討論這些詳細資料並準備開始您的商業關係之後，您可以在您的整合帳戶中建立代表你們雙方的合作夥伴。

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>合作夥伴在您的整合帳戶中扮演什麼角色？

為定義在合作夥伴之間交換之訊息的相關詳細資料，您必須在那些合作夥伴之間建立合約。 然而，在您可以建立合約之前，至少需要在整合帳戶中新增兩個以上的合作夥伴。 您的組織必須是與**主機合作夥伴**相關之合約的一部分。 另一個合作夥伴 (或稱為**來賓合作夥伴**) 代表與您的組織交換訊息的組織。 來賓合作夥伴可以是另一家公司，或甚至是您自己組織中的部門。

在您新增這些合作夥伴之後，您可以建立合約。

接收和傳送設定是主控夥伴觀點導向的。 例如，合約中的接收設定會決定主控夥伴如何接收來賓夥伴所傳送的訊息。 同樣地，合約中的傳送設定會指出主控夥伴如何將訊息傳送給來賓夥伴。

## <a name="how-to-create-a-partner"></a>如何建立夥伴？

1. 在 Azure 入口網站中，選取 [瀏覽]。

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. 在篩選搜尋方塊中輸入**整合**，然後選取結果清單中的 [整合帳戶]。

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. 選取要將合作夥伴新增到其中的整合帳戶。

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. 選取 [夥伴]  圖格。

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. 在 [合作夥伴] 刀鋒視窗中，選擇 [新增]。

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. 輸入合作夥伴的名稱，然後選取 [辨識符號]。 最後，輸入 [值] 以協助識別傳送到您應用程式的文件。

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. 若要查看合作夥伴建立程序，請選取 [鈴鐺] 通知圖示。

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. 若要確認是否已順利新增您的新合作夥伴，請選取 [合作夥伴] 圖格。

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    選取 [合作夥伴] 圖格之後，您也會看見新加入的合作夥伴顯示於 [合作夥伴] 刀鋒視窗中。

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>如何編輯您整合帳戶中的現有合作夥伴

1. 選取 [合作夥伴] 圖格。
2. [合作夥伴] 刀鋒視窗開啟之後，請選取您要編輯的合作夥伴。
3. 在 [更新合作夥伴] 圖格上，進行您所需的變更。
4. 完成之後，請選擇 [儲存]；若要取消您的變更，請選取 [捨棄]。

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>如何刪除夥伴

1. 選取 [夥伴]  圖格。
2. [合作夥伴] 刀鋒視窗開啟之後，請選取您要刪除的合作夥伴。
3. 選擇 [刪除]。

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>後續步驟
* [深入了解合約](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企業整合合約")  

