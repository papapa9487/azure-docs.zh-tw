---
title: "Service Fabric CLI 指令碼範例 - 更新叢集上的應用程式"
description: "Service Fabric CLI 指令碼範例 - 以新版本來更新應用程式。 此範例也會以新版本來升級部署的應用程式。"
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: adegeo
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f91ea8c2f8c6e9a6ce38cf72be2c61d9e5218b23
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>將應用程式憑證新增到 Service Fabric 叢集

此範例指令碼會上傳現有應用程式的新版本，然後以新版本來升級部署的應用程式。

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>範例指令碼

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Service Fabric CLI 文件](../service-fabric-cli.md)。

您可以在 [Service Fabric CLI 範例](../samples-cli.md)中找到適用於 Azure Service Fabric 的其他 Service Fabric CLI 範例。

