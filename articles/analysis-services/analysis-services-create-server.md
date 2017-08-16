---
title: "在 Azure 中建立 Analysis Services 伺服器 |Microsoft Docs"
description: "瞭解如何在 Azure 中建立 Analysis Services 伺服器執行個體。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/14/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 8ff39e31d790b9d7040ec0398a42fb104890915f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>在 Azure 入口網站中建立 Azure Analysis Services 伺服器
本文將逐步引導您在 Azure 訂用帳戶中建立 Analysis Services 伺服器資源。

## <a name="before-you-begin"></a>開始之前
若要完成本快速入門，您需要：

* **Azure 訂用帳戶**︰瀏覽 [Azure 免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/)建立帳戶。
* **Azure Active Directory**：您的訂用帳戶必須與 Azure Active Directory 租用戶相關聯。 而且，您必須使用該 Azure Active Directory 中的帳戶來登入 Azure。 不支援 Microsoft 帳戶。 若要深入了解，請參閱[驗證和使用者權限](analysis-services-manage-users.md)。
* **資源群組**：使用現有資源群組，或[建立新的群組](../azure-resource-manager/resource-group-overview.md)。

> [!NOTE]
> 建立伺服器可能會導致新的可計費服務。 若要深入了解，請參閱 [Analysis Services 價格](https://azure.microsoft.com/pricing/details/analysis-services/)。
> 
> 

## <a name="to-create-a-server-in-azure-portal"></a>若要在 Azure 入口網站中建立伺服器
1. 登入 [Azure 入口網站](https://portal.azure.com)。  
2. 按一下 [+ 新增] > [資料 + 分析] > [Analysis Services]。
3. 在 [Analysis Services] 刀鋒視窗中，填寫必要的欄位，然後按 [建立]。
   
    ![建立伺服器](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **伺服器名稱**︰輸入用來參考伺服器的唯一名稱。
   * **訂用帳戶**：選取此伺服器向其收費的訂用帳戶。
   * **資源群組**：這些容器是為了協助您管理 Azure 資源集合而設計。 若要深入了解，請參閱[資源群組](../azure-resource-manager/resource-group-overview.md)。
   * **位置**︰此 Azure 資料中心位置裝載著伺服器。 請選擇最靠近最大使用者群體的位置。
   * **定價層**：選取定價層。 表格式模型最多支援 400 GB。 若要深入了解，請參閱 [Azure Analysis Services 定價](https://azure.microsoft.com/pricing/details/analysis-services/)。
4. 按一下 [建立] 。

建立程序通常不到一分鐘即可完成；往往在數秒內。 如果您選取 [新增到入口網站]，請瀏覽至您的入口網站來查看新的伺服器。 或者，瀏覽至 [更多服務]  >  **Analysis Services** 以查看您的伺服器是否就緒。

 ![儀表板](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>後續步驟
建立您的伺服器後，您可以使用 SSDT 或透過 SSMS，在其中[部署模型](analysis-services-deploy.md)。

如果您部署到伺服器的模型會連接到內部部署資料來源，您就必須在您網路中的電腦上安裝[內部部署資料閘道](analysis-services-gateway.md)。


