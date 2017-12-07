---
title: "Azure PowerShell 指令碼範例 - 更新 RDP 使用者名稱和密碼 | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 針對特定節點類型的所有 Service Fabric 叢集節點，更新 RDP 使用者名稱和密碼。"
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
ms.date: 11/17/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3b97cee11c9a85cbd60a05bdbdcd010a0f0a106f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>為叢集中的 VM 更新管理員使用者名稱和密碼

Service Fabric 叢集中的每個節點類型都是一個虛擬機器擴展集。 此指令碼範例會針對特定節點類型的叢集虛擬機器，更新管理員使用者名稱和密碼。  管理員密碼是不可修改的擴展集屬性，因此請在擴展集內新增 VMAccessAgent 擴充功能。  使用者名稱和密碼的變更會套用至擴展集內的所有節點。 視需要自訂參數。

您可以視需要使用 [Azure PowerShell 指南 (英文)](/powershell/azure/overview) 中的指示來安裝 Azure PowerShell。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | 取得叢集節點類型 (虛擬機器擴展集) 的屬性。   |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension)| 在虛擬機器擴展集內新增擴充功能。|
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)|將虛擬機器擴展集的狀態更新為本機 VMSS 物件的狀態。|

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../service-fabric-powershell-samples.md)中找到適用於 Azure Service Fabric 的其他 Azure PowerShell 範例。
