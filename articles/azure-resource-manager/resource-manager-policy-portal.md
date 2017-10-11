---
title: "資源原則的 Azure 入口網站 | Microsoft Docs"
description: "描述如何使用 Azure 入口網站來建立和管理 Resource Manager 原則。 原則可以套用在訂用帳戶或資源群組。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>使用 Azure 入口網站來指派和管理資源原則
Azure 入口網站可讓您將資源原則指派給資源群組和訂用帳戶。 使用者介面可讓您輕鬆地選取您想要指派的原則，並指定該原則的參數值來自訂原則設定。 

若要透過入口網站指派原則，您的訂用帳戶必須已有原則定義。 您的訂用帳戶有數個內建的原則定義，準備好讓您指派給資源群組或訂用帳戶。 當您使用入口網站指派原則時，會看到這些內建的原則，以及您已定義的任何自訂原則。 如需原則簡介及定義自訂原則的方式，請參閱[資源原則概觀](resource-manager-policy.md)。

原則會由所有子資源繼承。 所以，如果原則套用至資源群組，它會適用於該資源群組中的所有資源。 在本文中，**範圍**這個詞彙指的是已指派原則的資源群組或訂用帳戶。 

建立和更新資源 (PUT 和 PATCH 作業) 時，會評估原則。

## <a name="assign-a-policy"></a>指派原則

1. 若要將原則指派給資源群組或訂用帳戶，請選取該資源群組或訂用帳戶。 在設定中，選取 [原則]。

   ![選取原則](./media/resource-manager-policy-portal/select-policies.png)

2. 若要建立此範圍的原則指派，請選取 [新增指派]。

   ![新增指派](./media/resource-manager-policy-portal/add-assignment.png)

3. 選取您想要指派的原則。 即使您尚未將任何原則定義新增至您的訂用帳戶，仍會看到適用於指派的內建原則。 這些內建原則涵蓋許多常見的案例。

   ![選取定義](./media/resource-manager-policy-portal/select-definition.png)

4. 選取原則之後，您會看到原則的描述，以及該原則的所有參數。 例如，下圖顯示的 **Allowed locations** 參數是限制可用位置之原則的必要項目。

   ![顯示參數](./media/resource-manager-policy-portal/show-parameters.png)

5. 透過使用者介面，選取要針對原則參數指定的值 (例如可用於部署的位置)。

   ![選取參數值](./media/resource-manager-policy-portal/select-parameters.png)

6. 提供其他參數的值。 會根據您在啟動原則指派時選取的刀鋒視窗，自動將範圍進行指派。 完成時選取 [確定]  。

   ![定義參數](./media/resource-manager-policy-portal/define-parameters.png)

  您已將原則指派至指定的範圍。

## <a name="view-policy-assignments"></a>檢視原則指派

將原則指派之後，您會在該範圍的原則清單中看見它。 [詳細資料] 索引標籤會顯示原則指派的摘要。

![顯示詳細資料](./media/resource-manager-policy-portal/show-details.png)

[指派規則] 索引標籤會顯示原則定義的 JSON。

![顯示指派規則](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>變更現有的原則指派

若要變更原則，請選取 [編輯指派] 或 [刪除]

![編輯或刪除指派](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>指派自訂的原則

如果您已在訂用帳戶中定義自訂原則，這些原則都可透過入口網站進行指派。 這些原則前面會加上 [自訂]

![自訂原則](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>後續步驟
* 若要深入了解定義原則的 JSON 語法，請參閱[資源原則概觀](resource-manager-policy.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。
* 原則結構描述會發佈於 [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json)。 

