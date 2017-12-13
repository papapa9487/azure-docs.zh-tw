---
title: "Azure API 管理原則範例 - 新增 Forwarded 標頭 | Microsoft Docs"
description: "Azure API 管理原則範例 - 示範如何在輸入要求中新增 Forwarded 標頭，以允許後端 API 建構適當的 URL。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: cc2df914532b6cda37c951b65b243e90b63d57cb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="add-a-forwarded-header"></a>新增 Forwarded 標頭

本文會說明 Azure API 管理原則範例，示範如何在輸入要求中新增 Forwarded 標頭，以允許後端 API 建構適當的 URL。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="code"></a>代碼

將程式碼貼至 [輸入] 區塊。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)
