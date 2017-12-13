---
title: "Azure API 管理原則範例 - 使用 OAuth2 在閘道與後端之間進行授權 | Microsoft Docs"
description: "Azure API 管理原則範例 - 示範如何在閘道與後端之間使用 OAuth2 進行授權。 此範例示範如何從 AAD 取得存取權杖並將它轉送到後端。"
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
ms.openlocfilehash: e0aeec66f23033f916c782c8a895e725b0735b62
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>使用 OAuth2 在閘道與後端之間進行授權

本文會說明 Azure API 管理原則範例，示範如何在閘道與後端之間使用 OAuth2 進行授權。 此範例示範如何從 AAD 取得存取權杖並將它轉送到後端。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入] 區塊。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)

