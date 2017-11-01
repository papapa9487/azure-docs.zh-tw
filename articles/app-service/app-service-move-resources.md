---
title: "將 Azure Web 應用程式移至另一個資源群組 | Microsoft Docs"
description: "描述您可以將 Web Apps 與 App Service 從一個資源群組移至另一個的案例。"
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>將 Azure Web 應用程式移至另一個資源群組
您可以將 Web 應用程式和/或其相關資源移至相同訂用帳戶中的其他資源群組，或移至不同訂用帳戶中的資源群組。 這已經是 Azure 標準資源管理的一部分。 如需詳細資訊，請參閱 [將 Azure 資源移至新的訂用帳戶或資源群組](../azure-resource-manager/resource-group-move-resources.md)。

## <a name="limitations-when-moving-within-the-same-subscription"></a>在相同訂用帳戶內移動時的限制

在_相同訂用帳戶內_移動 Web 應用程式時，您無法移動已上傳的 SSL 憑證。 不過，您可以只將 Web 應用程式移至新的資源群組，而不移動其上傳的 SSL 憑證，而且您應用程式的 SSL 功能仍可正常運作。 

如果您想在移動 Web 應用程式時一併移動 SSL 憑證，請遵循下列步驟：

1.  刪除從 Web 應用程式上傳的憑證。
2.  移動 Web 應用程式。
3.  將憑證上傳至移動的 Web 應用程式。

## <a name="limitations-when-moving-across-subscriptions"></a>在訂用帳戶之間移動時的限制

在_訂用帳戶之間_移動 Web 應用程式時，適用下列限制：

- 目的地資源群組中必須沒有任何已存在的 App Service。 App Service 資源包括：
    - Web Apps
    - App Service 方案
    - 已上傳或已匯的入 SSL 憑證
    - App Service 環境
- 資源群組中的所有 App Service 資源必須一起移動。
- 只能從其最初建立 App Service 資源的資源群組中移動 App Service 資源。 如果 App Service 資源已不存在於其原始的資源群組中，則必須將其移回原始資源群組，然後才可以在訂用帳戶間移動。 
