---
title: "PowerShell 指令碼：使用 Azure Data Factory 在雲端中複製資料 | Microsoft Docs"
description: "此 PowerShell 指令碼會將資料從 Azure Blob 儲存體中的一個位置複製到相同 Blob 儲存體中的其他位置。"
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
ms.openlocfilehash: ad97a40c5c12aa5a8f69a8d56750bd6f39725be0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>使用 PowerShell 建立資料處理站管線，以在雲端中複製資料

這個範例 PowerShell 指令碼會建立 Azure Cosmos DB API 帳戶。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

> [!IMPORTANT]
> 這個指令碼會建立 JSON 檔案，該檔案定義硬碟上 c:\ 資料夾中的 Data Factory 實體 (已連結的服務、資料集和管線)。

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>清除部署

執行範例指令碼之後，您可以使用下列命令以移除資源群組及與其相關聯的所有資源：

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```
若要從資源群組移除資料處理站，請執行下列命令： 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<data factory name>" -ResourceGroupName "<resource group name>"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| 命令 | 注意事項 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | 建立資料處理站。 |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | 在資料處理站中建立已連結的服務。 已連結的服務會將資料存放區或計算連結到資料處理站。 |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | 在資料處理站中建立資料集。 資料集代表管線中活動的輸入/輸出。 | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | 在資料處理站中建立管線。 管線包含一或多個執行特定作業的活動。 在此管線中，複製活動會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | 建立管線的執行。 也就是說，執行管線。 |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | 在管線中取得活動的執行 (活動執行) 的相關詳細資料。 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Cosmos DB PowerShell 指令碼](../samples-powershell.md)中找到其他 Azure Cosmos DB PowerShell 指令碼範例。