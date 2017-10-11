---
title: "排程器限制及預設值"
description: "排程器限制及預設值"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="scheduler-limits-and-defaults"></a>排程器限制及預設值
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>排程器配額、限制、預設值和節流
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>x-ms-request-id 標頭
每一個對排程器服務提出的要求都會傳回名為**x-ms-request-id**的回應標頭。 此標頭包含專門識別要求的不透明值。

如果要求一直失敗，而且您已確認要求表達正確，則可以使用此值將錯誤回報給 Microsoft。 在您的報告中，請包括 x-ms-request-id 的值、提出要求的大約時間、訂用帳戶的識別碼、工作集合及/或工作，以及要求所嘗試的作業類型。

## <a name="see-also"></a>另請參閱
 [排程器是什麼？](scheduler-intro.md)

 [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [在 Azure 入口網站中開始使用排程器](scheduler-get-started-portal.md)

 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)

 [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)

