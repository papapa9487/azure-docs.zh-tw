---
title: "Logic Apps B2B 錯誤與解決方案清單：Azure App Service | Microsoft Docs"
description: "Logic Apps B2B 錯誤與解決方案清單"
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
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 1865d75f1b4c2aa18d5a3130f639572d19563b3e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>Logic Apps B2B 錯誤與解決方案清單  
本文協助您針對可能發生在 Logic Apps B2B 案例中的錯誤進行疑難排解，並提出修正這些錯誤的適當動作。


## <a name="agreement-resolution"></a>合約解析

### <a name="no-agreement-found"></a>*找不到合約 

|   |   |  
|---|---|
| 錯誤說明 | 找不到具有合約解析參數的合約|    
| 使用者動作 | 合約應新增至具議定的商務識別之整合帳戶。</br> 商務識別應與輸入訊息識別碼相符|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* 找不到具有識別身分的合約

|   |   | 
|---|---|
| 錯誤說明 | 找不到具有識別身分的合約：'AS2Identity'::'Partner1' 和 'AS2Identity'::'Partner3'| 
| 使用者動作 | 為合約設定的無效 AS2-From 或 AS2-To。 </br> 以合約設定更正 AS2 訊息 AS2-From 或 AS2-To 標題或合約，以符合 AS2 訊息標題中的 AS2 識別碼 |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* 遺漏 AS2 訊息標題  

|   |   |  
|---|---|
| 錯誤說明| 不正確的 AS2 標題。 'AS2-To' 或 'AS2-From' 其中一個標題為空白| 
| 使用者動作 | 收到的 AS2 訊息未包含 AS2-From 或 AS2-To 或兩個標題皆未包含。 </br> 檢查 AS2 訊息 AS2-From 和 AS2-To 標題，並根據合約設定進行更正 |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* 遺漏 AS2 訊息本文和標題    

|   |   |  
|---|---|
| 錯誤說明| 要求內容為 Null 或空白 | 
| 使用者動作 | 收到未包含訊息本文的 AS2 訊息 |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* AS2 訊息解密失敗

|   |   | 
|---|---|
| 錯誤說明 |  [已處理/錯誤：解密失敗] | 
| 使用者動作 | 傳送給夥伴前將 @base64ToBinary 新增至 AS2Message 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* MDN 解密失敗

|   |   | 
|---|---|
| 錯誤說明 |  [已處理/錯誤：解密失敗] | 
| 使用者動作 | 傳送給夥伴前將 @base64ToBinary 新增至 MDN 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* 遺漏簽署憑證

|   |   |  
|---|---|
| 錯誤說明| 尚未設定 AS2 合作對象的簽署憑證。 </br> AS2-From：partner1 AS2-To：partner2 | 
| 使用者動作 | 以正確的簽章憑證設定 AS2 合約設定 |
|  |  | 

## <a name="x12-and-edifact"></a>X12 和 EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* 發現前置或尾端空格    
    
|   |   | 
|---|---|
| 錯誤說明 | 剖析期間發生錯誤。 包含在識別碼 '987654' 的交換 (沒有群組) 中識別碼為 '123456' 的 Edifact 交易集 (其傳送者識別碼為 'Partner1'，接收者識別碼為 'Partner2') 因為下列錯誤而暫止：發現前置尾端分隔符號 |
| 使用者動作 | 要設定的合約設定，以允許前置和尾端空格。 </br> 編輯合約設定，以允許前置和尾端空格 |
|   |   |

![允許空格](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* 合約中已啟用重複檢查

|   |   | 
|---|---| 
| 錯誤說明 | 重複控制編號 |
| 使用者動作 | 此錯誤表示收到的訊息具有重複控制編號。 </br> 更正控制編號並重新傳送訊息 |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* 合約中遺漏結構描述

|   |   | 
|---|---| 
| 錯誤說明 | 剖析期間發生錯誤。 包含在識別碼 '000056422' 的交換中識別碼 '56422' 的功能群組內識別碼為 '564220001' 的 X12 交易集 (其傳送者識別碼為 '12345678       '，接收者識別碼為 '87654321       ') 因為下列錯誤而暫止「訊息具有不明的文件型別，且未解析合約中設定的任何現有結構描述」 |
| 使用者動作 | 在合約設定中設定結構描述  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* 合約中不正確的結構描述

|   |   | 
|---|---| 
| 錯誤說明 | 訊息具有不明的文件型別，且未解析合約中設定的任何現有結構描述。 |
| 使用者動作 | 在合約設定中設定正確的結構描述  |
|   |   |

## <a name="flat-file"></a>一般檔案

### <a name="-input-message-with-no-body"></a>* 輸入訊息沒有本文

|   |   | 
|---|---|
| 錯誤說明 | InvalidTemplate。 無法在行 '1' 與欄 '1902' 的動作 'Flat_File_Decoding' 輸入中處理範本語言運算式：'必要屬性「內容」需有值但收到 null。 路徑 ''.'。 |
| 使用者動作 | 此錯誤表示輸入訊息未包含本文 |
|   |   | 

## <a name="learn-more"></a>詳細資訊
[深入了解企業整合套件](logic-apps-enterprise-integration-overview.md)