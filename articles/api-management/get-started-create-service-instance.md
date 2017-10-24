---
title: "建立 Azure API 管理執行個體 | Microsoft Docs"
description: "請依照本教學課程的步驟來建立新的 Azure API 管理執行個體。"
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>建立新的 Azure API 管理服務執行個體

本教學課程描述使用 [Azure 入口網站](https://portal.azure.com/)建立新的 API 管理執行個體的步驟。

## <a name="prerequisites"></a>必要條件

+ 有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>建立新的服務

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [新增] > [企業整合] > [API 管理]。

    或者，選擇 [新增]，在搜尋方塊中輸入 `API management`，然後按 Enter 鍵。 按一下 [建立] 。

2. 在 [API 管理服務] 視窗中，為 API 管理服務輸入唯一的**名稱**。 稍後無法變更此名稱。

    > [!TIP]
    > 服務名稱用來產生預設網域名稱，格式為 *{name}.azure-api.net*。 如果您想要使用自訂網域名稱，請參閱[設定自訂網域](configure-custom-domain.md)。 <br/>
    > 服務名稱用來參考服務和對應的 Azure 資源。

5. 從您可存取的不同 Azure 訂用帳戶之中，選取一個**訂用帳戶**。
6. 在 [資源群組] 中，選取新的或現有資源。  資源群組是共用生命週期、權限及原則的資源集合。 [在此](../azure-resource-manager/resource-group-overview.md#resource-groups)深入了解。
7. 在 [位置] 中，選取 API 管理服務建立所在的地理區域。 只有可用的 API 管理服務區域才會出現在下拉式清單方塊中。 
9. 輸入 [組織名稱]。 許多地方會用到此名稱。 例如，開發人員入口網站的標題及通知電子郵件的寄件者。
10. 在 [管理員電子郵件] 中，設定電子郵件地址以接收所有來自 **API 管理** 的通知。
11. 在 [定價層] 中，設定 [開發人員] 層來評估服務。 此階層不適用於生產環境。 如需有關調整 API 管理層規模的詳細資訊，請參閱[升級和調整規模](upgrade-and-scale.md)。
12. 選擇 [建立] 。

    > [!TIP]
    > 建立 API 管理服務通常需要 20 到 30 分鐘。 選取 [釘選到儀表板]以更輕鬆找到新建立的服務。

## <a name="next-steps"></a>後續步驟

[使用 Azure API 管理來發佈 API](#api-management-getstarted-publish-api.md)