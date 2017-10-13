---
title: "如何將 Web 服務部署到多個區域 | Microsoft Docs"
description: "將新的 Web 服務部署 (複製) 到其他區域的步驟。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 8e616bd6f13c4f97886a3eae225e188afadb2305
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>如何將 Web 服務部署到多個區域
新式 Azure Web 服務可讓您輕鬆地將 Web 服務部署到多個區域，而不需要多個訂用帳戶或工作區。 

價格依照區域而有所不同，因此您必須為您要部署 Web 服務的每個區域定義計費方案。

## <a name="to-create-a-plan-in-another-region"></a>在其他區域中建立方案
1. 登入 [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/)。
2. 按一下 [方案]  功能表選項。
3. 在方案概觀頁面上，按一下 [新增] 。
4. 從 [訂用帳戶]  下拉式清單中，選取新方案所在的訂用帳戶。
5. 從 [區域]  下拉式清單中，選取新方案的區域。 所選區域的「方案選項」會顯示在頁面的 [方案選項]  區段中。
6. 從 [資源群組]  下拉式清單中，選取方案的資源群組。 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。
7. 在 [方案名稱]  中，輸入方案的名稱。
8. 在 [方案選項] 下，按一下新方案的計費層級。
9. 按一下 [建立] 。

## <a name="deploying-the-web-service-to-another-region"></a>將 Web 服務部署到另一個區域
1. 按一下 [Web 服務]  功能表選項。
2. 選取您要部署到新區域的 Web 服務。
3. 按一下 [複製] 。
4. 在 [Web 服務名稱] 中，輸入 Web 服務的新名稱。
5. 在 [Web 服務描述] 中，輸入 Web 服務的描述。
6. 從 [訂用帳戶]  下拉式清單中，選取新 Web 服務所在的訂用帳戶。
7. 從 [資源群組]  下拉式清單中，選取 Web 服務的資源群組。 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。
8. 從 [區域]  下拉式清單中，選取要部署 Web 服務的區域。
9. 從 [儲存體帳戶]  下拉式清單中，選取要儲存 Web 服務的儲存體帳戶。
10. 從 [價格方案]  下拉式清單中，在您在步驟 8 選取的區域中選取方案。
11. 按一下 [複製] 。

