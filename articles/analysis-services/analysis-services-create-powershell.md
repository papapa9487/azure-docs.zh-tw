---
title: "使用 PowerShell 建立 Azure Analysis Services 伺服器 | Microsoft Docs"
description: "了解如何使用 PowerShell 來建立 Azure Analysis Services 伺服器"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2017
ms.author: owend
ms.custom: mvc
ms.openlocfilehash: cb42fd3ed51364cf478848cc51ebbb2f175e96d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>使用 PowerShell 來建立 Azure Analysis Services 伺服器

本快速入門說明如何從命令列使用 PowerShell 在 Azure 訂用帳戶的 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)中建立 Azure Analysis Services 伺服器。

此工作需要 Azure PowerShell 模組 4.0 版或更新版本。 若要尋找版本，請執行 ` Get-Module -ListAvailable AzureRM`。 若要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 

> [!NOTE]
> 建立伺服器可能會導致新的可計費服務。 若要深入了解，請參閱 [Analysis Services 價格](https://azure.microsoft.com/pricing/details/analysis-services/)。

## <a name="prerequisites"></a>必要條件
若要完成本快速入門，您需要：

* **Azure 訂用帳戶**︰瀏覽 [Azure 免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/)建立帳戶。
* **Azure Active Directory**：您的訂用帳戶必須與 Azure Active Directory 租用戶相關聯，而且您必須在該目錄中有一個帳戶。 若要深入了解，請參閱[驗證和使用者權限](analysis-services-manage-users.md)。

## <a name="import-azurermanalysisservices-module"></a>Import AzureRm.AnalysisServices 模組
若要在您的訂用帳戶中建立伺服器，您可使用 [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) 元件模組。 將 AzureRm.AnalysisServices 模組載入 PowerShell 工作階段中。

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>登入 Azure

使用 [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) 命令登入您的 Azure 訂用帳戶。 遵循螢幕上的指示進行。

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>建立資源群組
 
[Azure 資源群組](../azure-resource-manager/resource-group-overview.md)是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 當您建立伺服器時，必須指定您訂用帳戶中的資源群組。 如果您還沒有資源群組，可以使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令建立一個新的資源群組。 下列範例會在美國西部區域中建立一個名為 `myResourceGroup` 的資源群組。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>建立伺服器

使用 [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) 命令建立新的伺服器。 下列範例會在美國西部地區 (D1 層) 的 myResourceGroup 中建立名為 myServer 的伺服器，並將 philipc@adventureworks.com 指定為伺服器管理員。

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>清除資源

您也可以使用 [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) 命令，從您的訂用帳戶中移除伺服器。 如果您繼續進行本集合中的其他快速入門和教學課程，請勿移除您的伺服器。 下列範例會移除在前一個步驟中建立的伺服器。


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟
[使用 PowerShell 管理 Azure Analysis Services](analysis-services-powershell.md)   
[從 SSDT 部署模型](analysis-services-deploy.md)   
[在 Azure 入口網站中建立模型](analysis-services-create-model-portal.md)