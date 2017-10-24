---
title: "使用 PowerShell 管理 Azure Analysis Services | Microsoft Docs"
description: "使用 PowerShell 的 Azure Analysis Services 管理。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: owend
ms.openlocfilehash: 385dd7798893447817dfc2c3a3538a13409ab6e3
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="manage-azure-analysis-services-with-powershell"></a>使用 PowerShell 管理 Azure Analysis Services

本文說明用來執行 Azure Analysis Services 伺服器和資料庫管理工作的 PowerShell Cmdlet。 

伺服器管理工作，例如建立或刪除伺服器、暫停或繼續伺服器作業，或是變更服務層級 (層級)，會使用 Azure Resource Manager (AzureRM) Cmdlet。 其他資料庫管理工作 (例如新增或移除角色成員、處理或資料分割) 會使用與 SQL Server Analysis Services 相同之 SqlServer 模組內含的 Cmdlet。

## <a name="permissions"></a>權限
大部分的 PowerShell 工作需要您在您管理的 Analysis Services 伺服器上具備系統管理員權限。 排定的 PowerShell 工作都是自動的作業。 執行排程器的帳戶必須具有 Analysis Services 伺服器上的管理員權限。 

針對使用 AzureRm Cmdlet 的伺服器作業，您的帳戶或執行排程器的帳戶也必須屬於 [Azure 角色型存取控制 (RBAC)](../active-directory/role-based-access-control-what-is.md) 中之資源的 Owner 角色。 

## <a name="server-operations"></a>伺服器作業 
Azure Analysis Services Cmdlet 包含在 [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) 元件模組中。 若要安裝 AzureRM Cmdlet 模組，請參閱 PowerShell 資源庫中的 [Azure Resource Manager Cmdlet (英文)](/powershell/azure/overview)。

|Cmdlet|說明| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azurerm.analysisservices/add-azureanalysisservicesaccount)|新增已驗證的帳戶以用於 Azure Analysis Services 伺服器 Cmdlet 要求。| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|取得伺服器執行個體的詳細資料。|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|建立伺服器執行個體。|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|移除伺服器執行個體。|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|在目前登入的環境中重新啟動 Analysis Services 伺服器的執行個體；在 Add-AzureAnalysisServicesAccount 命令中指定。|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|繼續伺服器執行個體。|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|暫停伺服器執行個體。| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|修改伺服器執行個體。|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|測試伺服器執行個體的存在。| 

## <a name="database-operations"></a>資料庫作業

Azure Analysis Services 資料庫作業會使用與 SQL Server Analysis Services 相同的 [SqlServer](https://www.powershellgallery.com/packages/SqlServer) 模組。 不過，Azure Analysis Services 並不支援所有的 Cmdlet。 

SqlServer 模組提供特定工作的資料庫管理 Cmdlet，以及接受「表格式模型指令碼語言」(TMSL) 查詢或指令碼的一般用途 Invoke-ASCmd Cmdlet。 Azure Analysis Services 支援 SqlServer 模組中的下列 Cmdlet。

  
|Cmdlet|說明|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|將成員新增到資料庫角色。| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|備份 Analysis Services 資料庫。|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|從資料庫角色移除成員。|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|執行 TMSL 指令碼。|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|處理資料庫。|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|處理資料分割。| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|處理資料表。|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|合併資料分割。|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|還原 Analysis Services 資料庫。| 
  

## <a name="related-information"></a>相關資訊

* [下載 SQL Server PowerShell 模組](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [下載 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [PowerShell 資源庫中的 SqlServer 模組](https://www.powershellgallery.com/packages/SqlServer)    
* [相容性層級 1200 和更高層級的表格式模型程式設計](https://msdn.microsoft.com/library/mt712541.aspx)
