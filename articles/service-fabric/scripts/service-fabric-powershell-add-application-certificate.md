---
title: "Azure PowerShell 指令碼範例 - 將應用程式憑證新增到叢集 | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 將應用程式憑證新增到 Service Fabric 叢集。"
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
ms.openlocfilehash: 3212944a8bd882ced86e841944ac60bd1845e3d1
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---

# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>將應用程式憑證新增到 Service Fabric 叢集

這個範例指令碼在指定的 Azure Key Vault 中建立自我簽署的憑證，並將它安裝到 Service Fabric 叢集的所有節點。 憑證也會下載至本機資料夾。 下載的憑證名稱會與金鑰保存庫中的憑證名稱相同。 視需要自訂參數。

您可以視需要使用 [Azure PowerShell 指南 (英文)](/powershell/azure/overview) 中的指示來安裝 Azure PowerShell，然後執行 `Login-AzureRmAccount` 來建立與 Azure 的連線。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "將應用程式憑證新增到叢集")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) | 將新的應用程式憑證新增到組成叢集的虛擬機器擴展集。  |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../service-fabric-powershell-samples.md)中找到適用於 Azure Service Fabric 的其他 Azure PowerShell 範例。

