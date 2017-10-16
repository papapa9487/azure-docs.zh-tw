---
title: "使用 PowerShell 建立 SQL 資料倉儲 | Microsoft Docs"
description: "使用 PowerShell 建立 SQL 資料倉儲"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: a763f1c600c1a3f37cb565a8eb7db3c3f27dcf75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-sql-data-warehouse-using-powershell"></a>使用 PowerShell 建立 SQL 資料倉儲
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

本文說明如何使用 PowerShell 建立 SQL 資料倉儲。

## <a name="prerequisites"></a>必要條件
若要開始，您需要：

* **Azure 帳戶**︰請瀏覽 [Azure 免費試用][Azure Free Trial]或 [MSDN Azure 點數][MSDN Azure Credits]以建立帳戶。
* **Azure SQL Server**︰如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure SQL Database][Create an Azure SQL database in the Azure Portal] 或[使用 PowerShell 建立 Azure SQL Database][Create an Azure SQL database with PowerShell]。
* **資源群組**︰使用與 Azure SQL Server 相同的資源群組，或參閱 [如何建立資源群組](../azure-resource-manager/resource-group-portal.md)。
* **PowerShell 1.0.3 版或更新版本**：您可以執行 **Get-Module -ListAvailable -Name Azure** 來檢查您的版本。  可透過 [Microsoft Web Platform Installer][Microsoft Web Platform Installer]安裝最新的版本。  如需安裝最新版本的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][How to install and configure Azure PowerShell]。

> [!NOTE]
> 建立 SQL 資料倉儲可能會導致新的可計費服務。  如需價格的詳細資訊，請參閱 [SQL 資料倉儲價格][SQL Data Warehouse pricing]。
>
>

## <a name="create-a-sql-data-warehouse"></a>建立 SQL 資料倉儲
1. 開啟 Windows PowerShell。
2. 執行此 Cmdlet 來登入 Azure 資源管理員。

    ```Powershell
    Login-AzureRmAccount
    ```
3. 選取目前的工作階段要使用的訂用帳戶。

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. 建立資料庫。 這個範例會使用服務目標等級 "DW400" 建立名為 "mynewsqldw" 的資料庫，以部署到 "mywesteuroperesgp1" 資源群組中名為 "sqldwserver1" 的伺服器。

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

必要參數如下：

* **RequestedServiceObjectiveName**：您要求的 [DWU][DWU] 數量。  支援的值為︰DW100、DW200、DW300、DW400、DW500、DW600、DW1000、DW1200、DW1500、DW2000、DW3000 和 DW6000。
* **DatabaseName**：您要建立的 SQL 資料倉儲的名稱。
* **ServerName**：您用來建立的伺服器名稱 (必須是 V12)。
* **ResourceGroupName**：您使用的資源群組。  若要尋找訂用帳戶中可用的資源，請使用 Get-AzureResource。
* **版本**：必須是 "DataWarehouse"，才能建立 SQL 資料倉儲。

選擇性參數如下：

* **CollationName**：未指定定序時的預設值為 SQL_Latin1_General_CP1_CI_AS。  無法變更資料庫的定序。
* **MaxSizeBytes**︰資料庫的預設大小上限為 10 GB。

如需參數選項的詳細資訊，請參閱 [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] 和[建立資料庫 (Azure SQL 資料倉儲)][Create Database (Azure SQL Data Warehouse)]。

## <a name="next-steps"></a>後續步驟
SQL 資料倉儲完成佈建之後，建議您試著[載入範例資料][loading sample data]，或查看如何[開發][develop]、[載入][load]或[移轉][migrate]。

如果您有興趣進一步了解如何以程式設計方式管理 SQL 資料倉儲，請查看我們的文章中有關 [PowerShell Cmdlet 和 REST API][PowerShell cmdlets and REST APIs] 的使用方式。

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
