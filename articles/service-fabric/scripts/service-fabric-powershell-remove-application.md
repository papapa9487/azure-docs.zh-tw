---
title: "Azure PowerShell 指令碼範例 - 從叢集移除應用程式 | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 從 Service Fabric 叢集移除應用程式。"
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 951c9a014f587ce53450dd705fc478da625b1e6b
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>從 Service Fabric 叢集移除應用程式

這個範例指令碼刪除執行中的 Service Fabric 應用程式執行個體、從叢集取消註冊應用程式類型和版本，並刪除叢集映像存放區中的應用程式封裝。  刪除應用程式執行個體也會刪除應用程式相關聯的所有執行中服務執行個體。 視需要自訂參數。 

如有需要，可隨同 [Service Fabric SDK](../service-fabric-get-started.md) 一起安裝 Service Fabric PowerShell 模組。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "從叢集移除應用程式")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | 從叢集移除執行中的 Service Fabric 應用程式執行個體。  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | 從叢集取消註冊 Service Fabric 應用程式類型和版本。 |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | 從映像存放區移除 Service Fabric 應用程式封裝。|

## <a name="next-steps"></a>後續步驟

如需有關 Service Fabric PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/service-fabric/?view=azureservicefabricps)。

您可以在 [PowerShell 範例](../service-fabric-powershell-samples.md)中找到適用於 Azure Service Fabric 的其他 Azure PowerShell 範例。

