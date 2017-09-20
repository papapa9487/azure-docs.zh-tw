---
title: "Azure PowerShell 指令碼範例 - 將 Web 應用程式連線至 SQL 資料庫 | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 將 Web 應用程式連線至 SQL 資料庫"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 3396f21a36a1dc4de620416d2d863a291c1f67b9
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017

---

# <a name="connect-a-web-app-to-a-sql-database"></a>將 Web 應用程式連接至 SQL Database

在此案例中，您會學習如何建立 Azure SQL Database 和 Azure Web 應用程式。 然後，您會使用應用程式設定將 SQL Database 連結到 Web 應用程式。

您可以視需要使用 [Azure PowerShell 指南 (英文)](/powershell/azure/overview) 中的指示來安裝 Azure PowerShell，然後執行 `Login-AzureRmAccount` 來建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "將 Web 應用程式連線至 SQL 資料庫")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、Web 應用程式和所有相關資源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | 建立 App Service 方案。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | 建立 Web 應用程式。 |
| [New-AzureRMSQLServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | 建立 SQL Database 伺服器。 |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | 建立 SQL Database 伺服器防火牆規則。 |
| [New-AzureRMSQLDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | 建立資料庫或彈性資料庫。 |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | 修改 Web 應用程式的組態。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../app-service-powershell-samples.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。

