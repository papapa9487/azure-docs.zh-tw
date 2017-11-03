---
title: "如何解除安裝彈性資料庫工作工具"
description: "了解如何使用 Azure 入口網站或 PowerShell 解除安裝彈性資料庫工作元件。"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: bfc9d820-edbd-4fca-bfbf-1f339cfcc448
ms.service: sql-database
ms.workload: Inactive
ms.custom: scale out apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 5e665ee8cc9efacbd31111dc0458ad6096e457c0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="uninstall-elastic-database-jobs-components"></a>解除安裝彈性資料庫工作元件
**彈性資料庫工作**元件可以使用 Azure 入口網站或 PowerShell 加以解除安裝。

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>使用 Azure 入口網站解除安裝彈性資料庫工作元件
1. 開啟 [Azure 入口網站](https://portal.azure.com/)。
2. 瀏覽至包含 **彈性資料庫工作** 元件的訂用帳戶，也就是彈性資料庫工作元件安裝所在的訂用帳戶。
3. 按一下 [瀏覽]，然後按一下 [資源群組]。
4. 選取名為 "__ElasticDatabaseJob" 的資源群組。
5. 刪除資源群組。

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>使用 PowerShell 解除安裝彈性資料庫作業元件
1. 啟動 Microsoft Azure PowerShell 命令視窗，並且瀏覽至 Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x 資料夾底下的工具子目錄：輸入 **cd tools**。
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools
2. 執行 .\UninstallElasticDatabaseJobs.ps1 PowerShell 指令碼。
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

或者，假設安裝元件時使用預設值，則直接執行下列指令碼：

        $ResourceGroupName = "__ElasticDatabaseJob"
        Switch-AzureMode AzureResourceManager

        $resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
        if(!$resourceGroup)
        {
            Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
            return
        }

        Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
        Remove-AzureResourceGroup -Name $ResourceGroupName -Force
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>後續步驟
若要重新安裝彈性資料庫工作，請參閱 [安裝彈性資料庫工作服務](sql-database-elastic-jobs-service-installation.md)

如需彈性資料庫工作的概觀，請參閱 [彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。

<!--Image references-->


