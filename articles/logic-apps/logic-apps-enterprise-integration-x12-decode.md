---
title: "將 X12 訊息解碼 - Azure Logic Apps | Microsoft Docs"
description: "在 Azure Logic Apps 的企業整合套件中使用 X12 訊息解碼器，針對交易集進行驗證 EDI 並產生通知"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 18719a8f49c74973947517161f7306c233a9323f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>使用企業整合套件將 Azure Logic Apps 的 X12 訊息解碼

使用 Decode X12 訊息連接器，您可以對照交易夥伴協議來驗證信封、驗證 EDI 和夥伴特定的屬性、將交換分割為交易集或保留整個交換，並產生已處理交易的通知。 若要使用此連接器，您必須將連接器新增至邏輯應用程式中的現有觸發程序。

## <a name="before-you-start"></a>開始之前

以下是您所需的項目︰

* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* 已經定義並與 Azure 訂用帳戶相關聯的[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)。 您必須有整合帳戶才能使用解碼 X12 訊息連接器。
* 至少已經在整合帳戶中定義兩個[夥伴](logic-apps-enterprise-integration-partners.md)
* 已經在整合帳戶中定義的 [X12 合約](logic-apps-enterprise-integration-x12.md)

## <a name="decode-x12-messages"></a>解碼 X12 訊息

1. [建立邏輯應用程式](logic-apps-create-a-logic-app.md)。

2. 解碼 X12 訊息連接器沒有觸發程序，因此您必須新增觸發程序 (例如要求觸發程序) 來啟動邏輯應用程式。 在 Logic Apps 設計工具中，新增觸發程序，然後將動作新增至您的邏輯應用程式。

3.  在搜尋方塊中，輸入 "X12" 做為篩選條件。 選取 [X12 - 將 X12 訊息]。
   
    ![搜尋 "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. 如果您先前未建立與整合帳戶的任何連線，系統將會提示您立即建立該連線。 替連線命名，然後選取您要連線的整合帳戶。 

    ![提供整合帳戶連線詳細資料](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    具有星號的屬性為必要項目。

    | 屬性 | 詳細資料 |
    | --- | --- |
    | 連線名稱 * |為連接器輸入任何名稱。 |
    | 整合帳戶 * |輸入整合帳戶的名稱。 確定您的整合帳戶和邏輯應用程式位於相同的 Azure 位置。 |

5.  當您完成時，連線詳細資料看起來類似此範例。 若要完成連線建立，請選擇 [建立]。
   
    ![整合帳戶連線詳細資料](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. 建立您的連線之後 (如此範例所示)，請選取要解碼的 X12 一般檔案訊息。

    ![整合帳戶連線已建立](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    例如：

    ![選取 X12 一般檔案訊息進行解碼](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-details"></a>X12 解碼詳細資料

X12 解碼連接器會執行下列工作︰

* 針對交易夥伴合約驗證信封
* 驗證 EDI 和夥伴特定屬性
  * EDI 結構驗證，以及擴充的結構描述驗證
  * 驗證交換信封的結構。
  * 針對控制結構描述進行信封的結構描述驗證。
  * 針對訊息結構描述進行交易集資料元素的結構描述驗證。
  * 對交易集資料元素執行 EDI 驗證 
* 驗證交換、群組和交易集控制編號並未重複
  * 針對先前已接收的交換檢查交換控制編號。
  * 針對交換中的其他群組控制編號檢查群組控制編號。
  * 針對該群組中其他交易集控制編號檢查交易集控制編號。
* 將交換分割為交易集，或保留整個交換︰
  * 將交換分割為交易集 - 暫止發生錯誤的交易集︰將交換分割為交易集，並剖析每個交易集。 
  X12 Decode 動作只會輸出未通過 `badMessages` 驗證的交易集，並將剩餘的交易輸出到 `goodMessages`。
  * 將交換分割為交易集 - 暫止發生錯誤的交換︰將交換分割為交易集，並剖析每個交易集。 
  如果交換中有一或多個交易集無法通過驗證，X12 Decode 動作會將該交換中的所有交易集輸出到 `badMessages`。
  * 保留交換 - 暫止發生錯誤的交易集︰保留交換並處理整個批次交換。 
  X12 Decode 動作只會輸出未通過 `badMessages` 驗證的交易集，並將剩餘的交易輸出到 `goodMessages`。
  * 保留交換 - 暫止發生錯誤的交換︰保留交換並處理整個批次交換。 
  如果交換中有一或多個交易集無法通過驗證，X12 Decode 動作會將該交換中的所有交易集輸出到 `badMessages`。 
* 產生技術和/或功能確認 (若已設定)。
  * 標頭驗證後會產生技術確認。 技術確認會報告位址接收者處理交換標頭和結尾的狀態。
  * 內文驗證後會產生功能確認。 功能確認會報告處理接收的文件時遇到的每個錯誤

## <a name="view-the-swagger"></a>檢視 Swagger
請參閱 [Swagger 詳細資料](/connectors/x12/)。 

## <a name="next-steps"></a>後續步驟
[深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md "了解企業整合套件") 

