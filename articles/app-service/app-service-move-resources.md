---
title: "將 Web App 資源移動到另一個資源群組"
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
ms.date: 12/21/2016
ms.author: zarizvi
ms.openlocfilehash: 1b5059dc052005b6079f70ecf6771a3771df8d87
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="supported-move-configurations"></a>支援的移動組態
您可以使用 [Resource Manager 移動資源 API](../azure-resource-manager/resource-group-move-resources.md) 來移動 Azure Web 應用程式資源。

Azure Web Apps 目前支援下列移動案例：

* 將整個資源群組內容 (Web 應用程式、App Service 方案及憑證) 移到另一個資源群組。 
   > [!Note]
   > 在此案例中，目的地資源群組不能包含任何 Microsoft.Web 資源。

* 將個別 Web 應用程式移到不同的資源群組，但同時仍將它們裝載在其目前的 App Service 方案中 (App Service 方案會保留在舊的資源群組中)。


