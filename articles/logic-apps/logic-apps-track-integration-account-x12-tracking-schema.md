---
title: "適用於 B2B 監視的 X12 追蹤結構描述 - Azure Logic Apps | Microsoft Docs"
description: "使用 X12 追蹤結構描述以監視來自 Azure 整合帳戶中交易的 B2B 訊息。"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3782c0a76ea8728a146b3d73774f74c31187cbfd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>啟動或啟用追蹤 X12 訊息，以監視成功、錯誤和訊息屬性
您可以在 Azure 整合帳戶中使用這些 X12 追蹤結構描述，協助您監視企業對企業 (B2B) 交易︰

* X12 交易集追蹤結構描述
* X12 交易集通知追蹤結構描述
* X12 交換追蹤結構描述
* X12 交換通知追蹤結構描述
* X12 功能群組追蹤結構描述
* X12 功能群組通知追蹤結構描述

## <a name="x12-transaction-set-tracking-schema"></a>X12 交易集追蹤結構描述
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| 屬性 | 類型 | 說明 |
| --- | --- | --- |
| senderPartnerName | String | X12 訊息傳送者的夥伴名稱。 (選用) |
| receiverPartnerName | String | X12 訊息接收者的夥伴名稱。 (選用) |
| senderQualifier | String | 傳送合作夥伴辨識符號。 (必要) |
| senderIdentifier | String | 傳送合作夥伴識別碼。 (必要) |
| receiverQualifier | String | 接收合作夥伴辨識符號。 (必要) |
| receiverQualifier | String | 接收合作夥伴識別碼。 (必要) |
| agreementName | String | 據以解析訊息的 X12 合約名稱。 (選用) |
| direction | 例舉 | 訊息流程的方向，接收或傳送。 (必要) |
| interchangeControlNumber | String | 交換控制編號。 (選用) |
| functionalGroupControlNumber | String | 功能的控制編號。 (選用) |
| transactionSetControlNumber | String | 交易集控制編號。 (選用) |
| CorrelationMessageId | String | 相互關聯訊息識別碼。 {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} 組合。 (選用) |
| messageType | String | 交易集或文件類型。 (選用) |
| isMessageFailed | Boolean | X12 訊息是否失敗。 (必要) |
| isTechnicalAcknowledgmentExpected | Boolean | X12 合約中是否設定技術通知。 (必要) |
| isFunctionalAcknowledgmentExpected | Boolean | X12 合約中是否設定功能通知。 (必要) |
| needAk2LoopForValidMessages | Boolean | 有效的訊息是否需要 AK2 迴圈。 (必要) |
| segmentsCount | Integer | X12 交易集的區段數目。 (選用) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 交易集通知追蹤結構描述
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| 屬性 | 類型 | 說明 |
| --- | --- | --- |
| senderPartnerName | String | X12 訊息傳送者的夥伴名稱。 (選用) |
| receiverPartnerName | String | X12 訊息接收者的夥伴名稱。 (選用) |
| senderQualifier | String | 傳送合作夥伴辨識符號。 (必要) |
| senderIdentifier | String | 傳送合作夥伴識別碼。 (必要) |
| receiverQualifier | String | 接收合作夥伴辨識符號。 (必要) |
| receiverQualifier | String | 接收合作夥伴識別碼。 (必要) |
| agreementName | String | 據以解析訊息的 X12 合約名稱。 (選用) |
| direction | 例舉 | 訊息流程的方向，接收或傳送。 (必要) |
| interchangeControlNumber | String | 功能通知的交換控制編號。 僅針對傳送至夥伴的訊息而接收功能通知的傳送端，才填入值。 (選用) |
| functionalGroupControlNumber | String | 功能群組的交換控制編號。 僅針對傳送至夥伴的訊息而接收功能通知的傳送端，才填入值。 (選用) |
| isaSegment | String | 訊息的 ISA 區段。 僅針對傳送至夥伴的訊息而接收功能通知的傳送端，才填入值。 (選用) |
| isaSegment | String | 訊息的 GS 區段。 僅針對傳送至夥伴的訊息而接收功能通知的傳送端，才填入值。 (選用) |
| respondingfunctionalGroupControlNumber | String | 回應交換控制編號。 (選用) |
| respondingFunctionalGroupId | String | 回應功能群組識別碼，在通知中對應至 AK101。 (選用) |
| respondingtransactionSetControlNumber | String | 回應交易集控制編號。 (選用) |
| respondingTransactionSetId | String | 回應交易集識別碼，在通知中對應至 AK201。 (選用) |
| StatusCode | Boolean | 交易集通知狀態碼。 (必要) |
| segmentsCount | 例舉 | 通知狀態碼。 允許的值為 **Accepted**、**Rejected** 和 **AcceptedWithErrors**。 (必要) |
| processingStatus | 例舉 | 通知的處理狀態。 允許的值為 **Received**、**Generated** 和 **Sent**。 (必要) |
| CorrelationMessageId | String | 相互關聯訊息識別碼。 {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} 組合。 (選用) |
| isMessageFailed | Boolean | X12 訊息是否失敗。 (必要) |
| ak2Segment | String | 接收的功能群組內交易集的通知。 (選用) |
| ak3Segment | String | 報告資料區段中的錯誤。 (選用) |
| ak5Segment | String | 報告是否接受或拒絕 AK2 區段中識別的交易集，以及原因。 (選用) |

## <a name="x12-interchange-tracking-schema"></a>X12 交換追蹤結構描述
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| 屬性 | 類型 | 說明 |
| --- | --- | --- |
| senderPartnerName | String | X12 訊息傳送者的夥伴名稱。 (選用) |
| receiverPartnerName | String | X12 訊息接收者的夥伴名稱。 (選用) |
| senderQualifier | String | 傳送合作夥伴辨識符號。 (必要) |
| senderIdentifier | String | 傳送合作夥伴識別碼。 (必要) |
| receiverQualifier | String | 接收合作夥伴辨識符號。 (必要) |
| receiverQualifier | String | 接收合作夥伴識別碼。 (必要) |
| agreementName | String | 據以解析訊息的 X12 合約名稱。 (選用) |
| direction | 例舉 | 訊息流程的方向，接收或傳送。 (必要) |
| interchangeControlNumber | String | 交換控制編號。 (選用) |
| isaSegment | String | 訊息 ISA 區段。 (選用) |
| isTechnicalAcknowledgmentExpected | Boolean | X12 合約中是否設定技術通知。 (必要) |
| isMessageFailed | Boolean | X12 訊息是否失敗。 (必要) |
| isa09 | String | X12 文件交換日期。 (選用) |
| isa10 | String | X12 文件交換時間。 (選用) |
| isa11 | String | X12 交換控制標準識別碼。 (選用) |
| isa12 | String | X12 交換控制版本號碼。 (選用) |
| isa14 | String | 已要求 X12 通知。 (選用) |
| isa15 | String | 用於測試或生產的指標。 (選用) |
| isa16 | String | 元素分隔符號。 (選用) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 交換通知追蹤結構描述
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| 屬性 | 類型 | 說明 |
| --- | --- | --- |
| senderPartnerName | String | X12 訊息傳送者的夥伴名稱。 (選用) |
| receiverPartnerName | String | X12 訊息接收者的夥伴名稱。 (選用) |
| senderQualifier | String | 傳送合作夥伴辨識符號。 (必要) |
| senderIdentifier | String | 傳送合作夥伴識別碼。 (必要) |
| receiverQualifier | String | 接收合作夥伴辨識符號。 (必要) |
| receiverQualifier | String | 接收合作夥伴識別碼。 (必要) |
| agreementName | String | 據以解析訊息的 X12 合約名稱。 (選用) |
| direction | 例舉 | 訊息流程的方向，接收或傳送。 (必要) |
| interchangeControlNumber | String | 從合作夥伴收到的技術通知交換控制編號。 (選用) |
| isaSegment | String | 從合作夥伴收到的技術通知 ISA 區段。 (選用) |
| respondingInterchangeControlNumber |String | 從合作夥伴收到的技術通知交換控制編號。 (選用) |
| isMessageFailed | Boolean | X12 訊息是否失敗。 (必要) |
| StatusCode | 例舉 | 交換通知狀態碼。 允許的值為 **Accepted**、**Rejected** 和 **AcceptedWithErrors**。 (必要) |
| processingStatus | 例舉 | 通知狀態。 允許的值為 **Received**、**Generated** 和 **Sent**。 (必要) |
| ta102 | String | 交換日期。 (選用) |
| ta103 | String | 交換時間。 (選用) |
| ta105 | String | 交換說明碼。 (選用) |

## <a name="x12-functional-group-tracking-schema"></a>X12 功能群組追蹤結構描述
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| 屬性 | 類型 | 說明 |
| --- | --- | --- |
| senderPartnerName | String | X12 訊息傳送者的夥伴名稱。 (選用) |
| receiverPartnerName | String | X12 訊息接收者的夥伴名稱。 (選用) |
| senderQualifier | String | 傳送合作夥伴辨識符號。 (必要) |
| senderIdentifier | String | 傳送合作夥伴識別碼。 (必要) |
| receiverQualifier | String | 接收合作夥伴辨識符號。 (必要) |
| receiverQualifier | String | 接收合作夥伴識別碼。 (必要) |
| agreementName | String | 據以解析訊息的 X12 合約名稱。 (選用) |
| direction | 例舉 | 訊息流程的方向，接收或傳送。 (必要) |
| interchangeControlNumber | String | 交換控制編號。 (選用) |
| functionalGroupControlNumber | String | 功能的控制編號。 (選用) |
| isaSegment | String | 訊息 GS 區段。 (選用) |
| isTechnicalAcknowledgmentExpected | Boolean | X12 合約中是否設定技術通知。 (必要) |
| isFunctionalAcknowledgmentExpected | Boolean | X12 合約中是否設定功能通知。 (必要) |
| isMessageFailed | Boolean | X12 訊息是否失敗。 (必要)|
| gs01 | String | 功能識別碼。 (選用) |
| gs02 | String | 應用程式寄件者的代碼。 (選用) |
| gs03 | String | 應用程式接收者的代碼。 (選用) |
| gs04 | String | 功能群組的日期。 (選用) |
| gs05 | String | 功能群組的時間。 (選用) |
| gs07 | String | 負責單位的代碼。 (選用) |
| gs08 | String | 版本/版次/產業識別碼。 (選用) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 功能群組通知追蹤結構描述
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| 屬性 | 類型 | 說明 |
| --- | --- | --- |
| senderPartnerName | String | X12 訊息傳送者的夥伴名稱。 (選用) |
| receiverPartnerName | String | X12 訊息接收者的夥伴名稱。 (選用) |
| senderQualifier | String | 傳送合作夥伴辨識符號。 (必要) |
| senderIdentifier | String | 傳送合作夥伴識別碼。 (必要) |
| receiverQualifier | String | 接收合作夥伴辨識符號。 (必要) |
| receiverQualifier | String | 接收合作夥伴識別碼。 (必要) |
| agreementName | String | 據以解析訊息的 X12 合約名稱。 (選用) |
| direction | 例舉 | 訊息流程的方向，接收或傳送。 (必要) |
| interchangeControlNumber | String | 從合作夥伴收到技術通知時，傳送端會填入的交換控制編號。 (選用) |
| functionalGroupControlNumber | String | 從合作夥伴收到技術通知時，傳送端會填入的技術通知功能群組控制編號。 (選用) |
| isaSegment | String | 同交換控制編號，但只在特定情況下才會填入。 (選用) |
| isaSegment | String | 同功能群組控制編號，但只在特定情況下才會填入。 (選用) |
| respondingfunctionalGroupControlNumber | String | 原始功能群組的控制編號。 (選用) |
| respondingFunctionalGroupId | String | 在通知功能群組識別碼中對應至 AK101。 (選用) |
| isMessageFailed | Boolean | X12 訊息是否失敗。 (必要) |
| StatusCode | 例舉 | 通知狀態碼。 允許的值為 **Accepted**、**Rejected** 和 **AcceptedWithErrors**。 (必要) |
| processingStatus | 例舉 | 通知的處理狀態。 允許的值為 **Received**、**Generated** 和 **Sent**。 (必要) |
| ak903 | String | 已接收交易集數目。 (選用) |
| ak904 | String | 所識別的功能群組中接受的交易集數目。 (選用) |
| ak9Segment | String | 是否接受或拒絕 AK1 區段中識別的功能群組，以及原因。 (選用) |

## <a name="next-steps"></a>後續步驟
* [深入了解監視 B2B 訊息](logic-apps-monitor-b2b-message.md)。
* 深入了解 [AS2 追蹤結構描述](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)。
* [深入了解 B2B 自訂追蹤結構描述](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)。
* 了解[在 Operations Management Suite 入口網站中追蹤 B2B 訊息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。
* [深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)。  
