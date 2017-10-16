---
title: "在 Marketplace 中使用 Azure 受管理的應用程式 | Microsoft Docs"
description: "描述如何建立可透過 Marketplace 取得的 Azure 受管理的應用程式。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="consume-azure-managed-applications-in-the-marketplace"></a>使用 Marketplace 中 Azure 受管理的應用程式

如[受管理的應用程式概觀](managed-application-overview.md)一文中所討論，端對端體驗中有兩種案例。 其中一個是發行者或廠商，是要建立受管理的應用程式供客戶使用。 第二個是受管理應用程式的客戶或取用者。 本文涵蓋第二個案例。 它會描述您可以如何從 Microsoft Azure Marketplace 部署受管理的應用程式。

## <a name="create-from-the-marketplace"></a>從 Marketplace 建立

從 Marketplace 部署受管理的應用程式類似於從 Marketplace 部署任何類型的資源。 

在入口網站中，選取 [+ 新增]，並搜尋要部署的解決方案類型。 從可用的選項，選取您需要的選項。

![搜尋解決方案](./media/managed-application-consume-marketplace/search-apps.png)

檢閱應用程式的摘要，並選取 [建立]。

![建立受管理的應用程式](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

如同任何其他解決方案，您會看到要提供值的欄位。 這些欄位會因您建立的受管理應用程式類型而變。 

## <a name="view-support-information"></a>檢視支援資訊

部署受管理的應用程式之後，檢視應用程式的支援資訊。 在 [受管理的應用程式] 刀鋒視窗中，會列出支援資訊。

![支援](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>檢視發行者權限

管理您應用程式的廠商會被授與資源的存取權。 若要查看這些權限，請選取 [授權]。

![授權](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>後續步驟

* 如需在 Marketplace 中發行受管理應用程式的詳細資訊，請參閱 [Marketplace 中 Azure 受管理的應用程式](managed-application-author-marketplace.md)。
* 若要發行只供您組織中使用者使用之受管理的應用程式，請參閱[建立和發行 Service Catalog 受管理的應用程式](managed-application-publishing.md)。
* 若要使用只供您組織中使用者使用之受管理的應用程式，請參閱[使用 Service Catalog 受管理的應用程式](managed-application-consumption.md)。