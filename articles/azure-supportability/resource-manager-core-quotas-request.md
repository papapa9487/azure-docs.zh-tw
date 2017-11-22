---
title: "Azure Resource Manager vCPU 配額增加要求 | Microsoft Docs"
description: "Azure Resource Manager vCPU 配額增加要求"
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c22a6dde0067385a1bf8d889cc76178bb44dd0ac
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Resource Manager vCPU 配額增加要求

區域層級和 SKU 系列層級強制執行 Resource Manager vCPU 配額。
您可以在 [Azure 訂用帳戶和服務限制](http://aka.ms/quotalimits)頁面深入了解如何強制執行配額。
若要深入了解 SKU 系列，您可以在[虛擬機器價格](http://aka.ms/pricingcompute)頁面上比較成本和效能。

若要要求增加，請在 Azure 入口網站 [https://portal.azure.com](https://portal.azure.com) 中建立 vCPU 的配額支援案例。

> [!NOTE]
> 了解如何在 Azure 入口網站中[建立支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)

1. 在新的支援要求頁面上，在 [問題類型] 選取 [配額]，在 [配額類型] 選取 [核心]。

    ![配額基本概念刀鋒視窗](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. 在 [部署模型] 選取 [Resource Manager]，並選取位置。

    ![配額問題刀鋒視窗](./media/resource-manager-core-quotas-request/Problem-step.png)

3. 選取需要增加的 SKU 系列。

    ![選取的 SKU 系列](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. 輸入您想要對訂用帳戶採取的新限制。

    ![SKU 新配額要求](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- 若要移除一行，請從 SKU 系列下拉式清單中取消核取 SKU，或按一下捨棄 [x] 圖示。
輸入每個 SKU 系列所需的配額後，請在 [問題步驟] 頁面上按 [下一步] 繼續建立支援要求。
