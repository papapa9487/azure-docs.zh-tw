---
title: "Azure API 管理原則範例 - 產生共用存取簽章 | Microsoft Docs"
description: "Azure API 管理原則範例 - 示範如何使用運算式來產生共用存取簽章，並使用 rewrite-uri 原則來將要求轉送到 Azure 儲存體。"
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
ms.openlocfilehash: e1f17f9f4e17a3eebb55e4ec1905aec19a2165a5
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="generate-shared-access-signature"></a>產生共用存取簽章

本文會說明 Azure API 管理原則範例，示範如何使用運算式來產生[共用存取簽章](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1)，並使用 rewrite-uri 原則來將要求轉送到 Azure 儲存體。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入] 區塊。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)

