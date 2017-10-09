---
title: "在 Azure Stack 中提供服務 | Microsoft Docs"
description: "作為雲端操作員，您可以向使用者提供服務。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7a54771d99f2719fcc345496b152a5d3acc02121
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="overview-of-offering-services-in-azure-stack"></a>Azure Stack 中的服務供應項目概觀

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

[Microsoft Azure Stack](azure-stack-poc.md) 是一個混合式雲端平台，可讓您從資料中心提供服務。 作為服務提供者，您可以提供服務給租用戶。 在企業或政府機構中，您可以提供內部部署服務給您的員工。 您可以傳遞的服務包括、但不限於：

- 平台即服務 (PaaS) 服務，例如應用程式服務、行動應用程式、API Apps、API 函式、SQL、MySQL。

您甚至可以結合服務，針對不同的使用者整合和建置複雜的解決方案。

若要將這些服務提供給您的使用者，您必須建立[計劃、產品和配額](azure-stack-plan-offer-quota-overview.md)。 您的使用者接著會訂閱您的產品來使用服務。

## <a name="plan-your-service-offers"></a>規劃服務產品

規劃產品時，請記住下列重點：

**試用產品**：您可以使用試用產品來吸引新使用者，接著升級到其他服務。 若要建立試用產品，請建立小型的[基本方案](azure-stack-plan-offer-quota-overview.md#base-plan)與選用的大附加元件方案。

**容量規劃**：您可能會擔心使用者擷取大量資源，然後阻礙系統的所有使用者使用。 若要協助改善效能，您可以[設定方案與配額](azure-stack-plan-offer-quota-overview.md#plans)來規劃使用方式。

**委派的提供者**：您可將在您的環境中建立產品的能力授與其他人。 例如，如果您是服務提供者，您可以將此能力[委派](azure-stack-delegated-provider.md)給您的轉售商。 或者，如果您是組織，您可以委派給其他部門/分公司。

## <a name="next-steps"></a>後續步驟
[深入瞭解產品、方案、配額和訂用帳戶](azure-stack-plan-offer-quota-overview.md)


