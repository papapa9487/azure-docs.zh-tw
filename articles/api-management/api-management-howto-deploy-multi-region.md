---
title: "將 Azure API 管理服務部署到多個 Azure 區域 | Microsoft Docs"
description: "瞭解如何將 Azure API 管理服務執行個體部署到多個 Azure 區域。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 47389ad6-f865-4706-833f-846115e22e4d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 1c39fee739c2f5fd4b928e1e76e1ea57f072b5f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>如何將 Azure API 管理服務執行個體部署到多個 Azure 區域
API 管理支援多區域部署，可讓 API 發行者跨所需的任意數量 Azure 區域發佈單一 API 管理服務。 這有助於降低地理上分散的 API 取用者感受到的要求延遲，並且可以改善某個區域離線時服務的可用性。 

最初建立 API 管理服務時，它只會包含一個 [單位][unit]，並且位在指派做為主要區域的單一 Azure 區域中。 您可以透過「Azure 入口網站」輕鬆新增其他區域。 「API 管理」閘道伺服器會部署到每個區域，而呼叫流量則會路由傳送到距離最近的閘道。 如果區域離線，流量會自動重新導向到下一個最近的閘道。 

> [!IMPORTANT]
> 多重區域部署僅供**[進階][Premium]**層使用。
> 
> 

## <a name="add-region"> </a>部署 API 管理服務執行個體到新區域
> [!NOTE]
> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][Get started with Azure API Management]教學課程中的[建立 API 管理服務執行個體][Create an API Management service instance]。
> 
> 

在「Azure 入口網站」中，瀏覽至「API 管理」服務執行個體的 [級別與價格] 頁面。 

![調整索引標籤][api-management-scale-service]

若要部署新區域，請從工具列中按一下 [+加入區域]。

![加入區域][api-management-add-region]

從下拉式清單中選取位置，然後使用滑桿設定單位數量。

![指定單位][api-management-select-location-units]

按一下 [加入] 以將您的選項放入 [位置] 表格中。 

重複此程序，直到設定好所有位置為止，然後從工具列中按一下 [儲存] 來開始部署程序。

## <a name="remove-region"> </a>從區域中刪除 API 管理服務執行個體
在「Azure 入口網站」中，瀏覽至「API 管理」服務執行個體的 [級別與價格] 頁面。 

![調整索引標籤][api-management-scale-service]

針對您想要移除的位置，使用表格最右邊的 [...] 按鈕來開啟操作功能表。 選取 [刪除] 選項。

![移除區域][api-management-remove-region]

確認刪除，然後按一下 [儲存] 來套用變更。

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Get started with Azure API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

