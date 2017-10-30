---
title: "如何在 Azure 入口網站中建立服務匯流排命名空間 | Microsoft Docs"
description: "使用 Azure 入口網站建立服務匯流排命名空間。"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2017
ms.author: sethm
ms.openlocfilehash: 38c79495683f0aa619247d486bfb8d843dfdfdc0
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2017
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>使用 Azure 入口網站建立服務匯流排命名空間

命名空間是所有傳訊元件的範圍容器。 多個佇列和主題可以位於單一命名空間，而且命名空間通常會做為應用程式容器。 有 2 種方式可建立服務匯流排命名空間：

1. Azure 入口網站 (本文)
2. [Resource Manager 範本][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>在 Azure 入口網站中建立命名空間

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

恭喜！ 您現已建立服務匯流排訊息命名空間。

## <a name="next-steps"></a>後續步驟

查看 [GitHub 範例][github-samples]，其中會示範一些更進階的服務匯流排傳訊功能。

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
