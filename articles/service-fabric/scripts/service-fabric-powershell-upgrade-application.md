---
title: "Azure PowerShell 指令碼範例 - 升級 Service Fabric 應用程式 | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 升級 Service Fabric 應用程式。"
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
ms.date: 08/23/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 43c1c0d38d12a36c39a3962a3399d9c05937e8b1
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---

# <a name="upgrade-a-service-fabric-application"></a>升級 Service Fabric 應用程式

這個範例指令碼會將執行中的 Service Fabric 應用程式執行個體升級到 1.3.0 版。 指令碼會將新的應用程式套件複製到叢集映像存放區，註冊應用程式類型，開始進行監視的升級，並會持續檢查升級狀態，直到升級完成或復原為止。 視需要自訂參數。 

如有需要，可隨同 [Service Fabric SDK](../service-fabric-get-started.md) 一起安裝 Service Fabric PowerShell 模組。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "升級應用程式")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | 取得 Service Fabric 叢集或特定應用程式中的所有應用程式。  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | 取得 Service Fabric 應用程式升級的狀態。 |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | 取得已在 Service Fabric 叢集上註冊的 Service Fabric 應用程式類型。 |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | 取消註冊 Service Fabric 應用程式類型。  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | 將 Service Fabric 應用程式套件複製到映像存放區。  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | 註冊 Service Fabric 應用程式類型。 |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | 將 Service Fabric 應用程式升級至指定的應用程式類型版本。 |


## <a name="next-steps"></a>後續步驟

如需有關 Service Fabric PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/service-fabric/?view=azureservicefabricps)。

您可以在 [PowerShell 範例](../service-fabric-powershell-samples.md)中找到適用於 Azure Service Fabric 的其他 Azure PowerShell 範例。

