---
title: "PowerShell 指令碼 - 部署 Azure-SSIS 整合執行階段 | Microsoft Docs"
description: "這個 PowerShell 指令碼會建立可在雲端中執行 SSIS 封裝的 Azure-SSIS 整合執行階段。"
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9be0ed15f5bea06b499c696a63829f7898eb7b74
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell 指令碼 - 部署 Azure-SSIS 整合執行階段

這個範例 PowerShell 指令碼會建立可在 Azure 中執行 SSIS 封裝的 Azure-SSIS 整合執行階段。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>清除部署

執行範例指令碼之後，您可以使用下列命令以移除資源群組及與其相關聯的所有資源：

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：

| 命令 | 注意事項 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | 建立資料處理站。 |
| [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime) | 建立可在雲端中執行 SSIS 封裝的 Azure-SSIS 整合執行階段 |
| [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime) | 啟動 Azure-SSIS 整合執行階段。 |
| [Get-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntime) | 取得 Azure-SSIS 整合執行階段的相關資訊。 |
| [Get-AzureRmDataFactoryV2IntegrationRuntimeStatus](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntimestatus) | 取得 Azure-SSIS 整合執行階段狀態的相關資訊。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Cosmos DB PowerShell 指令碼](../samples-powershell.md)中找到其他 Azure Cosmos DB PowerShell 指令碼範例。
